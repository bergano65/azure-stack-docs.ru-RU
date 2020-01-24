---
title: Проверка сертификатов инфраструктуры открытых ключей Azure Stack Hub для развертывания интегрированных систем Azure Stack Hub | Документация Майкрософт
description: Сведения о проверке сертификатов PKI Azure Stack Hub для интегрированных систем Azure Stack Hub. В этой статье описывается использование средства проверки сертификатов Azure Stack Hub.
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
ms.openlocfilehash: 23225b21d1dc3074c69cefa2af23a99b634a7a73
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812864"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Проверка сертификатов PKI Azure Stack Hub

Инструмент проверки готовности Azure Stack Hub, описанный в этой статье, доступен в [коллекции PowerShell](https://aka.ms/AzsReadinessChecker). Вы можете использовать этот инструмент, чтобы проверить, что [сгенерированные сертификаты PKI](azure-stack-get-pki-certs.md) подходят для предварительного развертывания. Проверьте сертификаты, оставив достаточно времени для проверки и повторной выдачи сертификатов при необходимости.

Средство проверки готовности выполняет следующие проверки сертификата:

- **Анализ PFX**  
    Проверяет наличие допустимого PFX-файла, правильность пароля и наличие защиты паролем для общедоступной информации. 
- **Дата окончания срока действия**  
    Проверяет наличие минимального срока действия в 7 дней. 
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

> [!IMPORTANT]  
> PFX-файл и пароль сертификата PKI должны быть конфиденциальными.

## <a name="prerequisites"></a>предварительные требования

Прежде чем начать проверку сертификатов PKI для развертывания Azure Stack Hub, необходимо убедиться, что в системе присутствуют следующие компоненты и установлена нужная ОС.

- Инструмент проверки готовности Microsoft Azure Stack Hub
- Сертификаты SSL, экспортированные в соответствии с [инструкциями по подготовке](azure-stack-prepare-pki-certs.md).
- Файл DeploymentData.json.
- Windows 10 или Windows Server 2016;

## <a name="perform-core-services-certificate-validation"></a>Выполнение проверки сертификата основных служб

Чтобы подготовить и проверить сертификаты PKI Azure Stack Hub для развертывания и смены секретов, выполните следующие действия:

1. Установите **AzsReadinessChecker** из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Создайте структуру каталога сертификатов. В приведенном ниже примере вы можете изменить `<C:\Certificates\Deployment>` на новый путь каталога по своему усмотрению.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Службы федерации Active Directory (AD FS) и Graph необходимы, если вы используете AD FS как свою систему идентификации. Пример:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Поместите сертификаты в соответствующие каталоги, созданные на предыдущем шаге. Пример:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. В окне PowerShell измените значения **RegionName** и **FQDN** в соответствии со средой Azure Stack Hub и выполните команду ниже:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Проверьте выходные данные. Все сертификаты должны пройти все проверки. Пример:

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    Чтобы проверить сертификаты для других служб Azure Stack Hub, измените значение ```-CertificateType```. Пример:

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHub
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
Каждая папка должна содержать один PFX-файл для каждого типа сертификата. Если тип сертификата поддерживает несколько сертификатов, то нужны вложенные папки для каждого отдельного сертификата с учетом имени.  В следующем примере кода показана структура папок и сертификатов для всех типов сертификатов, а также соответствующее значение для ```-CertificateType``` и ```-CertificatePath```.
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHub            # Invoke-AzsCertificateValidation `
                |       eventhub.pfx    #   -CertificateType EventHub `
                |                       #   -CertificatePath C:\Certificates\EventHub
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
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

Как только ваши сертификаты будут проверены с помощью AzsReadinessChecker, их можно использовать в развертывании Azure Stack Hub или для смены секретов Azure Stack Hub. 

 - В целях развертывания безопасно передайте свои сертификаты специалистам по развертыванию, чтобы они могли скопировать их на узел развертывания, как указано в [документации по требованиям PKI для Azure Stack Hub](azure-stack-pki-certs.md).
 - В целях смены секретов вы можете использовать сертификаты, чтобы обновить старые сертификаты для общедоступных конечных точек инфраструктуры среды Azure Stack Hub, следуя инструкциям, приведенным в [документации по смене секретов в Azure Stack Hub](azure-stack-rotate-secrets.md).
 - Если вы используете службы PaaS, вы можете применить сертификаты для установки поставщиков ресурсов SQL, MySQL и Служб приложений в Azure Stack Hub, выполнив инструкции из [документации по обзору предложения служб в Azure Stack Hub](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

[Интеграция центра обработки данных Azure Stack: идентификация](azure-stack-integrate-identity.md)
