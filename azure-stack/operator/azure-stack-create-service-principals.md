---
title: Получение доступа к ресурсам с использованием удостоверения приложения | Документация Майкрософт
description: Сведения о настройке субъекта-службы, с помощью которого в сочетании с механизмом управления доступом на основе ролей можно выполнять вход и получать доступ к ресурсам.
services: azure-stack
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-stack
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2019
ms.author: bryanla
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 7110febfa58fb1d31cde5f0ae1b4df659f567956
ms.sourcegitcommit: 8203490cf3ab8a8e6d39b137c8c31e3baec52298
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73712739"
---
# <a name="use-an-app-identity-to-access-resources"></a>Использование удостоверения приложения для доступа к ресурсам

*Область применения: интегрированные системы Azure Stack и среда "Пакет средств разработки Azure Stack" (ASDK)*

Приложение, необходимое для развертывания или настройки ресурсов через Azure Resource Manager, должно быть представлено субъектом-службой. Так же как пользователь представлен субъектом-пользователем, субъект-служба — это тип субъекта безопасности, который представляет приложение. Субъект-служба предоставляет приложению удостоверение, что позволяет делегировать субъекту-службе только необходимые разрешения.  

Например, можно создать приложение управления конфигурацией, которое использует Azure Resource Manager для создания списка ресурсов Azure. Чтобы реализовать этот сценарий, вам нужно создать субъект-службу, назначить ему роль читателя и предоставить приложению управления конфигурацией доступ только для чтения.

## <a name="overview"></a>Обзор

Как и субъект-пользователь, субъект-служба должен предоставить учетные данные во время проверки подлинности. Эта проверка подлинности состоит из двух элементов:

- **Идентификатор приложения**, который иногда называют идентификатором клиента. Это глобальный уникальный идентификатор (GUID), однозначно определяющий регистрацию приложения в клиенте Active Directory.
- **Секрет**, связанный с идентификатором приложения. Вы можете создать строку секрета клиента (аналогичную паролю) или указать сертификат X509 (который использует открытый ключ).

Запуск приложения с удостоверением субъекта-службы предпочтительнее по сравнению с его запуском от имени субъекта-пользователя, так как:

 - Субъект-служба может использовать сертификат X509. Такие **учетные данные более надежные**.  
 - Вы можете назначить субъекту-службе **более строгие разрешения**. Как правило, приложение получает именно те разрешения, которые требуются для его работы. Это называется *принцип минимальных привилегий*.
 - **Разрешения и учетные данные субъекта-службы не требуется изменять так часто**, как учетные данные пользователя. Например, когда изменяются обязанности пользователя или пользователь покидает организацию, требования к паролю определяют изменение.

