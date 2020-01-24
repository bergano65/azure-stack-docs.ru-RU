---
title: Интеграция удостоверения AD FS с центром обработки данных Azure Stack Hub | Документация Майкрософт
description: Сведения об интеграции поставщика удостоверений AD FS Azure Stack Hub с системой AD FS в центре обработки данных.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 4d1ca3a04e838743983a7ed9d68fde5b1b189ff6
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817386"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-hub-datacenter"></a>Интеграция удостоверения AD FS с центром обработки данных Azure Stack Hub

Azure Stack Hub можно развернуть, используя в качестве поставщика удостоверений Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS). Этот выбор нужно сделать перед развертыванием Azure Stack Hub. При наличии подключения к Интернету вы можете выбрать Azure AD или AD FS. В сценарии без подключения поддерживается только AD FS. В этой статье описано, как интегрировать AD FS Azure Stack Hub с AD FS в центре обработки данных.

> [!IMPORTANT]
> Вы не сможете изменить поставщик удостоверений без повторного развертывания всего решения Azure Stack Hub.

## <a name="active-directory-federation-services-and-graph"></a>Службы федерации Active Directory и Graph

Развертывание AD FS позволяет удостоверениям в существующем лесу Active Directory проходить аутентификацию в ресурсах, размещенных в Azure Stack Hub. Этот существующий лес Active Directory требует развертывания AD FS, чтобы можно было создать отношения доверия с федерацией AD FS.

Аутентификация является одной частью применения удостоверения. Для управления доступом на основе ролей (RBAC) в Azure Stack Hub необходимо настроить компонент Graph. При делегировании доступа к ресурсу компонент Graph ищет учетную запись пользователя в существующем лесу Active Directory, используя протокол LDAP.

