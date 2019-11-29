---
title: Управление Azure Stack с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как развертывать и администрировать ресурсы в Azure Stack с помощью кроссплатформенного интерфейса командной строки (CLI).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: a5a6cf3ef5c2c03992647c207422eb266f171ac4
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465485"
---
# <a name="manage-and-deploy-resources-to-azure-stack-with-azure-cli"></a>Развертывание и администрирование ресурсов в Azure Stack с помощью Azure CLI

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Следуйте приведенным здесь инструкциям, чтобы настроить интерфейс командной строки Azure (CLI) для управления ресурсами Пакета средств разработки Azure Stack (ASDK) на клиентских платформах Linux, Mac и Windows.

## <a name="prepare-for-azure-cli"></a>Подготовка к использованию Azure CLI

Если вы используете Azure CLI, вам понадобится корневой сертификат ЦС для Azure Stack на компьютере разработки. Этот сертификат используется для управления ресурсами с помощью CLI.

 - **Корневой сертификат ЦС Azure Stack** требуется, если CLI используется на рабочей станции без ASDK.  

 - **Конечная точка псевдонимов виртуальных машин** предоставляет псевдоним, например UbuntuLTS или Win2012Datacenter. Этот псевдоним ссылается на издателя образа, предложение, номер SKU и версию как на один параметр при развертывании виртуальных машин.  

В разделах ниже описано, как получить эти значения.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Экспорт корневого сертификата ЦС Azure Stack

При использовании интегрированной системы вам не нужно экспортировать корневой сертификат ЦС. При использовании ASDK экспортируйте корневой сертификат ЦС в ASDK.

Чтобы экспортировать корневой сертификат ASDK в формате PEM:

1. Получите имя корневого сертификата Azure Stack:
    - Войдите на портал администратора или пользователя Azure Stack.
    - Щелкните **Безопасность** рядом с адресной строкой.
    - Во всплывающем окне щелкните **Действительный**.
    - В диалоговом окне "Сертификат" щелкните вкладку **Путь сертификации**.
    - Запишите имя корневого сертификата Azure Stack.

    ![Корневой сертификат Azure Stack](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Создайте виртуальную машину Windows в Azure Stack](azure-stack-quick-windows-portal.md).

3. Войдите на виртуальную машину, откройте командную строку PowerShell с повышенными правами и выполните следующий скрипт:

    ```powershell  
      $label = "<the name of your azure stack root cert from Step 1>"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

4. Скопируйте сертификат на локальный компьютер.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Настройка конечной точки псевдонимов для виртуальных машин

Вы можете настроить общедоступную конечную точку с файлом псевдонимов виртуальных машин. В файле псевдонимов виртуальных машин хранится общее имя образа в JSON-формате. Имя будет использоваться при развертывании виртуальной машины в качестве параметра Azure CLI.

1. Когда вы публикуете пользовательский образ, запишите сведения об издателе, предложении, номере SKU и версии, которые указываете во время публикации. Если используется образ из Marketplace, вы можете просмотреть эти сведения с помощью командлета ```Get-AzureVMImage```.  

2. Скачайте [образец файла](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) с сайта GitHub.

3. Создайте учетную запись хранения в Azure Stack. Затем создайте контейнер BLOB-объектов. Установите для политики доступа значение public.  

4. Отправьте JSON-файл в только что созданный контейнер. После этого просмотрите URL-адрес большого двоичного объекта. Выберите имя большого двоичного объекта, затем из панели свойств большого двоичного объекта — URL-адрес.

### <a name="install-or-upgrade-cli"></a>Установка или обновление CLI

Войдите на рабочую станцию разработки и установите CLI. Для работы с Azure Stack требуется Azure CLI версии 2.0 или выше. Для последней версии профилей API требуется текущая версия CLI. См. сведения об [установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

1. Чтобы проверить успешность установки, откройте окно терминала или командной строки и выполните следующую команду:

    ```shell
    az --version
    ```

    Отобразятся номера версий Azure CLI и зависимых библиотек, установленных на этом компьютере.

    ![Использование Azure CLI в расположении Python в Azure Stack](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Запишите расположение Python в интерфейсе командной строки. Если вы используете пакет ASDK, это расположение потребуется для добавления сертификата.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

В этом разделе объясняется, как настроить CLI, если вы используете Azure AD в качестве службы управления удостоверениями и работаете с CLI на компьютере с Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно обеспечить доверие корневому сертификату ЦС на удаленном компьютере. Этого не нужно делать при использовании интегрированных систем.

Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его в существующее хранилище сертификатов Python для версии Python, установленной вместе с Azure CLI. Вы можете выполнять собственный экземпляр Python. Azure CLI содержит свою версию Python.

1. Найдите хранилище сертификатов на своем компьютере.  Для этого выполните команду `az --version`.

2. Перейдите к папке, содержащей ваше приложение Python с поддержкой CLI. Нужно запустить именно эту версию Python. Если вы настроили для Python системную переменную PATH, при выполнении Python запустится ваша собственная версия Python. Вместо этого нужно запустить версию, поддерживаемую CLI, и добавить свой сертификат в эту версию. Например, ваше приложение Python с поддержкой интерфейса командной строки может располагаться здесь: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Используйте следующие команды:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение сертификата. Например, `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Конкретный путь будет зависеть от операционной системы и установки CLI.

2. Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

1. Зарегистрируйте среду Azure Stack, выполнив команду `az cloud register`.

2. Зарегистрируйте среду. При выполнении команды `az cloud register` используйте следующие параметры:

    | Значение | Пример | ОПИСАНИЕ |
    | --- | --- | --- |
    | Имя среды | AzureStackUser | Используйте `AzureStackUser` для среды пользователя. Если вы оператор, укажите `AzureStackAdmin`. |
    | Конечная точка Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** в ASDK имеет следующее значение: `https://management.local.azurestack.external/`. **ResourceManagerUrl** в интегрированных системах: `https://management.<region>.<fqdn>/`. Если у вас есть вопрос о конечной точке интегрированной системы, обратитесь к оператору облака. |
    | конечную точку службы хранилища; | local.azurestack.external | `local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Суффикс хранилища ключей | .vault.local.azurestack.external | `.vault.local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Конечная точка документа с псевдонимами образов виртуальной машины | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI документа, который содержит псевдонимы образов виртуальной машины. См. сведения о [настройке конечной точки псевдонимов виртуальных машин](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Следующие команды позволяют выбрать активную среду:

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Если вы используете версию Azure Stack до 1808, вам потребуется профиль версии API **2017-03-09-profile** вместо профиля версии **2019-03-01-hybrid**. Вам также потребуется последняя версия Azure CLI.
 
1. Войдите в среду Azure Stack с помощью команды `az login`. Войдите в среду Azure Stack от имени пользователя или [субъекта-службы](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Вход от имени *пользователя*. 

     Вы можете указать имя пользователя и пароль непосредственно в команде `az login` или выполнить аутентификацию в браузере. Если для вашей учетной записи включена многофакторная аутентификация, возможным будет только второй вариант.

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Если в учетной записи пользователя используется многофакторная проверка подлинности, выполните команду `az login` без параметра `-u`. При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.

   - Войдите в систему как *субъект-служба*. 
    
     Для входа от имени субъекта-службы следует заранее [создать субъект-службу с помощью портала Azure](azure-stack-create-service-principals.md) или CLI, а также назначить ему роль. После этого выполните такую команду для входа:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
az group create -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![Выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

В этом разделе объясняется, как настроить CLI, если вы используете службы федерации Active Directory (AD FS) в качестве службы управления удостоверениями и работаете с CLI на компьютере с Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно обеспечить доверие корневому сертификату ЦС на удаленном компьютере. Этого не нужно делать при использовании интегрированных систем.

1. Найдите расположение сертификата на своем компьютере. Это расположение зависит от того, куда вы установили Python. Откройте командную строку или строку PowerShell с повышенными правами и введите следующую команду:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение сертификата. Например, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Конкретный путь зависит от операционной системы и установленной версии Python.

2. Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

1. Зарегистрируйте среду Azure Stack, выполнив команду `az cloud register`.

2. Зарегистрируйте среду. При выполнении команды `az cloud register` используйте следующие параметры:

    | Значение | Пример | ОПИСАНИЕ |
    | --- | --- | --- |
    | Имя среды | AzureStackUser | Используйте `AzureStackUser` для среды пользователя. Если вы оператор, укажите `AzureStackAdmin`. |
    | Конечная точка Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** в ASDK имеет следующее значение: `https://management.local.azurestack.external/`. **ResourceManagerUrl** в интегрированных системах: `https://management.<region>.<fqdn>/`. Если у вас есть вопрос о конечной точке интегрированной системы, обратитесь к оператору облака. |
    | конечную точку службы хранилища; | local.azurestack.external | `local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Суффикс хранилища ключей | .vault.local.azurestack.external | `.vault.local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Конечная точка документа с псевдонимами образов виртуальной машины | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI документа, который содержит псевдонимы образов виртуальной машины. См. сведения о [настройке конечной точки псевдонимов виртуальных машин](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Следующие команды позволяют выбрать активную среду:

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Если вы используете версию Azure Stack до 1808, вам потребуется профиль версии API **2017-03-09-profile** вместо профиля версии **2019-03-01-hybrid**. Вам также потребуется последняя версия Azure CLI.

1. Войдите в среду Azure Stack с помощью команды `az login`. Вы можете войти в среду Azure Stack от имени пользователя или [субъекта-службы](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Вход от имени *пользователя*.

     Вы можете указать имя пользователя и пароль непосредственно в команде `az login` или выполнить аутентификацию в браузере. Если для вашей учетной записи включена многофакторная аутентификация, возможным будет только второй вариант.

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Если в учетной записи пользователя используется многофакторная проверка подлинности, выполните команду `az login` без параметра `-u`. При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.

   - Войдите в систему как *субъект-служба*. 
    
     Подготовьте PEM-файл для использования для входа субъект-службы.

     На клиентском компьютере, где был создан субъект, экспортируйте сертификат субъект-службы как PFX-файл с закрытым ключом, расположенным здесь: `cert:\CurrentUser\My`. Имя этого сертификата совпадает с именем субъекта.

     Преобразуйте PFX в PEM (используйте служебную программу OpenSSL).

     Вход в интерфейс командной строки:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
az group create -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![Выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

В этом разделе объясняется, как настроить CLI, если вы используете Azure AD в качестве службы управления удостоверениями и работаете с CLI на компьютере с Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно обеспечить доверие корневому сертификату ЦС на удаленном компьютере. Этого не нужно делать при использовании интегрированных систем.

Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

1. Найдите расположение сертификата на своем компьютере. Это расположение зависит от того, куда вы установили Python. Вам потребуется установить pip и модуль certifi. Выполните следующие команды Python из командной строки Bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение сертификата. Например, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Конкретный путь зависит от операционной системы и установленной версии Python.

2. Запустите следующую команду bash, указав путь к вашему сертификату.

   - Для удаленного компьютера Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Для компьютера Linux в среде Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Для подключения к Azure Stack выполните следующие действия:

1. Зарегистрируйте среду Azure Stack, выполнив команду `az cloud register`. В некоторых сценариях прямое исходящее подключение к Интернету маршрутизируется через прокси-сервер или брандмауэр, который принудительно использует перехват SSL. В этих случаях команда `az cloud register` может выдать ошибку, например "Unable to get endpoints from the cloud." (Не удалось получить конечные точки из облака). Чтобы избежать этой ошибки, можно задать следующие переменные среды:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Зарегистрируйте среду. При выполнении команды `az cloud register` используйте следующие параметры:

    | Значение | Пример | ОПИСАНИЕ |
    | --- | --- | --- |
    | Имя среды | AzureStackUser | Используйте `AzureStackUser` для среды пользователя. Если вы оператор, укажите `AzureStackAdmin`. |
    | Конечная точка Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** в ASDK имеет следующее значение: `https://management.local.azurestack.external/`. **ResourceManagerUrl** в интегрированных системах: `https://management.<region>.<fqdn>/`. Если у вас есть вопрос о конечной точке интегрированной системы, обратитесь к оператору облака. |
    | конечную точку службы хранилища; | local.azurestack.external | `local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Суффикс хранилища ключей | .vault.local.azurestack.external | `.vault.local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Конечная точка документа с псевдонимами образов виртуальной машины | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI документа, который содержит псевдонимы образов виртуальной машины. См. сведения о [настройке конечной точки псевдонимов виртуальных машин](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Настройте активную среду. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Если вы используете версию Azure Stack до 1808, вам потребуется профиль версии API **2017-03-09-profile** вместо профиля версии **2019-03-01-hybrid**. Вам также потребуется последняя версия Azure CLI.

5. Войдите в среду Azure Stack с помощью команды `az login`. Вы можете войти в среду Azure Stack от имени пользователя или [субъекта-службы](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Вход от имени *пользователя*.

     Вы можете указать имя пользователя и пароль непосредственно в команде `az login` или выполнить аутентификацию в браузере. Если для вашей учетной записи включена многофакторная аутентификация, возможным будет только второй вариант.

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Если в учетной записи пользователя используется многофакторная проверка подлинности, команду `az login` можно выполнять без параметра `-u`. При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.
   
   * Вход с помощью *субъекта-службы*
    
     Для входа от имени субъекта-службы следует заранее [создать субъект-службу с помощью портала Azure](azure-stack-create-service-principals.md) или CLI, а также назначить ему роль. После этого выполните такую команду для входа:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
    az group create -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![Выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

В этом разделе объясняется, как настроить CLI, если вы используете службы федерации Active Directory (AD FS) в качестве службы управления и работаете с CLI на компьютере с Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно обеспечить доверие корневому сертификату ЦС на удаленном компьютере. Этого не нужно делать при использовании интегрированных систем.

Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

1. Найдите расположение сертификата на своем компьютере. Это расположение зависит от того, куда вы установили Python. Вам потребуется установить pip и модуль certifi. Выполните следующие команды Python из командной строки Bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение сертификата. Например, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Конкретный путь зависит от операционной системы и установленной версии Python.

2. Запустите следующую команду bash, указав путь к вашему сертификату.

   - Для удаленного компьютера Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Для компьютера Linux в среде Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Для подключения к Azure Stack выполните следующие действия:

1. Зарегистрируйте среду Azure Stack, выполнив команду `az cloud register`. В некоторых сценариях прямое исходящее подключение к Интернету маршрутизируется через прокси-сервер или брандмауэр, который принудительно использует перехват SSL. В этих случаях команда `az cloud register` может выдать ошибку, например "Unable to get endpoints from the cloud." (Не удалось получить конечные точки из облака). Чтобы избежать этой ошибки, можно задать следующие переменные среды:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Зарегистрируйте среду. При выполнении команды `az cloud register` используйте следующие параметры.

    | Значение | Пример | ОПИСАНИЕ |
    | --- | --- | --- |
    | Имя среды | AzureStackUser | Используйте `AzureStackUser` для среды пользователя. Если вы оператор, укажите `AzureStackAdmin`. |
    | Конечная точка Resource Manager | https://management.local.azurestack.external | **ResourceManagerUrl** в ASDK имеет следующее значение: `https://management.local.azurestack.external/`. **ResourceManagerUrl** в интегрированных системах: `https://management.<region>.<fqdn>/`. Если у вас есть вопрос о конечной точке интегрированной системы, обратитесь к оператору облака. |
    | конечную точку службы хранилища; | local.azurestack.external | `local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Суффикс хранилища ключей | .vault.local.azurestack.external | `.vault.local.azurestack.external` используется для ASDK. Для интегрированной системы нужно использовать соответствующую конечную точку.  |
    | Конечная точка документа с псевдонимами образов виртуальной машины | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI документа, который содержит псевдонимы образов виртуальной машины. См. сведения о [настройке конечной точки псевдонимов виртуальных машин](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Настройте активную среду. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Если вы используете версию Azure Stack до 1808, вам потребуется профиль версии API **2017-03-09-profile** вместо профиля версии **2019-03-01-hybrid**. Вам также потребуется последняя версия Azure CLI.

5. Войдите в среду Azure Stack с помощью команды `az login`. Вы можете войти в среду Azure Stack от имени пользователя или [субъекта-службы](/azure/active-directory/develop/app-objects-and-service-principals). 

6. Войдите в систему: 

   *  Как **пользователь** с помощью веб-браузера с кодом устройства:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.

   * Как субъект-служба:
        
     Подготовьте PEM-файл для использования для входа субъект-службы.

      * На клиентском компьютере, где был создан субъект, экспортируйте сертификат субъект-службы как PFX-файл с закрытым ключом, расположенным здесь: `cert:\CurrentUser\My`. Имя этого сертификата совпадает с именем субъекта.
  
      * Преобразуйте PFX в PEM (используйте служебную программу OpenSSL).

     Вход в интерфейс командной строки:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
  az group create -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![Выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Известные проблемы

При использовании интерфейса командной строки в Azure Stack существуют следующие известные проблемы.

 - Интерактивный режим CLI. Например, команда `az interactive` еще не поддерживается в Azure Stack.
 - Чтобы получить список образов виртуальных машин, доступных в Azure Stack, используйте команду `az vm image list --all` вместо `az vm image list`. Указание параметра `--all` гарантирует, что ответ возвращает только образы, доступные в среде Azure Stack.
 - Azure Stack может не поддерживать псевдонимы образов виртуальных машин, доступные в Azure. При использовании образов виртуальных машин вместо псевдонима образа необходимо указать полное имя URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0). Этот URN должен соответствовать спецификации образа, полученной из команды `az vm images list`.

## <a name="next-steps"></a>Дополнительная информация

- [Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)
- [Включение Azure CLI для пользователей Azure Stack (оператор)](../operator/azure-stack-cli-admin.md)
- [Управление разрешениями пользователей](azure-stack-manage-permissions.md) 
