---
title: Добавление Kubernetes в Azure Stack Marketplace | Документация Майкрософт
description: Узнайте, как добавить Kubernetes в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 09fa7b503f0c594d2af0c6f16a6d4618cec0fac3
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308480"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Добавление Kubernetes в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии. Сейчас в предварительной версии не поддерживаются сценарии работы с Azure Stack в автономном режиме.

Вы можете обеспечить своим пользователям доступ к Kubernetes из Azure Stack Marketplace. Затем развертывание Kubernetes выполняется за одну согласованную операцию.

В этой статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager. Прежде чем начать, проверьте настройки Azure Stack и глобальные параметры клиента Azure. Соберите необходимые сведения об Azure Stack. Добавьте необходимые ресурсы в клиент и Azure Stack Marketplace. Кластер зависит от сервера Ubuntu, настраиваемого скрипта и элемента кластера Kubernetes из Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Создание плана, предложения и подписки

Создайте план, предложение и подписку для Kubernetes. Можно также использовать имеющийся план и предложение.

1. Войдите на [портал администрирования.](https://adminportal.local.azurestack.external)

1. Создайте базовый план. Инструкции см. в статье [Создание плана в Azure Stack](azure-stack-create-plan.md).

1. Создайте предложение. Инструкции см. в статье [Создание предложения в Azure Stack](azure-stack-create-offer.md).

1. Выберите **Предложения** и найдите созданное предложение.

1. В колонке "Предложение" выберите **Обзор**.

1. Щелкните **Изменить состояние**. Щелкните **Общедоступный**.

1. Чтобы создать подписку, последовательно выберите **+ Create a resource**(+ Создать ресурс) > **Offers and Plans**(Предложения и планы) > **Подписка**.

    a. Введите значение в поле **Отображаемое имя**.

    b. Введите значение в поле **Пользователь**. Используйте учетную запись Azure AD, связанную с вашим клиентом.

    c. **Описание поставщика**

    d. В поле **Клиент каталога** укажите клиент Azure AD для Azure Stack. 

    д. Выберите **Предложение**. Выберите имя созданного предложения. Запишите идентификатор подписки.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Создание субъекта-службы и учетных данных в AD FS

Если вы используете службы федерации Active Directory (AD FS) для службы управления удостоверениями, необходимо будет создать субъект-службу для пользователей, развертывающих кластер Kubernetes.

1. Создайте и экспортируйте самозаверяющий сертификат, используемый для создания субъекта-службы. 

    - Вам понадобятся следующие сведения:

       | Значение | ОПИСАНИЕ |
       | ---   | ---         |
       | Пароль | Введите новый пароль для сертификата. |
       | Локальный путь к сертификату | Введите путь и имя файла сертификата. Например: `c:\certfilename.pfx` |
       | Имя сертификата | Введите имя сертификата. |
       | Расположение хранилища сертификатов |  Например `Cert:\LocalMachine\My`. |

    - Откройте PowerShell с помощью командной строки с повышенными привилегиями. Выполните следующий скрипт, используя параметры, обновленные в соответствии с вашими значениями:

        ```powershell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Запишите новый идентификатор сертификата, отображаемый в сеансе PowerShell, `1C2ED76081405F14747DC3B5F76BB1D83227D824`. Идентификатор будет использоваться при создании субъекта-службы.

    ```powershell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Создайте субъект-службу с помощью сертификата.

    - Вам понадобятся следующие сведения:

       | Значение | ОПИСАНИЕ                     |
       | ---   | ---                             |
       | IP-адрес ERCS | В ASDK привилегированная конечная точка — это, как правило, `AzS-ERCS01`. |
       | имя приложения; | Введите простое имя субъекта-службы приложения. |
       | Расположение хранилища сертификатов | Путь на компьютере, где был сохранен сертификат. На это указывает местоположение хранилища и идентификатор сертификата, сформированный на первом этапе. Например: `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       При появлении запроса используйте следующие учетные данные для подключения к конечной точке привилегии. 
        - Имя пользователя. Укажите учетную запись CloudAdmin в формате `<Azure Stack domain>\cloudadmin`. (При использовании ASDK имя пользователя — azurestack\cloudadmin.)
        - Пароль: Введите пароль, который использовался во время установки учетной записи администратора домена AzureStackAdmin.

    - Выполните следующий скрипт, используя параметры, обновленные в соответствии с вашими значениями:

        ```powershell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - Сведения о субъекте-службе выглядят как приведенный ниже фрагмент кода.

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Добавление образа сервера Ubuntu

Добавьте в Marketplace следующий образ сервера Ubuntu:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Ubuntu Server`.

1. Выберите последнюю версию сервера. Проверьте полный номер версии и убедитесь, что вы используете последнюю версию:
    - **Издатель**: Canonical
    - **Предложение**: UbuntuServer
    - **Версия.** 16.04.201806120 (или последняя версия)
    - **SKU.** 16.04-LTS

1. Выберите **Скачать**.

## <a name="add-a-custom-script-for-linux"></a>Добавление настраиваемого скрипта для Linux

Добавьте Kubernetes в Marketplace, выполнив следующие действия:

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Custom Script for Linux`.

1. Выберите скрипт со следующим профилем:
   - **Предложение**: Настраиваемый скрипт для Linux 2.0
   - **Версия.** 2.0.6 (или последняя версия)
   - **Издатель**: Корпорация Майкрософт

     > [!Note]  
     > Могут отображаться несколько версий настраиваемого скрипта для Linux. Необходимо добавить последнюю версию элемента.

1. Выберите **Скачать**.


## <a name="add-kubernetes-to-the-marketplace"></a>Добавление Kubernetes в Marketplace

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Kubernetes`.

1. Выберите `Kubernetes Cluster`.

1. Выберите **Скачать**.

    > [!note]  
    > Для отображения элемента в Marketplace может потребоваться пять минут.

    ![kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Обновление или удаление Kubernetes 

При обновлении Kubernetes удалите предыдущий элемент в Marketplace. Выполните инструкции из этой статьи по добавлению обновления Kubernetes в Marketplace.

Чтобы удалить Kubernetes, выполните такие действия:

1. Подключитесь к Azure Stack с помощью PowerShell в роли оператора. Инструкции см. в статье [Настройка среды PowerShell в Azure Stack](azure-stack-powershell-configure-admin.md).

2. Найдите текущий элемент кластера Kubernetes в коллекции.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Запомните имя текущего элемента, например `Microsoft.AzureStackKubernetesCluster.0.3.0`.

4. Удалите элемент, выполнив следующий командлет PowerShell:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Дополнительная информация

[Развертывание Kubernetes в Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Общие сведения о предложении служб в Azure Stack](azure-stack-offer-services-overview.md)
