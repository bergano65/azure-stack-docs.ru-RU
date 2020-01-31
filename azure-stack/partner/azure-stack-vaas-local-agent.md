---
title: Развертывание локального агента
description: Разверните локальный агент в рамках проверки как услуги Azure Stack.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a84f2495b0e2195ac143dc8fd9ac4ea4668392b4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884086"
---
# <a name="deploy-the-local-agent"></a>Развертывание локального агента

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Узнайте, как использовать локальный агент модели "проверка как услуга" (VaaS) для запуска проверочных тестов. Локальный агент необходимо развернуть перед запуском проверочных тестов.

> [!Note]  
> Убедитесь в том, что компьютер, на котором выполняется локальный агент, не потеряет исходящий доступ к Интернету. Этот компьютер должен быть доступен только для пользователей, которые авторизованы для использования VaaS от имени клиента.

Чтобы развернуть локальный агент, выполните приведенные ниже действия.

1. Скачайте и установите локальный агент.
2. Проверьте работоспособность перед запуском тестов.
3. Запустите локальный агент.

## <a name="download-and-start-the-local-agent"></a>Скачивание и запуск локального агента

Скачайте агент на компьютер в центре обработки данных, который соответствует требованиям и имеет доступ ко всем конечным точкам Azure Stack. Компьютер не должен быть частью системы Azure Stack или размещаться в облаке Azure Stack.

### <a name="machine-prerequisites"></a>Предварительные требования к компьютерам

Убедитесь, что ваш компьютер соответствует следующим условиям:

- доступ ко всем конечным точкам Azure Stack;
- установка .NET 4.6 и PowerShell 5.0;
- не менее 8 ГБ ОЗУ;
- минимум 8-ядерные процессоры;
- не менее 200 ГБ дискового пространства;
- постоянное сетевое подключение к Интернету.

### <a name="download-and-install-the-local-agent"></a>Скачивание и установка локального агента

1. Откройте Windows PowerShell в командной строке с повышенными привилегиями на компьютере, который будет использоваться для выполнения тестов.
2. Выполните следующую команду, чтобы скачать и установить зависимости локального агента и скопировать образы (VHD операционной системы) из общедоступного репозитория (PIR) в среду Azure Stack.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> Командлет Install-VaaSPrerequisites позволяет скачать большие файлы образов виртуальных машин. Если скорость передачи по сети медленная, вы можете скачать файлы на локальный файловый сервер и вручную добавить образы виртуальных машин в тестовую среду. Дополнительные сведения см. в разделе о [работе при медленном сетевом подключении](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

**Параметры**

| Параметр | Описание |
| --- | --- |
| AadServiceAdminUser | Глобальный администратор для клиента Azure AD. Например, это может быть vaasadmin@contoso.onmicrosoft.com. |
| AadServiceAdminPassword | Пароль для пользователя с правами администратора. |
| CloudAdminUserName | Администратор облака, который имеет права на запуск разрешенных команд в привилегированной конечной точке и доступ к ним. Например, это может быть AzusreStack\CloudAdmin. Дополнительные сведения см. в [этом разделе](azure-stack-vaas-parameters.md) . |
| CloudAdminPassword | Пароль к учетной записи администратора облака.|

![Скачивание необходимых компонентов](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>Проверка работоспособности перед запуском тестов

В рамках тестов выполняются удаленные операции. Компьютеру, на котором выполняются тесты, необходим доступ к конечным точкам Azure Stack. В противном случае тесты не будут работать. При использовании VaaS на локальном агенте нужен компьютер, на котором будет выполняться агент. Чтобы убедиться, что компьютер имеет доступ к конечным точкам Azure Stack, выполните следующие проверки.

1. Проверьте, что базовый URI доступен. Откройте командную строку или оболочку Bash и запустите следующую команду, заменив `<EXTERNALFQDN>` внешним полным доменным именем среды:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Откройте веб-браузер и перейдите к `https://adminportal.<EXTERNALFQDN>`, чтобы убедиться, что портал MAS доступен.

3. Войдите, используя значения имени и пароля администратора служб Azure AD, предоставленные при создании тестового прохода.

4. Проверьте состояние работоспособности системы, выполнив командлет PowerShell **Test-AzureStack**, как описано в статье [Запуск проверочного теста в Azure Stack](../operator/azure-stack-diagnostic-test.md). Устраните предупреждения и исправьте ошибки перед запуском каких-либо тестов.

## <a name="run-the-local-agent"></a>Запустите локальный агент.

1. Откройте Windows PowerShell из командной строки с повышенными привилегиями.

2. Выполните следующую команду:

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Параметры**  

    | Параметр | Описание |
    | --- | --- |
    | CloudAdminUserName | Администратор облака, который имеет права на запуск разрешенных команд в привилегированной конечной точке и доступ к ним. Например, это может быть AzusreStack\CloudAdmin. Дополнительные сведения см. в [этом разделе](azure-stack-vaas-parameters.md) . |
    | CloudAdminPassword | Пароль к учетной записи администратора облака.|
    | VaaSUserId | Идентификатор пользователя, используемый для входа на портал VaaS (например, имя_пользователя\@Contoso.com). |
    | VaaSTenantId | Идентификатор клиента Azure AD для учетной записи Azure, зарегистрированный с помощью проверки как услуги. |

    > [!Note]  
    > При запуске агента текущий рабочий каталог должен быть расположением исполняемого файла узла обработчика задач **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Если вы не видите сообщения об ошибках, локальный агент был успешно выполнен. Следующий пример текста отображается в окне консоли.

`Heartbeat was sent successfully.`

![Запущенный агент](media/started-agent.png)

Агент однозначно идентифицируется по имени. По умолчанию используется полное доменное имя (FQDN) компьютера, на котором он был запущен. Уменьшите окно, чтобы случайно не щелкнуть его, так как при изменении фокуса остальные действия приостанавливаются.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка как услуга: устранение неполадок](azure-stack-vaas-troubleshoot.md)
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Проверка как услуга: основные понятия)
- [Краткое руководство. Планирование первого теста](azure-stack-vaas-schedule-test-pass.md);