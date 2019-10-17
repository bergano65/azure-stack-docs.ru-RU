---
title: Проверка сертификатов инфраструктуры открытых ключей Azure Stack для развертывания интегрированных систем Azure Stack | Документация Майкрософт
description: В этой статье описано, как проверить сертификаты PKI Azure Stack для интегрированных систем Azure Stack. В статье описывается использование средства проверки сертификатов Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 3823aa73d58af48c662690aa0d8e8a21180b4ed6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283238"
---
# <a name="validate-azure-stack-pki-certificates"></a>Проверка сертификатов PKI Azure Stack

Инструмент проверки готовности Azure Stack, описанный в этой статье, доступен в [коллекции PowerShell](https://aka.ms/AzsReadinessChecker). Вы можете использовать этот инструмент, чтобы проверить, что [сгенерированные сертификаты PKI](azure-stack-get-pki-certs.md) подходят для предварительного развертывания. Проверьте сертификаты, оставив достаточно времени для проверки и повторной выдачи сертификатов при необходимости.

Средство проверки готовности выполняет следующие проверки сертификата:

- **Чтение PFX-файла**.  
    Проверяет наличие допустимого PFX-файла и не защищена ли паролем общедоступная информация. 
- **Алгоритм подписи**.  
    Проверяет, не используется ли алгоритм подписи SHA1.
- **Закрытый ключ**.  
    Проверяет наличие закрытого ключа и то, экспортируется ли он с помощью атрибута локального компьютера. 
- **Цепочка сертификатов**.  
    Проверяет наличие и целостность цепочки сертификатов, включая самозаверяющиеся сертификаты.
- **DNS-имена**.  
    Проверяет, имеются ли в сети хранения данных соответствующие DNS-имена для каждой конечной точки или присутствует ли вспомогательный подстановочный знак.
- **Использование ключа**.  
    Проверяет, применяется ли цифровая подпись и шифрование при использовании ключа, а при расширенном использовании ключа проверяется аутентификация сервера и клиента.
- **Размер ключа**.  
    Проверяет, достигает ли размер ключа значения 2048.
- **Порядок в цепочке**.  
    Проверяет порядок других сертификатов, чтобы убедиться, что порядок правильный.
- **Другие сертификаты**.  
    Проверяет, не были ли упакованы в PFX-файл другие сертификаты (кроме соответствующего конечного сертификата и его цепочки).
- **Отсутствие профиля**  
    Проверяет, может ли новый пользователь скачать PFX-файл без загрузки профиля пользователя, что аналогично поведению групповых управляемых учетных записей службы во время обслуживания сертификатов.

> [!IMPORTANT]  
> PFX-файл и пароль сертификата PKI должны быть конфиденциальными.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать проверку сертификатов PKI для развертывания Azure Stack, необходимо убедиться, что в системе присутствуют следующие компоненты и установлена нужная ОС.

- Инструмент проверки готовности Microsoft Azure Stack
- Сертификаты SSL, экспортированные в соответствии с [инструкциями по подготовке](azure-stack-prepare-pki-certs.md).
- Файл DeploymentData.json.
- Windows 10 или Windows Server 2016;

## <a name="perform-core-services-certificate-validation"></a>Выполнение проверки сертификата основных служб

Чтобы подготовить и проверить сертификаты PKI Azure Stack для развертывания и смены секретов, выполните следующие действия:

1. Установите **AzsReadinessChecker** из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Создайте структуру каталога сертификатов. В приведенном ниже примере вы можете изменить `<c:\certificates>` на новый путь каталога по своему усмотрению.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Службы федерации Active Directory (AD FS) и Graph необходимы, если вы используете AD FS как свою систему идентификации. Например:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Поместите сертификаты в соответствующие каталоги, созданные на предыдущем шаге. Например:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. В окне PowerShell измените значения **RegionName** и **FQDN** в соответствии со средой Azure Stack и выполните команду ниже:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Проверьте выходные данные. Все сертификаты должны пройти все проверки. Например:

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
```

### <a name="known-issues"></a>Известные проблемы

**Признак**. Проверки пропускаются.

**Причина.** AzsReadinessChecker пропускает определенные проверки, если не установлена зависимость.

 - Проверка других сертификатов пропускается, если цепочка сертификатов нецелостна.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Решение**. Следуйте указаниям из руководства по инструментам в разделе сведений о каждом наборе проверок сертификата.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Выполнение проверки сертификата платформы как услуги

Выполните шаги ниже, чтобы подготовить и проверить сертификаты PKI Azure Stack для сертификатов платформы как услуги (PaaS), если запланированы развертывания SQL/MySQL или службы приложений.

1.  Установите **AzsReadinessChecker** из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Создайте вложенную хэш-таблицу, содержащую пути и пароль для каждого сертификата PaaS, который нужно проверить. В окне PowerShell выполните команду ниже:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Измените значения **RegionName** и **FQDN** в соответствии со своей средой Azure Stack, чтобы начать проверку. Далее выполните:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Проверьте выходные данные. Все сертификаты должны пройти все проверки.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Сертификаты

| Каталог | Сертификат |
| ---    | ----        |
| acsBlob | wildcard_blob_\<регион>_\<внешнее_полное_доменное_имя> |
| ACSQueue  |  wildcard_queue_\<регион>_\<внешнее_полное_доменное_имя> |
| ACSTable  |  wildcard_table_\<регион>_\<внешнее_полное_доменное_имя> |
| Хост-процесс для расширений администратора  |  wildcard_adminhosting_\<регион>_\<внешнее_полное_доменное_имя> |
| Портал администрирования  |  adminportal_\<регион>_\<внешнее_полное_доменное_имя> |
| ARM Admin  |  adminmanagement_\<регион>_\<внешнее_полное_доменное_имя> |
| ARM Public  |  management_\<регион>_\<внешнее_полное_доменное_имя> |
| Хранилище ключей  |  wildcard_vault_\<регион>_\<внешнее_полное_доменное_имя> |
| Внутреннее хранилище Key Vault  |  wildcard_adminvault_\<регион>_\<внешнее_полное_доменное_имя> |
| Общедоступный хост-процесс для расширений  |  wildcard_hosting_\<регион>_\<внешнее_полное_доменное_имя> |
| Общедоступный портал  |  portal_\<регион>_\<внешнее_полное_доменное_имя> |

## <a name="using-validated-certificates"></a>Использование проверенных сертификатов

Как только ваши сертификаты будут проверены с помощью AzsReadinessChecker, их можно использовать в развертывании Azure Stack или для смены секретов Azure Stack. 

 - В целях развертывания безопасно передайте свои сертификаты специалисту по развертыванию, чтобы он смог скопировать их на узел развертывания, как указано в [документации по требованиям PKI для Azure Stack](azure-stack-pki-certs.md).
 - В целях смены секретов вы можете использовать сертификаты, чтобы обновить старые сертификаты для общедоступных конечных точек инфраструктуры среды Azure Stack, следуя инструкциям, приведенным в статье о [смене секретов в Azure Stack](azure-stack-rotate-secrets.md).
 - Для служб PaaS можно использовать сертификаты для установки поставщиков ресурсов SQL, MySQL и служб приложений в Azure Stack, ознакомившись со статьей [Общие сведения о предложении служб в Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Дополнительная информация

[Интеграция центра обработки данных Azure Stack: идентификация](azure-stack-integrate-identity.md)
