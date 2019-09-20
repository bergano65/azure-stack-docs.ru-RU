---
title: Развертывание защищенного кластера Service Fabric в Azure Stack | Документация Майкрософт
description: Сведения о развертывании защищенного кластера Service Fabric в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: e672ee6227e00ea6276c92c22d02874f7c8b5529
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101255"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Развертывание кластера Service Fabric в Azure Stack

Используйте элемент **Кластер Service Fabric** из Azure Marketplace для развертывания защищенного кластера Service Fabric в Azure Stack. 

Дополнительные сведения о [работе с Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) и [сценариях защиты кластера Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) можно найти в документации Azure.

Кластер Service Fabric в Azure Stack не использует поставщик ресурсов Microsoft.ServiceFabric. Вместо этого в Azure Stack кластер Service Fabric представляет собой масштабируемый набор виртуальных машин с ПО, предустановленным с помощью платформы [Desired State Configuration (DSC)](https://docs.microsoft.com/powershell/dsc/overview/overview).

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены необходимые условия для развертывания кластера Service Fabric.
1. **Сертификат кластера**  
   Этот сертификат сервера X.509 вы добавляете в Key Vault при развертывании Service Fabric. 
   - **Имя этого сертификата** должно соответствовать полному доменному имени (FQDN) созданного вами кластера Service Fabric. Дополнительные сведения о FQDN см. в разделе о [сертификатах, необходимых для рабочего развертывания Службы приложений Azure в Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md#certificates-required-for-azure-stack-production-deployment-of-azure-app-service).
   - Сертификат должен быть PFX-файлом, так как и открытый, и закрытый ключи являются обязательными. 
     Дополнительные сведения см. в разделе с [требованиями](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) для создания сертификата на стороне сервера.

     > [!NOTE]  
     > Вы можете использовать самозаверяющий сертификат вместо сертификата сервера X.509 для тестирования. Самозаверяющие сертификаты не обязательно должны совпадать с FQDN кластера.

1. **Сертификат клиента администрирования.**  
   Это сертификат, который клиент использует для проверки подлинности в кластере Service Fabric. Он может быть самозаверяющим. Дополнительные сведения см. в разделе с [требованиями](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) для создания клиентского сертификата.

1. **Следующие компоненты должны быть доступны в Azure Stack Marketplace:**
    - **Windows Server 2016** — этот шаблон использует образ Windows Server 2016 для создания кластера.  
    - **Расширение для пользовательских скриптов** — расширение виртуальной машины от корпорации Майкрософт.  
    - **Настройка требуемого состояния PowerShell** — расширение виртуальной машины от корпорации Майкрософт.


## <a name="add-a-secret-to-key-vault"></a>Добавление секрета в Key Vault
Чтобы развернуть кластер Service Fabric, необходимо указать правильный *идентификатор секрета* Key Vault или URL-адрес для кластера Service Fabric. Шаблон Azure Resource Manager принимает Key Vault в качестве входного параметра. Затем при установке кластера Service Fabric шаблон получает сертификат кластера.

> [!IMPORTANT]  
> Добавить секрет в Key Vault для использования в Service Fabric можно с помощью PowerShell. Не используйте портал.  

Используйте приведенный ниже скрипт, чтобы создать Key Vault и добавить *сертификат кластера*. (См. раздел [Предварительные требования](#prerequisites).) Прежде чем запустить скрипт, просмотрите его пример и обновите указанные параметры в соответствии со своей средой. Этот скрипт также будет выводить значения, которые необходимо указать в шаблоне Azure Resource Manager. 

> [!TIP]  
> Для успешного выполнения скрипта нужно предоставить общедоступное предложение, которое содержит службы для вычислительных ресурсов, сети, хранилища и Key Vault. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


См. дополнительные сведения об [управлении Key Vault в Azure Stack с помощью PowerShell](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>Развертывание элемента Marketplace

1. На пользовательском портале последовательно выберите **+ Создать ресурс** > **Вычисление** > **Кластер Service Fabric**. 

   ![Выбор кластера Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Для каждой страницы, например *Основные сведения*, заполните форму развертывания. Используйте значения по умолчанию, если вы не знаете нужные значения.

    Чтобы выполнить развертывание в отключенной инфраструктуре Azure Stack или развертывание другой версии Service Fabric, скачайте пакет развертывания Service Fabric и соответствующий пакет среды выполнения и разместите их в большом двоичном объекте Azure Stack. Укажите эти значения в полях **URL-адреса пакета развертывания Service Fabric** и **URL-адреса пакета среды выполнения Service Fabric**.
    > [!NOTE]  
    > У последних выпусков Service Fabric и соответствующих пакетов SDK существуют проблемы с совместимостью. Пока они не устранены, укажите следующие параметры для URL-адреса пакета развертывания и URL-адреса пакета среды выполнения. Иначе развертывание завершится сбоем.
    > - URL-адрес пакета развертывания Service Fabric: <https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>.
    > - URL-адрес пакета среды выполнения Service Fabric: <https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>.
    >
    > Для отключенных развертываний скачайте эти пакеты из указанного расположения и разместите их локально в большом двоичном объекте Azure Stack.

   ![Основы](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. На странице *Параметры сети* укажите определенные порты, чтобы открыть их для ваших приложений:

   ![Параметры сети](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. На странице *Безопасность* добавьте значения, полученные при [создании Azure Key Vault](#add-a-secret-to-key-vault) и передаче секрета.

   В *соответствующем поле* введите отпечаток *сертификата клиента администрирования*. (См. раздел [Предварительные требования](#prerequisites).)
   
   - В поле "Исходное Key Vault"  укажите всю строку `keyVault id` из результатов скрипта. 
   - В поле URL-адреса сертификата кластера укажите полный URL-адрес из `Secret Id` из результатов скрипта. 
   - В поле отпечатка сертификата кластера укажите *отпечаток сертификата кластера* из результатов скрипта.
   - URL-адрес сертификата сервера. Если вы хотите использовать отдельный сертификат из сертификата кластера, отправьте сертификат в Key Vault и укажите полный URL-адрес секрета. 
   - В поле отпечатка сертификата сервера укажите отпечаток сертификата сервера.
   - В поле отпечатков сертификата клиента администрирования укажите *отпечаток сертификата клиента администрирования*, созданный при выполнении предварительных требований. 

   ![Выходные данные скрипта](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Безопасность](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Завершите работу мастера, а затем нажмите кнопку **Создать** для развертывания кластера Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Доступ к кластеру Service Fabric
Вы можете получить доступ к кластеру Service Fabric, используя Service Fabric Explorer или Service Fabric PowerShell.


### <a name="use-service-fabric-explorer"></a>Использование Service Fabric Explorer
1.  Удостоверьтесь, что веб-браузер имеет доступ к сертификату клиента администрирования и может пройти проверку подлинности в кластере Service Fabric.  

    a. Откройте Internet Explorer и последовательно выберите **Свойства обозревателя** > **Содержимое** > **Сертификаты**.
  
    b. На странице сертификатов выберите **Импорт** для запуска *мастера импорта сертификатов*, а затем нажмите кнопку **Далее**. На странице *File to Import* (Файл для импорта) щелкните **Обзор** и выберите **сертификат клиента администрирования**, который вы указали в шаблоне Azure Resource Manager.
        
       > [!NOTE]  
       > Этот сертификат не является сертификатом кластера, который был добавлен ранее в Key Vault.  

    c. Убедитесь, что в окне проводника в раскрывающемся списке расширений выбрано значение "Файл обмена личной информацией".  

       ![Файл обмена личной информацией](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. На странице *Хранилища сертификатов* выберите **Личное**, а затем завершите работу мастера.  
       ![Хранилище сертификатов](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Чтобы найти FQDN кластера Service Fabric, выполните следующие действия.  

    a. Перейдите к группе ресурсов, связанной с кластером Service Fabric, и найдите ресурс *общедоступного IP-адреса*. Выберите объект, связанный с общедоступным IP-адресом, чтобы открыть колонку *Общедоступный IP-адрес*.  

      ![Общедоступный IP-адрес](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. В колонке "Общедоступный IP-адрес" FQDN отображается как *DNS-имя*.  

      ![DNS-имя](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Чтобы найти URL-адрес для Service Fabric Explorer и конечную точку подключения клиента, просмотрите результаты развертывания шаблона.

1. В браузере перейдите по адресу <https://*FQDN*:19080>. Замените *FQDN* на полное доменное имя кластера Service Fabric из шага 2.   
   Если вы использовали самозаверяющий сертификат, отобразится предупреждение о том, что подключение не является безопасным. Чтобы перейти на веб-сайт, выберите **Дополнительные сведения**, а затем **Перейти на веб-страницу**. 

1. Для проверки подлинности на сайте нужно выбрать используемый сертификат. Выберите **More choices** (Дополнительные варианты), выберите соответствующий сертификат и нажмите кнопку **ОК** для подключения к Service Fabric Explorer. 

   ![Проверка подлинности](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>Использование Service Fabric PowerShell

1. Установите *пакет SDK для Microsoft Azure Service Fabric*  из раздела [Установка пакета SDK и инструментов](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) документации Azure Service Fabric.  

1. После завершения установки настройте системные переменные среды, чтобы убедиться, что командлеты Service Fabric доступны в PowerShell.  
    
    a. Последовательно выберите **Панель управления** > **Система и безопасность** > **Система**, а затем выберите **Дополнительные параметры системы**.  
    
      ![Панель управления](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. На вкладке **Дополнительно** в *свойствах системы* выберите **Переменные среды**.  

    c. Для *системных переменных* измените **путь** и убедитесь, что **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** находится в верхней части списка переменных среды.  

      ![Список переменных среды](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. После изменения порядка переменных среды перезапустите PowerShell и выполните следующий скрипт PowerShell для получения доступа к кластеру Service Fabric:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > В скрипте отсутствует *https://* перед именем кластера. Требуется порт 19000.

## <a name="next-steps"></a>Дополнительная информация

[Развертывание Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
