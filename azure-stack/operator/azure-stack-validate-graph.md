---
title: Проверка интеграции Azure Graph с Azure Stack Hub
description: Применение средства проверки готовности Azure Stack Hub для проверки интеграции Graph с Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 29cc035e66039d09e761410808098d57f0b1927f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882629"
---
# <a name="validate-graph-integration-for-azure-stack-hub"></a>Проверка интеграции Azure Graph для Azure Stack Hub

Средство проверки готовности Azure Stack Hub (AzsReadinessChecker) позволяет убедиться, что ваша среда готова к интеграции Azure Graph с Azure Stack Hub. Проверьте интеграцию Graph перед началом интеграции центра обработки данных или до развертывания Azure Stack Hub.

Средство проверки готовности позволяет определить:

* наличие прав на запрос Active Directory у учетных данных для учетной записи службы, созданной для интеграции Azure Graph;
* возможность разрешения *глобального каталога* и обращения к нему;
* возможность разрешения центра распространения ключей (KDC) и обращения к нему;
* наличие необходимого сетевого подключения.

Дополнительные сведения о требованиях к интеграции центра обработки данных Azure Stack Hub см. в статье [Интеграция удостоверения AD FS с центром обработки данных Azure Stack Hub](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Получение средства проверки готовности

Скачайте последнюю версию средства проверки готовности Azure Stack Hub (AzsReadinessChecker) из [коллекции PowerShell](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Предварительные требования

Убедитесь, что выполнены указанные ниже предварительные требования.

**На компьютере, где запускается это средство:**

* Необходимо установить Windows 10 или Windows Server 2016 и обеспечить подключение к домену.
* Необходимо установить PowerShell 5.1 или более поздней версии. Чтобы проверить используемую версию, выполните приведенную команду PowerShell и проверьте значения *Major* (основной номер версии) и *Minor* (дополнительный номер версии).  
   > `$PSVersionTable.PSVersion`
* Необходимо установить модуль PowerShell для Active Directory.
* Необходимо установить последнюю версию средства [проверки готовности Microsoft Azure Stack Hub](https://aka.ms/AzsReadinessChecker).

**В среде Active Directory:**

* Определите имя пользователя и пароль для учетной записи для службы Graph в существующем экземпляре Active Directory.
* Определите полное доменное имя корня леса Active Directory.

## <a name="validate-the-graph-service"></a>Проверка службы Graph

1. На компьютере, который соответствует всем предварительным требованиям, откройте командную строку PowerShell с правами администратора и выполните следующую команду, чтобы установить AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. В командной строке PowerShell выполните следующую команду, чтобы задать переменную *$graphCredential* для учетной записи Graph. Замените `contoso\graphservice` нужной учетной записью, используя формат `domain\username`.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. В командной строке PowerShell выполните приведенную ниже команду, чтобы начать проверку службы Graph. В качестве значения параметра **-ForestFQDN** укажите полное доменное имя корня леса.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Когда средство завершит работу, просмотрите выходные данные. Убедитесь, что требованиям к интеграции Graph выполнены. При успешном завершении проверки отобразится примерно следующий результат.

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

В рабочей среде проверка сетевого подключения с рабочей станции оператора не может считаться достаточной для определения наличия подключения к Azure Stack Hub. Виртуальной общедоступной сети IP-адресов для отметки Azure Stack Hub потребуется подключение для трафика LDAP, чтобы выполнить интеграцию удостоверений.

## <a name="report-and-log-file"></a>Файлы отчета и журнала

При каждом запуске проверки все результаты сохраняются в файлах **AzsReadinessChecker.log** и **AzsReadinessCheckerReport.json**. Расположение этих файлов указывается в PowerShell вместе с результатами проверки.

Файлы проверки помогут передать сведения о состоянии другим заинтересованным лицам перед развертыванием Azure Stack Hub или для исследования проблем, обнаруженных при проверке. В обоих файлах сохраняются результаты каждой очередной проверки. В отчете содержатся подтверждения команды развертывания для конфигурации удостоверений. Файл журнала поможет командам развертывания или поддержки диагностировать проблемы с проверкой.

По умолчанию оба файла записываются в `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Используйте следующую команду:

* **-OutputPath**. Параметр *path* в конце командной строки позволяет задать другое расположение отчетов.
* **-CleanReport**. В конце команды, чтобы удалить из файла *AzsReadinessCheckerReport.json* сведения о предыдущем отчете. Дополнительные сведения об отчетах проверки Azure Stack Hub можно найти [здесь](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Ошибки при проверке

Если проверка завершается ошибкой, сведения о сбое отображаются в окне PowerShell. Кроме того, сведения записываются в файл *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Дальнейшие действия

[Просмотр отчета о готовности](azure-stack-validation-report.md)  
[Планирование интеграции центра обработки данных для интегрированных систем Azure Stack Hub](azure-stack-datacenter-integration.md)  
