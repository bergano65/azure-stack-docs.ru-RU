---
title: Управление субъектом-службой для Azure Stack | Документация Майкрософт
description: Описывается управление новым субъектом-службой, который можно использовать в Azure Resource Manager в сочетании с контролем доступа на основе ролей для управления доступом к ресурсам.
services: azure-resource-manager
documentationcenter: na
author: PatAltimore
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: patricka
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: b08d2b59653b099b0cd0a314347ea2667fa42ca8
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691298"
---
# <a name="provide-applications-access-to-azure-stack"></a>Предоставление приложениям доступа к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Если приложению нужен доступ для развертывания или настройки ресурсов Azure Stack через Azure Resource Manager, вам следует создать субъект-службу, который будет использоваться как учетные данные для этого приложения. Этому субъекту-службе вы сможете делегировать только минимально необходимые разрешения.  

Например, можно создать средство управления конфигурацией, которое использует Azure Resource Manager для создания списка ресурсов Azure. Чтобы реализовать этот сценарий, вам нужно создать субъект-службу, назначить ему роль читателя и предоставить средству управления конфигурацией доступ только для чтения. 

Субъекты-службы предпочтительнее использовать для запуска приложения с вашими учетными данными по следующим причинам:

 - Для субъекта-службы можно назначить разрешения, которые отличаются от ваших разрешений учетной записи. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
 - Не требуется изменять учетные данные приложения в случае изменения ваших обязанностей.
 - Можно использовать сертификат, чтобы автоматизировать аутентификацию при выполнении автоматического сценария.  

## <a name="getting-started"></a>Начало работы

Прежде всего нужно создать субъект-службу. Процесс будет разным в зависимости от способа развертывания Azure Stack. В этом документе описывается создание субъекта-службы для

- Azure Active Directory (Azure AD). Azure AD — это мультитенантный облачный каталог и служба управления удостоверениями. Azure AD можно использовать с подключенной инфраструктурой Azure Stack.
- Служба федерации Active Directory (AD FS). В AD FS представлены возможности упрощенной безопасной федерации удостоверений и единого входа. AD FS можно использовать с подключенными и отключенными экземплярами Azure Stack.

Создав субъект-службу, вы делегируете разрешения для этой роли, используя единый процесс для AD FS и Azure AD.

## <a name="manage-service-principal-for-azure-ad"></a>Управление субъектом-службой для Azure AD

