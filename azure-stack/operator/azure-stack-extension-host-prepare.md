---
title: Подготовка хост-процесса для расширений в Azure Stack Hub | Документация Майкрософт
description: Узнайте, как подготовить Azure Stack Hub для хост-процесса для расширений, который автоматически включается с помощью пакета обновления Azure Stack Hub более поздней версии, чем 1808.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 10/02/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 45a26354edb7939a5fbc241bb5bcaf5d9db8edf3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818396"
---
# <a name="prepare-for-extension-host-in-azure-stack-hub"></a>Подготовка хост-процесса для расширений в Azure Stack Hub

Хост-процесс для расширений защищает Azure Stack Hub путем уменьшения количества необходимых портов TCP/IP. В этой статье рассматривается подготовка Azure Stack Hub для хост-процесса для расширений, который автоматически включается с помощью пакета обновления Azure Stack Hub более поздней версии, чем 1808. Эта статья относится к обновлениям Azure Stack Hub 1808, 1809 и 1811.

## <a name="certificate-requirements"></a>Требования к сертификатам

Хост-процесс для расширений реализует два новых пространства имен доменов, чтобы гарантировать уникальные записи узла для каждого расширения портала. Для обеспечения безопасного обмена данными новым доменным пространствам имен требуются два дополнительных групповых сертификата.

В таблице показаны новые пространства имен и связанные с ними сертификаты:

| Папка развертывания | Требуемый субъект сертификата и альтернативное имя субъекта | Область (для каждого региона) | Пространство имен поддомена |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Хост-процесс для расширений администратора | *.adminhosting.\<регион>.\<FQDN> (групповые SSL-сертификаты) | Хост-процесс для расширений администратора | adminhosting.\<регион>.\<FQDN> |
| Общедоступный хост-процесс для расширений | *.hosting.\<регион>.\<FQDN> (групповые SSL-сертификаты) | Общедоступный хост-процесс для расширений | hosting.\<регион>.\<FQDN> |

Подробные требования к сертификатам см. в статье [Требования к сертификатам инфраструктуры открытых ключей Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Создание запроса на подпись сертификата

Инструмент проверки готовности Azure Stack Hub позволяет создать запрос на подпись сертификата для двух новых обязательных SSL-сертификатов. Выполните действия, описанные в статье [Создание запросов на подписывание сертификатов для Azure Stack Hub](azure-stack-get-pki-certs.md).

> [!Note]  
> Можно пропустить этот шаг в зависимости от того, как вы запросили SSL-сертификаты.

## <a name="validate-new-certificates"></a>Проверка новых сертификатов

1. Откройте сеанс PowerShell с повышенными правами на узле жизненного цикла аппаратного обеспечения или рабочей станции управления Azure Stack Hub.
2. Запустите следующий командлет, чтобы установить инструмент проверки готовности Azure Stack Hub:

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Выполните следующий сценарий, чтобы создать нужную структуру папки:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > При развертывании с помощью служб федерации Azure Active Directory (AD FS) каталоги ниже должны быть добавлены в **$directories** в сценарии `ADFS`, `Graph`.

4. Поместите имеющиеся сертификаты, которые вы сейчас используете в Azure Stack Hub, в соответствующие каталоги. Например, поместите сертификат **ARM администратора** в папку `Arm Admin`. Затем поместите только что созданные сертификаты размещения в каталоги `Admin extension host` и `Public extension host`.
5. Чтобы запустить проверку сертификата, выполните следующий командлет:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Проверьте выходные данные. Все сертификаты должны пройти все проверки.


## <a name="import-extension-host-certificates"></a>Импорт сертификатов хост-процесса для расширений

Для выполнения следующих шагов используйте компьютер, который может подключиться к привилегированной конечной точке Azure Stack Hub. Обеспечьте доступ к новым файлам сертификатов с этого компьютера.

1. Для выполнения следующих шагов используйте компьютер, который может подключиться к привилегированной конечной точке Azure Stack Hub. Проверьте доступ к новым файлам сертификатов с этого компьютера.
2. Откройте интегрированную среду сценариев PowerShell, чтобы выполнить следующие блоки сценария.
3. Импортируйте сертификат для конечной точки размещения администратора.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Импортируйте сертификат для конечной точки размещения.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Обновление конфигурации DNS

> [!Note]  
> Этот шаг не является обязательным, если вы использовали делегирование зоны DNS для интеграции DNS.
Если записи A отдельного узла были настроены для публикации конечных точек Azure Stack Hub, вам нужно создать две дополнительные записи A узла:

| IP-адрес | Имя узла | Тип |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | Объект |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | Объект |

Выделенные IP-адреса можно получить с помощью привилегированной конечной точки, выполнив командлет **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Порты и протоколы

В статье [Публикация служб Azure Stack Hub в центре обработки данных](azure-stack-integrate-endpoints.md) описываются порты и протоколы, которым для публикации Azure Stack Hub перед развертыванием хост-процесса для расширений требуется входящая связь.

### <a name="publish-new-endpoints"></a>Публикация новых конечных точек

Есть две новые конечные точки, которые необходимо опубликовать через брандмауэр. Выделенные IP-адреса из пула общедоступных виртуальных IP-адресов можно получить, используя следующий код, который должен выполняться с помощью [привилегированной конечной точки среды](azure-stack-privileged-endpoint.md) Azure Stack Hub.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Hub Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Пример выходных данных

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Внесите это изменение, а затем включите хост-процесс для расширений. Это обеспечит непрерывную доступность порталов Azure Stack Hub.

| Конечная точка (виртуальный IP-адрес) | Протокол | порты; |
|----------------|----------|-------|
| Размещение администратора | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Обновление имеющихся правил публикации (после включения хост-процесса для расширений)

> [!Note]  
> Включение хост-процесса для расширений в пакете обновления Azure Stack Hub 1808 пока **недоступно**. Таким образом, вы можете подготовиться для хост-процесса для расширений, импортировав требуемые сертификаты. Порты можно закрыть только после автоматического включения хост-процесса для расширений с помощью пакета обновления Azure Stack Hub версии, вышедшей после обновления 1808.

В имеющихся правилах брандмауэра нужно закрыть следующие порты конечных точек.

> [!Note]  
> Эти порты рекомендуется закрыть после успешной проверки.

| Конечная точка (виртуальный IP-адрес) | Протокол | порты; |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Портал (для администратора) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Портал (для пользователя) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (для администратора) | HTTPS | 30024 |
| Azure Resource Manager (для пользователя) | HTTPS | 30024 |

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со сведениями об [интеграции брандмауэра](azure-stack-firewall.md).
- Ознакомьтесь со сведениями о [создании запроса на подпись сертификата Azure Stack Hub](azure-stack-get-pki-certs.md).