![Архитектура AD FS в Azure Stack Hub](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Существующие службы AD FS представляют собой службу токенов безопасности (STS) учетных записей, которая отправляет утверждения в AD FS для Azure Stack Hub (STS ресурсов). В Azure Stack Hub служба автоматизации создает отношения доверия с поставщиком утверждений при помощи конечной точки метаданных для существующих служб AD FS.

В существующих AD FS должны быть настроены отношения доверия с проверяющей стороной. Этот шаг не выполняется службой автоматизации и должен быть настроен оператором. Конечную точку с виртуальным IP-адресом Azure Stack Hub для AD FS можно создать в формате `https://adfs.<Region>.<ExternalFQDN>/`.

Конфигурация отношений доверия с проверяющей стороной также требует настройки правил преобразования утверждений, которые предоставляются корпорацией Майкрософт.

Для настройки Graph необходимо предоставить учетную запись службы с разрешением на чтение в существующей службе Active Directory. Эта учетная запись необходима в качестве входных данных службе автоматизации для реализации сценариев RBAC.

Для выполнения последнего шага для подписки поставщика по умолчанию настраивается новый владелец. Эта учетная запись получает полный доступ ко всем ресурсам после входа на портал администрирования Azure Stack Hub.

Требования

|Компонент|Требование|
|---------|---------|
|График|Microsoft Active Directory 2012, Microsoft Active Directory 2012 R2 или Microsoft Active Directory 2016|
|AD FS|Windows Server 2012, Windows Server 2012 R2 или Windows Server 2016|

## <a name="setting-up-graph-integration"></a>Настройка интеграции Graph

Graph поддерживает только интеграцию с отдельным лесом Active Directory. Если существует несколько лесов, только лес, указанный в конфигурации, будет использоваться для получения пользователей и групп.

Необходимо указать следующие сведения в качестве входных данных для параметров службы автоматизации.

|Параметр|Параметр листа сведений о развертывании|Description|Пример|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|Полное доменное имя леса ADFS|Полное доменное имя целевого леса Active Directory, с которым вы настраиваете интеграцию|Contoso.com|
|`CustomADAdminCredentials`| |Пользователь с разрешением на чтение LDAP.|ВАШ_ДОМЕН\graphservice|

### <a name="configure-active-directory-sites"></a>Настройка сайтов Active Directory

В случае развертываний Active Directory с несколькими сайтами настройте ближайший к вашему развертыванию Azure Stack Hub сайт Active Directory. Такая конфигурация позволяет избежать необходимости в разрешении запросов службой Graph в Azure Stack Hub через сервер глобального каталога на удаленном сайте.

Добавьте подсеть [Public VIP network](azure-stack-network.md#public-vip-network) (Общедоступная сеть VIP) Azure Stack Hub на сайт Active Directory, ближайший к Azure Stack Hub. Для примера предположим, что в вашей среде Active Directory есть два сайта: для Сиэтла и для Редмонда. Если экземпляр Azure Stack Hub развернут на сайте для Сиэтла, следует добавить к сайту Active Directory для Сиэтла подсеть сети Azure Stack Hub с общедоступными виртуальными IP-адресами.

Дополнительные сведения о сайтах Active Directory см. в разделе [Проектирование топологии сайтов](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Этот шаг можно пропустить, если Active Directory состоит из одного сайта. Если у вас настроена универсальная подсеть, убедитесь, что подсеть сети Azure Stack Hub с общедоступными виртуальными IP-адресами не является ее частью.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Создание учетной записи пользователя в существующей службе Active Directory (необязательно)

При необходимости можно создать учетную запись для службы Graph в существующей службе Active Directory. Выполните этот шаг, если у вас еще нет подходящей для использования учетной записи.

1. В существующей службе Active Directory создайте следующую учетную запись пользователя (рекомендация).
   - **Имя пользователя**: graphservice.
   - **Пароль**. Настройте надежный пароль без ограничения срока действия.

   Особые разрешения или членство не требуются.

#### <a name="trigger-automation-to-configure-graph"></a>Активация службы автоматизации для настройки Graph

Для выполнения этой процедуры используйте компьютер в сети центра обработки данных, который может взаимодействовать с привилегированной конечной точкой в Azure Stack Hub.

1. Откройте сеанс Windows PowerShell с повышенными правами (запуск от имени администратора) и подключитесь к IP-адресу привилегированной конечной точки. Используйте учетные данные **CloudAdmin** для аутентификации.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. После подключения к привилегированной конечной точке выполните следующие команды. 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   При появлении запроса укажите данные учетной записи пользователя, которую требуется использовать для службы Graph (например, graphservice). Для командлета Register-DirectoryService нужно указать имя леса и корневой домен в лесу, а не другие домены в лесу.

   > [!IMPORTANT]
   > Дождитесь появления всплывающего элемента для ввода учетных данных (Get-Credential не поддерживается на привилегированной конечной точке) и укажите учетные данные учетной записи службы Graph.

3. Командлет **Register-DirectoryService** имеет необязательные параметры, которые можно использовать в определенных сценариях при сбое существующей проверки Active Directory. При выполнении этот командлет проверяет, что предоставленный домен является корневым доменом, к серверу глобального каталога можно подключиться и предоставленная учетная запись обеспечивает доступ на чтение.

   |Параметр|Description|
   |---------|---------|
   |`-SkipRootDomainValidation`|Указывает, что вместо рекомендуемого корневого домена следует использовать дочерний домен.|
   |`-Force`|Обход всех проверок.|

#### <a name="graph-protocols-and-ports"></a>Протоколы и порты Graph

Служба Graph в Azure Stack Hub использует перечисленные ниже протоколы и порты для связи с доступным для записи глобальным сервером каталога (GC) и центром распространения ключей (KDC), который может обрабатывать запросы на вход в целевом лесу Active Directory.

Служба Graph в Azure Stack Hub использует приведенные ниже протоколы и порты для связи с целевой службой Active Directory.

|Тип|Порт|Протокол|
|---------|---------|---------|
|LDAP|389|TCP или UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Настройка интеграции AD FS путем скачивания метаданных федерации

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

|Параметр|Параметр листа сведений о развертывании|Description|Пример|
|---------|---------|---------|---------|
|CustomAdfsName|Имя поставщика AD FS|Имя поставщика утверждений.<br>Так оно отображается на целевой странице AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|URI метаданных AD FS|Ссылка на метаданные федерации.| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|Н/Д|Необязательный параметр, позволяющий пропустить проверку списка отзыва сертификатов.|None|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Активация службы автоматизации для настройки отношений доверия с поставщиком утверждений в Azure Stack Hub

Для выполнения этой процедуры используйте компьютер, который может взаимодействовать с привилегированной конечной точкой в Azure Stack Hub. Ожидается, что сертификат, используемый для учетной записи **AD FS службы токенов безопасности**, является доверенным для Azure Stack Hub.

1. Откройте сеанс Windows PowerShell с повышенными правами и подключитесь к привилегированной конечной точке.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Подключившись к привилегированной конечной точке, выполните следующую команду, указав параметры, подходящие для используемой среды.

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Выполните следующую команду, чтобы изменить владельца подписки поставщика по умолчанию, указав актуальные параметры для используемой среды.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Настройка интеграции AD FS путем предоставления файла метаданных федерации

Начиная с версии 1807, используйте этот метод, если выполняется какое-либо из следующих условий:

- Цепочка сертификатов для AD FS отличается по сравнению с другими конечными точками в Azure Stack Hub.
- Нет сетевого подключения между экземпляром AD FS Azure Stack Hub и существующим сервером AD FS.

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:


|Параметр|Description|Пример|
|---------|---------|---------|
|CustomAdfsName|Имя поставщика утверждений. Так оно отображается на целевой странице AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Содержимое метаданных.|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Создание файла метаданных федерации

Для выполнения следующей процедуры необходимо использовать компьютер с сетевым подключением к существующему развертыванию AD FS, которое становится службой токенов безопасности для учетной записи. Кроме того, нужно установить необходимые сертификаты.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующую команду, указав актуальные параметры для используемой среды.

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Копируйте файл метаданных на компьютер, который может взаимодействовать с привилегированной конечной точкой.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Активация службы автоматизации для настройки отношений доверия с поставщиком утверждений в Azure Stack Hub

Чтобы выполнить эту процедуру, используйте компьютер, который может взаимодействовать с привилегированной конечной точкой в Azure Stack Hub и имеет доступ к файлу метаданных, созданному на предыдущем шаге.

1. Откройте сеанс Windows PowerShell с повышенными правами и подключитесь к привилегированной конечной точке.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Подключившись к привилегированной конечной точке, выполните следующую команду, указав параметры, подходящие для используемой среды.

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Выполните следующую команду, чтобы изменить владельца подписки поставщика по умолчанию. Укажите актуальные параметры для используемой среды.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > При смене сертификата в существующих службах AD FS (служба токенов безопасности для учетной записи) нужно снова настроить интеграцию с AD FS. Эту интеграцию нужно настроить даже в том случае, если конечная точка метаданных доступна или была настроена посредством предоставления файла метаданных.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Настройка проверяющей стороны в существующем развертывании AD FS (службе токенов безопасности для учетной записи)

Корпорация Майкрософт предоставляет сценарий, который настраивает отношения доверия с проверяющей стороной, включая правила преобразования утверждений. Использовать этот сценарий необязательно, можно вручную выполнить необходимые команды.

Вспомогательный сценарий можно скачать в разделе [средств Azure Stack Hub](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) на сайте GitHub.

Если вы решили вручную выполнить команды, сделайте следующее.

1. Скопируйте приведенное ниже содержимое в TXT-файл (например, сохраните этот файл как c:\ClaimRules.txt) на экземпляре AD FS или элементе фермы своего центра обработки данных.

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Подтвердите, что проверка подлинности на основе Windows Forms для экстрасети и интрасети включена. Вы можете проверить, включена ли она, с помощью следующего командлета.

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Поддерживаемые строки пользовательских агентов службы WIA (встроенная проверка подлинности Windows) могут устареть для развертывания службы федерации Active Directory (AD FS), и в этом случае для поддержки современных клиентов потребуется их обновить. Подробнее об обновлении поддерживаемых строк пользовательских агентов в WIA можно прочитать в статье [Настройка интрасети на основе форм проверки подлинности для устройств, не поддерживающих WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br><br>В статье [Настройка политик аутентификации](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies) описана процедура включения политики проверки подлинности на основе форм.

3. Чтобы добавить отношения доверия с проверяющей стороной, выполните следующую команду Windows PowerShell на экземпляре AD FS или элементе фермы. Обязательно обновите конечную точку AD FS и укажите файл, созданный на шаге 1.

   **Для AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Для AD FS 2012 и AD FS 2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Чтобы настроить правила авторизации выдачи при использовании AD FS на основе Windows Server 2012 или Windows Server 2012 R2, необходимо использовать оснастку MMC AD FS.

4. При использовании браузера Internet Explorer или Microsoft Edge для доступа к Azure Stack Hub необходимо игнорировать привязки токенов. В противном случае попытка входа завершится сбоем. На экземпляре AD FS или элементе фермы выполните следующую команду.

   > [!note]  
   > Эта инструкция неприменима при использовании Windows Server 2012 или 2012 R2 AD FS. Для этих сред можно безопасно пропустить эту команду и продолжать интеграцию.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Создание имени субъекта-службы

Существует множество сценариев, в которых требуется использовать имя субъекта-службы для проверки аутентификации. Ниже приводятся некоторые примеры:

- использование интерфейса командной строки с развертыванием AD FS в Azure Stack Hub;
- использование пакета управления System Center для Azure Stack Hub при развертывании с AD FS;
- использование поставщиков ресурсов Azure Stack Hub при развертывании с AD FS;
- разные приложения;
- требуется поддержка неинтерактивного входа.

> [!Important]  
> AD FS поддерживает только сеансы интерактивного входа в систему. Если для автоматического сценария требуется неинтерактивный вход в систему, используйте имя субъекта-службы.

Дополнительные сведения о создании имени субъекта-службы см. в разделе [Создание субъекта-службы для AD FS](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Устранение неполадок

### <a name="configuration-rollback"></a>Откат конфигурации

Если возникла ошибка, которая оставила среду в состоянии, в котором аутентификация невозможна, можно выполнить откат.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Затем выполните следующий командлет.

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   После выполнения отката откатываются все изменения конфигурации. Возможна будет только аутентификация с использованием встроенного пользователя **CloudAdmin**.

   > [!IMPORTANT]
   > Необходимо настроить первоначального владельца для подписки поставщика по умолчанию.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Сбор дополнительных журналов

В случае сбоя при выполнении какого-либо из командлетов можно собрать дополнительные журналы, выполнив командлет `Get-Azurestacklogs`.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Затем выполните следующий командлет.

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>Дальнейшие действия

[Интеграция внешнего решения для мониторинга с Azure Stack](azure-stack-integrate-monitor.md)