Если вы развернули Azure Stack с помощью Azure Active Directory (Azure AD) как службу управления удостоверениями, субъекты-службы можно создать так же, как в Azure. В этом разделе описан процесс с использованием портала. Прежде чем начать, проверьте [необходимые разрешения Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="create-service-principal"></a>Создание субъекта-службы

В этом разделе вы создадите в Azure AD приложение (субъект-службу), которое будет представлять ваше приложение.

1. Войдите в учетную запись Azure через [портал Azure](https://portal.azure.com).
2. Последовательно выберите элементы **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.
3. Укажите имя и URL-адрес для приложения. 
4. Выберите **Поддерживаемые типы учетных записей**.
5.  Добавьте URI для приложения. В качестве типа создаваемого приложения выберите **Web** (Веб-приложение). Выбрав нужные значения, нажмите кнопку **Зарегистрировать**.

Субъект-служба для приложения создан.

### <a name="get-credentials"></a>Получение учетных данных

Если вход выполняется программными средствами, вам потребуются идентификатор приложения и ключ аутентификации для веб-приложения или API. Получить эти значения можно следующим образом.

1. Выберите **Azure Active Directory** > **Регистрация приложений**. Выберите приложение.

2. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Приложения в разделе примеров приложений используют это значение как идентификатор клиента.

3. Чтобы создать ключ аутентификации для веб-приложения или API, выберите **Сертификаты и секреты**. Выберите **New client secret** (Создать секрет клиента).

4. Введите описание и срок действия ключа. Когда все будет готово, нажмите **Добавить**.

После этого отобразится значение ключа. Скопируйте на время это значение в Блокнот или любое другое место, так как вы не сможете получить этот ключ позже. Значение ключа необходимо предоставить вместе с идентификатором приложения для входа от имени приложения. Сохраните значение ключа в том расположении, из которого приложение сможет извлечь это значение.

![Сохраненный ключ](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

После завершения можно назначить роль приложению.

## <a name="manage-service-principal-for-ad-fs"></a>Управление субъектом-службой для AD FS

Если вы развернули Azure Stack с помощью служб федерации Active Directory (AD FS) как службу управления удостоверениями, используйте PowerShell для создания субъекта-службы, назначения роли для доступа и входа с помощью этого удостоверения.

Создать субъект-службу с помощью AD FS можно одним из двух способов. Вы можете:
 - [создать субъект-службу с помощью сертификата](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate);
 - [создать субъект-службу с помощью секрета клиента](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret).

Задачи для управления субъектами-службами AD FS

| type | Действие |
| --- | --- |
| Сертификат AD FS | [Создание](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Сертификат AD FS | [Обновление](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs). |
| Сертификат AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Секрет клиента AD FS | [Создание](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Секрет клиента AD FS | [Обновление](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret). |
| Секрет клиента AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Создание субъекта-службы с помощью сертификата

При создании субъекта-службы с использованием AD FS для удостоверений можно применить сертификат.

#### <a name="certificate"></a>Сертификат

Требуется сертификат.

**Требования к сертификатам**

 - Поставщик служб шифрования (CSP) должен быть поставщиком ключей.
 - Сертификат должен быть PFX-файлом, так как и открытый, и закрытый ключи являются обязательными. Серверы Windows используют PFX-файлы, содержащие файлы открытого ключа (файл SSL-сертификата) и связанные файлы закрытого ключа.
 - Сертификаты должны быть выданы внутренним или общедоступным центром сертификации. Используя общедоступный центр сертификации, необходимо включить центр в базовый образ операционной системы как часть программы "Доверенный корневой центр сертификации Майкрософт". Вы можете ознакомиться с полным списком здесь: [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - У инфраструктуры Azure Stack должен быть сетевой доступ к расположению списка отзыва сертификатов (CRL) центра сертификации, опубликованного в сертификате. Этот список отзыва сертификатов должен быть конечной точкой HTTP.

#### <a name="parameters"></a>Параметры

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|ИМЯ|Имя учетной записи имени субъекта-службы|MyAPP|
|ClientCertificates|Массив объектов сертификата|Сертификат X509|
|ClientRedirectUris<br>(необязательный параметр)|URI перенаправления приложения|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Использование PowerShell для создания субъекта-службы

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие командлеты.

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

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

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > В целях проверки можно создать самозаверяющий сертификат на основе приведенного ниже примера.

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Когда работа службы автоматизации завершится, будут отображены необходимые сведения для использования имени субъекта-службы. Рекомендуется сохранить выходные данные для последующего использования.

   Например:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Обновление сертификата для субъекта-службы для AD FS

Если вы развернули Azure Stack с помощью AD FS, обновите секрет субъекта-службы с помощью PowerShell.

Этот скрипт выполняется из привилегированной конечной точки на виртуальной машине ERCS.

#### <a name="parameters"></a>Параметры

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|ИМЯ|Имя учетной записи имени субъекта-службы|MyAPP|
|ApplicationIdentifier|Уникальный идентификатор|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Массив объектов сертификата|Сертификат X509|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Пример обновления субъекта-службы для AD FS

В этом примере показано создание самозаверяющего сертификата. При выполнении этих командлетов в рабочей среде используйте команду [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item), чтобы получить объект сертификата для сертификата, который вы хотите использовать.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие командлеты.

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Когда работа службы автоматизации завершится, будет отображено обновленное значение эскиза, необходимое для проверки подлинности SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Создание субъекта-службы с помощью секрета клиента

При создании субъекта-службы с использованием AD FS для удостоверений можно применить сертификат. Для выполнения командлетов будет использоваться привилегированная конечная точка.

Эти скрипты выполняются из привилегированной конечной точки на виртуальной машине ERCS. Дополнительные сведения о привилегированной конечной точке см. в статье [Использование привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>Параметры

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

| Параметр | ОПИСАНИЕ | Пример |
|----------------------|--------------------------|---------|
| ИМЯ | Имя учетной записи имени субъекта-службы | MyAPP |
| GenerateClientSecret | Создание секрета |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Использование привилегированной конечной точки ERCS для создания субъекта-службы

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие командлеты.

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. После выполнения командлетов в оболочке будут отображены необходимые сведения для использования имени субъекта-службы. Сохраните секрет клиента.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Обновление секрета клиента для субъекта-службы для AD FS

Новый секрет клиента создается автоматически с помощью командлета PowerShell.

Этот скрипт выполняется из привилегированной конечной точки на виртуальной машине ERCS.

##### <a name="parameters"></a>Параметры

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

| Параметр | ОПИСАНИЕ | Пример |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Уникальный идентификатор. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Изменяет секрет клиента с периодом смены в 2880 минут, где старый секрет все еще является допустимым. |  |
| ResetClientSecret | Немедленно изменяет секрет клиента |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Пример обновления секрета клиента для AD FS

В примере используется параметр **ResetClientSecret**, который немедленно изменяет секрет клиента.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие командлеты.

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Когда работа службы автоматизации завершится, будет отображен созданный секрет, необходимый для проверки подлинности SPN. Сохраните новый секрет клиента.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Удаление субъекта-службы для AD FS

Если вы развернули Azure Stack с помощью AD FS, удалите субъект-службу с помощью PowerShell.

Этот скрипт выполняется из привилегированной конечной точки на виртуальной машине ERCS.

#### <a name="parameters"></a>Параметры

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
| Параметр | ОПИСАНИЕ | Пример |
| ApplicationIdentifier | Уникальный идентификатор | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Просмотреть список всех имеющихся субъектов-служб и их идентификаторов приложений можно с помощью команды get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Пример удаления субъекта-службы для AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Назначение роли

Чтобы обеспечить доступ к ресурсам в подписке, необходимо назначить приложению роль. Укажите, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](/azure/role-based-access-control/built-in-roles).

Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы.

1. На портале Azure Stack перейдите на уровень области действия, в которой вы хотите разместить приложение. Например, чтобы назначить роль в области действия подписки выберите **Подписки**. Или же вы можете выбрать группу ресурсов либо отдельный ресурс.

2. Выберите определенную подписку, группу ресурсов или ресурс, которым будет назначено приложение.

     ![Выбор подписки для назначения](./media/azure-stack-create-service-principal/image16.png)

3. Выберите **Управление доступом (IAM)** .

     ![Выбор доступа](./media/azure-stack-create-service-principal/image17.png)

4. Выберите **Добавить назначение ролей**.

5. Выберите роль, которая будет назначена приложению.

6. Найдите приложение и выберите его.

7. Нажмите кнопку **ОК**, чтобы завершить назначение роли. Вы увидите свое приложение в списке пользователей, назначенных выбранной роли для выбранной области действия.

Итак, вы создали субъект-службу и назначили ему роль. Теперь вы можете использовать его в приложении для доступа к ресурсам Azure Stack.  

## <a name="next-steps"></a>Дополнительная информация

[Добавление пользователей для AD FS](azure-stack-add-users-adfs.md)  
[Управление разрешениями пользователей](azure-stack-manage-permissions.md)  
[Документация по Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services) (Службы федерации Active Directory)