Сначала необходимо создать регистрацию приложения в каталоге, в результате чего создается связанный [объект субъекта-службы](/azure/active-directory/develop/developer-glossary#service-principal-object), представляющий удостоверение приложения в каталоге. В этом документе описывается процесс создания субъекта-службы и управления им в зависимости от выбранного каталога экземпляра Azure Stack:

- Azure Active Directory (Azure AD). Azure AD — это мультитенантный облачный каталог и служба управления удостоверениями. Azure AD можно использовать с подключенным экземпляром Azure Stack.
- Служба федерации Active Directory (AD FS). В AD FS представлены возможности упрощенной безопасной федерации удостоверений и единого входа. AD FS можно использовать с подключенными и отключенными экземплярами Azure Stack.

Сначала вы узнаете, как настроить субъект-службу, а затем как назначить ему роль, ограничивая доступ к ресурсам.

## <a name="manage-an-azure-ad-service-principal"></a>Управление субъектом-службой Azure AD

Если вы развернули Azure Stack с помощью Azure AD в качестве службы управления удостоверениями, субъекты-службы можно создать так же, как в Azure. В этом разделе описан процесс с использованием портала Azure. Прежде чем начать, проверьте [необходимые разрешения Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Создание субъекта-службы, который использует учетные данные в виде секрета клиента

Из этого раздела вы узнаете, как зарегистрировать приложение с помощью портала Azure, в результате чего в клиенте Azure AD создается объект субъекта-службы. В этом примере субъект-служба создается с учетными данными в виде секрета клиента, но портал также поддерживает учетные данные на основе сертификата X509.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи Azure.
2. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.
3. Укажите **имя** для приложения.
4. Выберите необходимые **поддерживаемые типы учетных записей**.
5. В разделе **URI перенаправления** выберите **Веб** как тип приложения и (необязательно) укажите URI перенаправления, если это необходимо приложению.
6. Выбрав нужные значения, нажмите кнопку **Зарегистрировать**. После этого создается регистрация приложения и открывается страница **Обзор**.
7. Скопируйте **идентификатор приложения** и вставьте его в код приложения. Эта строка также называется идентификатором клиента.
8. Чтобы создать секрет клиента, перейдите на страницу **Сертификаты и секреты**. Выберите **New client secret** (Создать секрет клиента).
9. Введите **описание** секрета и **срок его действия**.
10. Когда все будет готово, нажмите **Добавить**.
11. После этого отобразится значение секрета. Скопируйте и сохраните его в другом месте, так как позже вы не сможете получить его. Секрет и идентификатор приложения указываются в клиентском приложении во время входа в субъект-службу.

    ![Сохраненный ключ в секретах клиента](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Управление субъектом-службой AD FS

Если вы развернули Azure Stack с помощью AD FS в качестве службы управления удостоверениями, настроить субъект-службу необходимо с использованием PowerShell. Ниже приведены примеры для управления учетными данными субъекта-службы на основе сертификата X509 и секрета клиента.

Сценарии необходимо выполнять в консоли PowerShell, открытой с повышенными привилегиями ("Запуск от имени администратора"). Это также открывает другой сеанс на виртуальной машине, на котором размещена привилегированная конечная точка экземпляра Azure Stack. После установления сеанса привилегированной конечной точки с помощью дополнительных командлетов можно настроить субъект-службу. Дополнительные сведения о привилегированной конечной точке см. в статье [Использование привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Создание субъекта-службы, который использует учетные данные на основе сертификата

При создании сертификата для учетных данных субъекта-службы необходимо соблюдать следующие требования:

 - Сертификаты должны быть выданы внутренним или общедоступным центром сертификации. Используя общедоступный центр сертификации, необходимо включить центр в базовый образ операционной системы в рамках программы по использованию доверенного корневого центра сертификации Майкрософт. Вы можете ознакомиться с полным списком здесь: [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca). Пример создания самозаверяющего тестового сертификата также будет показан позже в ходе [обновления учетных данных сертификата субъекта-службы](#update-a-service-principals-certificate-credential). 
 - Поставщик служб шифрования должен быть указан как устаревший поставщик ключей поставщика служб шифрования (CSP) Майкрософт.
 - Сертификат должен быть PFX-файлом, так как и открытый, и закрытый ключи являются обязательными. Серверы Windows используют PFX-файлы, содержащие файлы открытого ключа (файл SSL-сертификата) и связанные файлы закрытого ключа.
 - У инфраструктуры Azure Stack должен быть сетевой доступ к расположению списка отзыва сертификатов (CRL) центра сертификации, опубликованного в сертификате. Этот список отзыва сертификатов должен быть конечной точкой HTTP.

Получив сертификат, с помощью приведенного ниже сценария PowerShell зарегистрируйте приложение и создайте субъект-службу. С помощью субъекта-службы также выполняется вход в Azure. Вместо приведенных ниже заполнителей используйте собственные значения.

| Placeholder | ОПИСАНИЕ | Пример |
| ----------- | ----------- | ------- |
| \<PepVM\> | Имя виртуальной машины с привилегированной конечной точкой в экземпляре Azure Stack. | AzS-ERCS01 |
| \<YourCertificateLocation\> | Расположение сертификата X509 в локальном хранилище сертификатов. | Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34 |
| \<YourAppName\> | Описательное имя новой регистрации приложения. | Инструмент управления |

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующий сценарий:

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. После завершения выполнения сценария отобразятся сведения о регистрации приложения, включая учетные данные субъекта-службы. Как показано, свойства `ClientID` и `Thumbprint` используются для входа с использованием удостоверения субъекта-службы. После входа удостоверение субъекта-службы будет использоваться для последующей авторизации и доступа к ресурсам, управляемым Azure Resource Manager.

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Не закрывайте сеанс в консоли PowerShell, так как в следующем разделе он будет использоваться со значением `ApplicationIdentifier`.

### <a name="update-a-service-principals-certificate-credential"></a>Обновление учетных данных на основе сертификата, используемого субъектом-службой

После создания субъекта-службы из этого раздела вы узнаете, как:

1. Создать самозаверяющий сертификат X509 для тестирования.
2. Обновить учетные данные субъекта-службы, обновив ее свойство **Thumbprint** в соответствии с новым сертификатом.

Обновите учетные данные на основе сертификата с помощью PowerShell, подставив собственные значения приведенных ниже заполнителей.

| Placeholder | ОПИСАНИЕ | Пример |
| ----------- | ----------- | ------- |
| \<PepVM\> | Имя виртуальной машины с привилегированной конечной точкой в экземпляре Azure Stack. | AzS-ERCS01 |
| \<YourAppName\> | Описательное имя новой регистрации приложения. | Инструмент управления |
| \<YourCertificateLocation\> | Расположение сертификата X509 в локальном хранилище сертификатов. | Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34 |
| \<AppIdentifier\> | Идентификатор, присвоенный регистрации приложения. | S-1-5-21-1512385356-3796245103-1243299919-1356 |

1. В сеансе Windows PowerShell, открытом с повышенными правами, выполните следующие командлеты:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. После выполнения сценария отобразятся сведения об обновленной регистрации приложения, включая значение отпечатка нового самозаверяющего сертификата.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Создание субъекта-службы, которая использует учетные данные в виде секрета клиента

> [!IMPORTANT]
> Учетные данные на основе секрета клиента менее безопасны, чем на основе сертификата X509. Дело не только в менее безопасном механизме проверки подлинности. При использовании этого типа учетных данных секрет необходимо внедрять в исходный код клиентского приложения. Учитывая вышесказанное, для рабочих приложений советуем использовать учетные данные на основе сертификата.

Теперь необходимо создать другую регистрацию приложения, но в этот раз укажите учетные данные на основе секрета клиента. В отличие от учетных данных на основе сертификата каталог поддерживает создание учетных данных на основе секрета клиента. Вместо указания секрета клиента вы с помощью параметра `-GenerateClientSecret` можете создать запрос на их создание. Вместо приведенных ниже заполнителей используйте собственные значения.

| Placeholder | ОПИСАНИЕ | Пример |
| ----------- | ----------- | ------- |
| \<PepVM\> | Имя виртуальной машины с привилегированной конечной точкой в экземпляре Azure Stack. | AzS-ERCS01 |
| \<YourAppName\> | Описательное имя новой регистрации приложения. | Инструмент управления |

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие командлеты.

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. После завершения выполнения сценария отобразятся сведения о регистрации приложения, включая учетные данные субъекта-службы. Как показано, свойство `ClientID` и созданный секрет `ClientSecret` используются для входа с использованием удостоверения субъекта-службы. После входа удостоверение субъекта-службы будет использоваться для последующей авторизации и доступа к ресурсам, управляемым Azure Resource Manager.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Не закрывайте сеанс в консоли PowerShell, так как в следующем разделе он будет использоваться со значением `ApplicationIdentifier`.

### <a name="update-a-service-principals-client-secret"></a>Обновление секрета клиента субъекта-службы

Обновите учетные данные на основе секрета клиента с помощью параметра **ResetClientSecret** в PowerShell, который немедленно изменяет секрет клиента. Вместо приведенных ниже заполнителей используйте собственные значения.

| Placeholder | ОПИСАНИЕ | Пример |
| ----------- | ----------- | ------- |
| \<PepVM\> | Имя виртуальной машины с привилегированной конечной точкой в экземпляре Azure Stack. | AzS-ERCS01 |
| \<AppIdentifier\> | Идентификатор, присвоенный регистрации приложения. | S-1-5-21-1634563105-1224503876-2692824315-2623 |

1. В сеансе Windows PowerShell, открытом с повышенными правами, выполните следующие командлеты:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. После завершения выполнения сценария отобразятся сведения об обновленной регистрации приложения, включая новый секрет клиента.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Удаление субъекта-службы

Теперь вы узнаете, как с помощью PowerShell удалить регистрацию приложения из каталога и связанный с ней объект субъекта-службы. 

Вместо приведенных ниже заполнителей используйте собственные значения.

| Placeholder | ОПИСАНИЕ | Пример |
| ----------- | ----------- | ------- |
| \<PepVM\> | Имя виртуальной машины с привилегированной конечной точкой в экземпляре Azure Stack. | AzS-ERCS01 |
| \<AppIdentifier\> | Идентификатор, присвоенный регистрации приложения. | S-1-5-21-1634563105-1224503876-2692824315-2623 |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

При вызове командлета Remove-GraphApplication на привилегированной конечной точке выходные данные не возвращаются, но во время выполнения этого командлета в консоли отобразится подтверждение в виде строки verbatim:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Назначение роли

Пользователи и приложения получают доступ к ресурсам Azure с помощью механизма управления доступом на основе ролей (RBAC). Чтобы разрешить приложению доступ к ресурсам в подписке с помощью субъекта-службы, необходимо *назначить* этому субъекту-службе *роль* для конкретного *ресурса*. Сначала укажите, какая роль предоставит приложению необходимые *разрешения*. Дополнительные сведения о доступных ролях см. в статье [Встроенные роли для ресурсов Azure](/azure/role-based-access-control/built-in-roles).

Выбранный тип ресурса также устанавливает *область доступа* для субъекта-службы. Вы можете задать область доступа на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы.

1. Войдите на соответствующий портал в зависимости от каталога, указанного во время установки Azure Stack (например, портал Azure для Azure AD или пользовательский портал Azure Stack для AD FS). В этом примере используется пользовательский портал Azure Stack.

   > [!NOTE]
   > Чтобы добавить назначение ролей для определенного ресурса, учетной записи пользователя должна быть назначена роль с разрешением `Microsoft.Authorization/roleAssignments/write`. Это, например, либо встроенная роль [Владелец](/azure/role-based-access-control/built-in-roles#owner), либо [Администратор доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator).  
2. Перейдите к ресурсу, к которому вы хотите предоставить доступ субъекту-службе. В этом примере вы назначите субъекту-службе роль на уровне подписки. Для этого выберите **Подписки**, а затем укажите конкретную подписку. Вы также можете выбрать группу ресурсов или конкретный ресурс, например виртуальную машину.

     ![Выбор подписки для назначения](./media/azure-stack-create-service-principal/select-subscription.png)

3. Перейдите на страницу **Управление доступом (IAM)** . Эта универсальная страница доступна для всех ресурсов, которые поддерживают RBAC.
4. Выберите **+ Добавить**.
5. В разделе **Роль** выберите роль, которая будет назначена приложению.
6. В разделе **Выбор** найдите приложение, используя полное или частичное его имя. Во время регистрации имя приложения имеет следующий формат: *Azurestack-\<имя_приложения\>-\<идентификатор_клиента\>* . Например, если приложение имеет имя *App2* и во время создания был назначен идентификатор клиента *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*, то полное имя будет *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Вы можете выполнять поиск по точной строке или по фрагменту, например *Azurestack* или *Azurestack-App2*.
7. Когда найдете приложение, выберите его. Оно отобразится в разделе **Выбранные элементы**.
8. Выберите **Сохранить**, чтобы завершить назначение роли.

     [![Назначение роли](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. По завершении приложение отобразится в списке субъектов, назначенных в текущей области, для этой роли.

     [![Назначенная роль](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Итак, вы создали субъект-службу и назначили ему роль. Теперь вы можете использовать субъект-службу в приложении для доступа к ресурсам Azure Stack.  

## <a name="next-steps"></a>Дополнительная информация

[Добавление пользователей для AD FS](azure-stack-add-users-adfs.md)  
[Управление разрешениями пользователей](azure-stack-manage-permissions.md)  
[Документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services) (Службы федерации Active Directory)
