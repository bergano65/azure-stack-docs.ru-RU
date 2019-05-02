---
title: Создание сертификатов инфраструктуры открытых ключей Azure Stack для развертывания интегрированных систем Azure Stack | Документация Майкрософт
description: В этой статье описано, как развернуть сертификаты PKI Azure Stack для интегрированных систем Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: e0556eb5cc3d0f140067a4e3b4a9054a47b91417
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307130"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Создание запроса на подпись сертификата Azure Stack

Вы можете использовать средство проверки готовности Azure Stack для создания запросов на подпись сертификата (CSR), подходящих для развертывания Azure Stack. Сертификаты необходимо запросить, создать и проверить, а также выделить достаточно времени на их тестирование перед развертыванием. Средство можно получить из [коллекции PowerShell](https://aka.ms/AzsReadinessChecker).

Вы можете использовать средство проверки готовности Azure Stack (AzsReadinessChecker) для запроса следующих сертификатов.

- **Стандартные запросы на сертификаты** согласно [Создание запроса на подпись сертификата Azure Stack](azure-stack-get-pki-certs.md).
- **Платформа как услуга (PaaS).** Можно запросить имена PaaS для сертификатов, как описано в разделе [Необязательные сертификаты PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем создать запросы на подпись сертификатов PKI для развертывания Azure Stack, необходимо убедиться, что в системе присутствуют приведенные ниже компоненты и установлена нужная ОС.

- Инструмент проверки готовности Microsoft Azure Stack
- Атрибуты сертификата:
  - Имя региона
  - Внешнее полное доменное имя (FQDN)
  - Субъект
- Windows 10 или Windows Server 2016;

  > [!NOTE]  
  > После получения сертификатов из центра сертификации действия, описанные в разделе [Подготовка сертификатов PKI Azure Stack](azure-stack-prepare-pki-certs.md), необходимо будет выполнить в той же системе.

## <a name="generate-certificate-signing-requests"></a>Создание запроса на подпись сертификата

Для подготовки и проверки сертификатов PKI Azure Stack выполните следующие действия:

1. Установите AzsReadinessChecker из командной строки PowerShell (версии 5.1 или более поздней), выполнив следующий командлет:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Объявите **subject** в качестве упорядоченного словаря. Например: 

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Если указано общее имя (CN), оно будет использоваться в качестве первого DNS-имени запроса сертификата.

3. Объявите имеющийся выходной каталог. Например: 

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Объявление системы идентификаторов

    Azure Active Directory

    ```powershell
    $IdentitySystem = "AAD"
    ```

    службы федерации Active Directory;

    ```powershell
    $IdentitySystem = "ADFS"
    ```

5. Объявите **имя региона** и **внешнее полное доменное имя**, которые предназначены для развертывания Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > На основе `<regionName>.<externalFQDN>` создаются все внешние DNS-имена в Azure Stack. В этом примере используется портал `portal.east.azurestack.contoso.com`.  

6. Чтобы создать запросы на подпись сертификатов для каждого DNS-имени, выполните следующую команду:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Для включения служб PaaS укажите параметр ```-IncludePaaS```

7. Также в средах разработки и тестирования для создания одного запроса на сертификат с несколькими альтернативными именами субъекта можно добавить параметр и значение **-RequestType SingleCSR** (**не** рекомендуется для рабочих сред):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Для включения служб PaaS укажите параметр ```-IncludePaaS```

8. Просмотрите выходные данные:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Отправьте созданный **REQ-файл** в центр сертификации (внутренний или общедоступный).  В выходном каталоге **New-AzsCertificateSigningRequest** содержится запрос на подпись сертификатов, который необходимо отправить в центр сертификации.  Каталог также содержит для справки дочерний каталог, содержащий INF-файлы, которые используются во время создания запроса сертификата. Убедитесь, что сертификаты в центре сертификации создаются с помощью запроса, который соответствует требованиям из статьи [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Дополнительная информация

[Подготовка сертификатов PKI Azure Stack](azure-stack-prepare-pki-certs.md)
