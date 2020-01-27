---
title: Создание запросов на подписывание сертификатов для Azure Stack Hub | Документация Майкрософт
description: Сведения о создании запросов на подписывание сертификатов для сертификатов PKI Azure Stack Hub в интегрированных системах Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 4b966ac4faec51dac06e39fab8804e295a9a40b1
ms.sourcegitcommit: 320eddb281a36d066ec80d67b103efad7d4f33c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76145841"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Создание запросов на подписывание сертификатов для Azure Stack Hub

Вы можете использовать средство проверки готовности Azure Stack Hub для создания запросов на подпись сертификата (CSR), подходящих для развертывания Azure Stack Hub. Сертификаты необходимо запросить, создать и проверить, а также выделить достаточно времени на их тестирование перед развертыванием. Средство можно получить из [коллекции PowerShell](https://aka.ms/AzsReadinessChecker).

Вы можете использовать средство проверки готовности Azure Stack Hub (AzsReadinessChecker) для запроса следующих сертификатов.

- **Стандартные запросы на сертификаты** согласно инструкциям по [созданию запроса на подпись сертификата](azure-stack-get-pki-certs.md#generate-certificate-signing-requests).
- **Платформа как услуга (PaaS).** Вы можете запросить имена PaaS для сертификатов, как описано в разделе [Необязательные сертификаты PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем создать запросы на подпись сертификатов PKI для развертывания Azure Stack Hub, необходимо убедиться, что система отвечает следующим требованиям:

- Инструмент проверки готовности Microsoft Azure Stack Hub
- Атрибуты сертификата:
  - Имя региона
  - Внешнее полное доменное имя (FQDN)
  - Тема
- Windows 10 или Windows Server 2016 или более поздней версии

  > [!NOTE]  
  > После получения сертификатов из центра сертификации действия, описанные в статье [Подготовка сертификатов PKI Azure Stack Hub для развертывания или смены секретов](azure-stack-prepare-pki-certs.md), необходимо будет выполнить в той же системе.

## <a name="generate-certificate-signing-requests"></a>Создание запросов на подпись сертификата

Для подготовки и проверки сертификатов PKI Azure Stack Hub выполните следующие действия.

1. Установите AzsReadinessChecker из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Объявите **субъект**. Пример:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Если указано общее имя, оно будет настроено для каждого запроса сертификата. Если общее имя не указано, то в запрос на сертификат будет включаться первое DNS-имя службы Azure Stack Hub.

3. Объявите имеющийся выходной каталог. Пример:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Объявление системы идентификаторов.

    Azure Active Directory (Azure AD).

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Служба федерации Active Directory (AD FS).

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > Этот параметр является обязательным только для развертывания CertificateType.

5. Объявите **имя региона** и **внешнее полное доменное имя**, которые предназначены для развертывания Azure Stack Hub.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > На основе `<regionName>.<externalFQDN>` создаются все внешние DNS-имена в Azure Stack Hub. В нашем примере используется портал `portal.east.azurestack.contoso.com`.  

6. Чтобы создать запросы на подпись сертификатов для развертывания, выполните следующее.

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Чтобы создать запросы на сертификаты для других служб Azure Stack Hub, измените значение `-CertificateType`. Пример:

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsCertificateSigningRequest -certificateType EventHubs -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Также в средах разработки и тестирования для создания одного запроса на сертификат с несколькими альтернативными именами субъекта можно добавить параметр и значение **-RequestType SingleCSR** (**не** рекомендуется для рабочих сред):

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Просмотрите выходные данные:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Отправьте созданный **REQ-файл** в центр сертификации (внутренний или общедоступный). В выходном каталоге **New-AzsCertificateSigningRequest** содержится запрос на подпись сертификатов, который необходимо отправить в центр сертификации. Каталог также содержит для справки дочерний каталог, содержащий INF-файлы, которые используются во время создания запроса сертификата. Убедитесь, что сертификаты в центре сертификации создаются с помощью запроса, который соответствует требованиям из статьи о [требованиях к PKI Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Дальнейшие действия

[Подготовка сертификатов PKI Azure Stack Hub для развертывания или смены секретов](azure-stack-prepare-pki-certs.md)
