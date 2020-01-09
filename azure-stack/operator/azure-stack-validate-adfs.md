---
title: Проверка интеграции AD FS с Azure Stack
description: Применение средства проверки готовности Azure Stack для проверки интеграции AD FS с Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 9945113e778f69000a720d5f5045f691f439d076
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718544"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Проверка интеграции AD FS с Azure Stack

Средство проверки готовности Azure Stack (AzsReadinessChecker) позволяет убедиться, что ваша среда готова к интеграции служб федерации Active Directory (AD FS) с Azure Stack. Проверьте интеграцию AD FS перед началом интеграции центра обработки данных или до развертывания Azure Stack.

Средство проверки готовности позволяет определить:

* *Метаданные федерации* содержат допустимые элементы XML для федерации.
* Можно получить *SSL-сертификат ADF S* и создать цепочку доверия. В метке службы AD FS должны доверять цепочке SSL-сертификатов. Сертификат должен быть подписан тем же *центром сертификации*, что и сертификаты развертывания Azure Stack, или доверенным партнером корневого центра. Полный список доверенных партнеров корневых центров приведен на сайте [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* *Сертификат для подписи AD FS* является доверенным и не истекает.

Дополнительные сведения о требованиях к интеграции центра обработки данных Azure Stack см. в статье [Интеграция центра обработки данных Azure Stack — идентификатор](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Получение средства проверки готовности

Скачайте последнюю версию средства проверки готовности Azure Stack (AzsReadinessChecker) из [коллекции PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>предварительные требования

Убедитесь, что выполнены указанные ниже предварительные требования.

**На компьютере, где запускается это средство:**

* Необходимо установить Windows 10 или Windows Server 2016 и обеспечить подключение к домену.
* Необходимо установить PowerShell 5.1 или более поздней версии. Чтобы проверить используемую версию, выполните приведенную команду PowerShell и проверьте значения *Major* (основной номер версии) и *Minor* (дополнительный номер версии).  
   > `$PSVersionTable.PSVersion`
* Необходимо установить последнюю версию [средства проверки готовности Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker).

**Среда служб федерации Active Directory:**

Вам потребуется по крайней мере одна из следующих форм метаданных:

* URL-адрес метаданных федерации AD FS. Например, `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* XML-файл метаданных федерации. Например, FederationMetadata.xml

## <a name="validate-ad-fs-integration"></a>Проверка интеграции AD FS

1. На компьютере, который соответствует всем предварительным требованиям, откройте командную строку PowerShell с правами администратора и выполните следующую команду, чтобы установить AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. В командной строке PowerShell выполните приведенную ниже команду, чтобы начать проверку. Укажите в качестве значения **-CustomADFSFederationMetadataEndpointUri** универсальный код ресурса (URI) для метаданных федерации.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Когда средство завершит работу, просмотрите выходные данные. Убедитесь, что требования к интеграции AD FS выполнены. При успешном завершении проверки отобразится примерно следующий результат.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Datacenter Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

В рабочих средах тестирование цепочки сертификатов доверия от рабочей станции оператора нельзя считать абсолютно показательным с точки зрения доверия PKI в инфраструктуре Azure Stack. Общедоступный виртуальный IP-адрес сети на метке Azure Stack требует подключения к CRL для инфраструктуры PKI.

## <a name="report-and-log-file"></a>Файлы отчета и журнала

При каждом запуске проверки все результаты сохраняются в файлах **AzsReadinessChecker.log** и **AzsReadinessCheckerReport.json**. Расположение этих файлов указывается в PowerShell вместе с результатами проверки.

Файлы проверки помогут передать сведения о состоянии другим заинтересованным лицам перед развертыванием Azure Stack или для исследования проблем, обнаруженных при проверке. В обоих файлах сохраняются результаты каждой очередной проверки. В отчете содержатся подтверждения команды развертывания для конфигурации удостоверений. Файл журнала поможет командам развертывания или поддержки диагностировать проблемы с проверкой.

По умолчанию оба файла записываются в `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Используйте следующую команду:

* **-OutputPath**. Параметр *path* в конце командной строки позволяет задать другое расположение отчетов.
* **-CleanReport**. В конце команды, чтобы удалить из файла AzsReadinessCheckerReport.json сведения о предыдущем отчете. Дополнительные сведения об отчетах проверки Azure Stack можно найти [здесь](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Ошибки при проверке

Если проверка завершается ошибкой, сведения о сбое отображаются в окне PowerShell. Кроме того, сведения записываются в файл *AzsReadinessChecker.log*.

В следующих примерах приведены некоторые рекомендации по устранению распространенных ошибок проверки.

### <a name="command-not-found"></a>Команда не найдена

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Причина.** При автозагрузке PowerShell не удалось правильно загрузить модуль проверки готовности.

**Решение**. Импортируйте модуль проверки готовности явным образом. Скопируйте и вставьте приведенный ниже код в PowerShell и укажите в параметре \<version\> номер текущей установленной версии.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Дальнейшие действия

[Просмотр отчета о готовности](azure-stack-validation-report.md)  
[Общие рекомендации по интеграции Azure Stack](azure-stack-datacenter-integration.md)  
