---
title: Использование средства проверки шаблонов в Azure Stack | Документация Майкрософт
description: Проверьте свои шаблоны для развертывания в Azure Stack с помощью средства проверки шаблонов.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a5e86f0f0719e30ef693c736ac4c70f05830c3bb
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961663"
---
# <a name="use-the-template-validation-tool-in-azure-stack"></a>Использование средства проверки шаблонов в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью средства проверки шаблонов вы можете проверить, готовы ли [шаблоны](azure-stack-arm-templates.md) Azure Resource Manager к развертыванию в Azure Stack. Средство проверки шаблона доступно в составе средств Azure Stack в репозитории GitHub. Чтобы скачать средства Azure Stack с GitHub, выполните действия, описанные в [здесь](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Обзор

Чтобы проверить шаблон, сначала нужно создать файл возможностей облака, а затем запустить средство проверки. Можно использовать приведенные ниже модули PowerShell из набора инструментов Azure Stack.

- В папке **CloudCapabilities**: **AzureRM.CloudCapabilities.psm1** создает JSON-файл со списком возможностей облака, где перечисляются доступные в облаке Azure Stack службы и версии.
- В папке **TemplateValidator**: **AzureRM.TemplateValidator.psm1** использует JSON-файл облачных компонентов для проверки шаблонов на возможность развертывания в Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Создание файла возможностей облака

Прежде чем использовать средство проверки шаблонов, запустите модуль PowerShell **AzureRM.CloudCapabilities**, чтобы создать файл JSON.

>[!NOTE]
> При обновлении интегрированной системы и добавлении новых служб или расширений виртуальной машины следует еще раз запустить этот модуль.

1. Убедитесь, что у вас есть подключение к Azure Stack. Эти шаги можно выполнить на узле Пакета средств разработки Azure Stack (ASDK) или на рабочей станции, подключенной через [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).
2. Импортируйте модуль PowerShell **AzureRM.CloudCapabilities**.

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. С помощью командлета **Get-CloudCapabilities** вы можете получить версии служб и создать JSON-файл облачных компонентов. Если вы не укажете параметр `-OutputPath`, файл **AzureCloudCapabilities.json** будет создан в текущем каталоге. Укажите фактическое расположение Azure.

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Проверка шаблонов

Этот процесс позволяет проверить шаблоны с помощью модуля PowerShell **AzureRM.TemplateValidator**. Вы можете использовать собственные шаблоны или [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Импортируйте модуль **AzureRM.TemplateValidator.psm1** PowerShell.

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Запустите средство проверки шаблонов:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Сведения о всех предупреждениях и ошибках, полученные в процессе проверки шаблонов, выводятся в консоль PowerShell и записываются HTML-файл в исходном каталоге. На снимке экрана ниже показан пример отчета о проверке.

![Пример отчета о проверке шаблона](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Параметры

Командлет средства проверки шаблонов поддерживает следующие параметры.

| Параметр | ОПИСАНИЕ | Обязательно |
| ----- | -----| ----- |
| `TemplatePath` | Указывает путь для рекурсивного поиска шаблонов Azure Resource Manager. | Yes |
| `TemplatePattern` | Задает шаблон имени, по которому выбираются файлы. | Нет |
| `CapabilitiesPath` | Указывает путь к JSON-файлу возможностей облака. | Yes |
| `IncludeComputeCapabilities` | Включает оценку ресурсов IaaS, например размеров и расширений виртуальных машин. | Нет |
| `IncludeStorageCapabilities` | Включает оценку ресурсов хранения, например типов SKU. | Нет |
| `Report` | Указывает имя создаваемого HTML-файла отчета. | Нет |
| `Verbose` | Выводит ошибки и предупреждения в консоль. | Нет|

### <a name="examples"></a>Примеры

Этот пример проверяет все [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates), скачанные в локальное хранилище. Пример также проверяет размеры и расширения виртуальной машины на соответствие возможностям ASDK.

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Дополнительная информация

- [Use Azure Resource Manager templates in Azure Stack](azure-stack-arm-templates.md) (Использование шаблонов Resource Manager в Azure Stack)
- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
