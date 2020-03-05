---
title: Использование средства проверки шаблонов в Azure Stack Hub
description: Проверка шаблонов для развертывания в Azure Stack Hub с помощью средства проверки шаблонов.
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 4c545c60c0890f87c87108101a3e30ab4c87d16d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705293"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Использование средства проверки шаблонов в Azure Stack Hub

С помощью средства проверки шаблонов вы можете проверить, готовы ли [шаблоны](azure-stack-arm-templates.md) Azure Resource Manager к развертыванию в Azure Stack Hub. Средство проверки шаблона доступно в составе средств Azure Stack Hub в репозитории GitHub. Чтобы скачать средства Azure Stack Hub с GitHub, выполните действия, описанные в [здесь](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Обзор

Чтобы проверить шаблон, сначала нужно создать файл возможностей облака, а затем запустить средство проверки. Можно использовать приведенные ниже модули PowerShell из набора инструментов Azure Stack Hub.

- В папке **CloudCapabilities**: **AzureRM.CloudCapabilities.psm1** создает JSON-файл со списком возможностей облака, где перечисляются доступные в облаке Azure Stack Hub службы и версии.
- В папке **TemplateValidator**: **AzureRM.TemplateValidator.psm1** использует JSON-файл облачных компонентов для проверки шаблонов на возможность развертывания в Azure Stack Hub.

## <a name="build-the-cloud-capabilities-file"></a>Создание файла возможностей облака

Прежде чем использовать средство проверки шаблонов, запустите модуль PowerShell **AzureRM.CloudCapabilities**, чтобы создать файл JSON.

>[!NOTE]
> При обновлении интегрированной системы и добавлении новых служб или расширений виртуальной машины следует еще раз запустить этот модуль.

1. Убедитесь, что у вас есть подключение к Azure Stack Hub. Эти шаги можно выполнить на узле Пакета средств разработки Azure Stack (ASDK) или на рабочей станции, подключенной через [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).
2. Импортируйте модуль PowerShell **AzureRM.CloudCapabilities**.

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. С помощью командлета **Get-CloudCapabilities** вы можете получить версии служб и создать JSON-файл облачных компонентов. Если вы не укажете параметр `-OutputPath`, файл **AzureCloudCapabilities.json** будет создан в текущем каталоге. Укажите фактическое расположение Azure.

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Проверка шаблонов

Этот процесс позволяет проверить шаблоны с помощью модуля PowerShell **AzureRM.TemplateValidator**. Вы можете использовать собственные шаблоны или [шаблоны быстрого запуска Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates).

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

| Параметр | Описание | Обязательно |
| ----- | -----| ----- |
| `TemplatePath` | Указывает путь для рекурсивного поиска шаблонов Azure Resource Manager. | Да |
| `TemplatePattern` | Задает шаблон имени, по которому выбираются файлы. | нет |
| `CapabilitiesPath` | Указывает путь к JSON-файлу возможностей облака. | Да |
| `IncludeComputeCapabilities` | Включает оценку ресурсов IaaS, например размеров и расширений виртуальных машин. | нет |
| `IncludeStorageCapabilities` | Включает оценку ресурсов хранения, например типов SKU. | нет |
| `Report` | Указывает имя создаваемого HTML-файла отчета. | нет |
| `Verbose` | Выводит ошибки и предупреждения в консоль. | нет|

### <a name="examples"></a>Примеры

Этот пример проверяет все [шаблоны быстрого запуска Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates), скачанные в локальное хранилище. Пример также проверяет размеры и расширения виртуальной машины на соответствие возможностям ASDK.

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование шаблонов Azure Resource Manager в Azure Stack Hub](azure-stack-arm-templates.md)
- [Разработка шаблонов для Azure Stack Hub](azure-stack-develop-templates.md)
