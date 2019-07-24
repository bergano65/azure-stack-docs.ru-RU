---
title: Различия между Azure и Azure Stack при использовании служб и создании приложений | Документация Майкрософт
description: Узнайте о различиях между Azure и Azure Stack при использовании служб и создании приложений.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/17/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a7a61e8eef33ee6a6efb87001504fe5234e3cf16
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303153"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Различия между Azure и Azure Stack при использовании служб и создании приложений

Если вы используете службы или создаете приложения в Azure Stack, важно иметь представление о различиях между Azure Stack и Azure. В этой статье описаны различные возможности и ключевые моменты, которые необходимо учитывать при выборе Azure Stack в качестве гибридной облачной среды разработки.

## <a name="overview"></a>Обзор

Azure Stack ― это гибридная облачная платформа, позволяющая использовать службы Azure из центра обработки данных вашей компании или поставщика услуг. Можно создать приложение в Azure Stack и развернуть его в Azure Stack, Azure или гибридное облако Azure.

У оператора Azure Stack можно узнать, какие службы доступны для использования и как получить поддержку. Эти службы предоставляются в составе настраиваемых планов и предложений.

В технической документации по Azure предполагается, что приложения разрабатываются для службы Azure, а не для Azure Stack. При разработке и развертывании приложений в Azure Stack важно учитывать несколько важных различий.

* Azure Stack предоставляет подмножество служб и функций, доступных в Azure.
* Компания или поставщик услуг могут самостоятельно выбрать, какие службы они будут предоставлять. Это могут быть настраиваемые службы или приложения. Также они могут предоставлять собственные версии документации.
* Необходимо использовать правильные конечные точки, предназначенные для работы с Azure Stack (например, URL-адрес портала и конечную точку Azure Resource Manager).
* Необходимо использовать версии PowerShell и API, которые поддерживаются в Azure Stack. Только в этом случае можно гарантировать, работу приложений в Azure Stack и в Azure.

## <a name="cheat-sheet-high-level-differences"></a>Краткий справочник. Основные различия

В следующей таблице перечислены основные различия между Azure Stack и Azure. Не забывайте о них, когда осуществляете разработку для Azure Stack или используете службы Azure Stack.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

| Область | Azure (глобальная) | Azure Stack |
| -------- | ------------- | ----------|
| Кто оператор? | Microsoft | Ваша организация или поставщик услуг.|
| Куда обращаться для получения поддержки? | Microsoft | В интегрированной системе за поддержкой следует обращаться к оператору Azure Stack (это может быть ваша организация или поставщик услуг).<br><br>Если вы используете Пакет средств разработки Azure Stack (ASDK), посетите [форумы Майкрософт](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Так как пакет разработки предлагается как среда для оценки, служба поддержки корпорации Майкрософт не предоставляет для него официальную поддержку.
| Доступные службы | См. список [продуктов Azure](https://azure.microsoft.com/services/?b=17.04b). Выбор доступных служб зависит от региона Azure. | Azure Stack поддерживает некоторое подмножество служб Azure. Конкретный набор зависит от политики вашей организации или поставщика услуг.
| Конечная точка Azure Resource Manager* | https://management.azure.com | При работе с интегрированной системой Azure Stack используйте конечную точку, предоставленную оператором Azure Stack.<br><br>Для пакета разработки используйте следующий адрес: https://management.local.azurestack.external.
| URL-адрес портала* | [https://portal.azure.com](https://portal.azure.com) | При работе с интегрированной системой Azure Stack используйте URL-адрес, предоставленный оператором Azure Stack.<br><br>Для пакета SDK используйте следующий адрес: https://portal.local.azurestack.external.
| Регион | Вы можете самостоятельно выбрать регион для развертывания. | В интегрированной системе Azure Stack используется регион, доступный в этой системе.<br><br>В пакете разработки всегда используется только **локальный** регион.
| Группы ресурсов | Группа ресурсов может размещаться в нескольких регионах. | Как в интегрированной системе, так и в пакете SDK доступен только один регион.
|Поддержка пространств имен, типов ресурсов и версий API | Поддерживается последняя версия и прежние версии, о прекращении поддержки которых не было официально объявлено. | Azure Stack поддерживает только определенные версии. См. также раздел [Требования к версиям](#version-requirements) в этой статье.
| | |

* Если вы — оператор Azure Stack, дополнительные сведения вы можете получить в статьях об [использовании портала администрирования](../operator/azure-stack-manage-portals.md) и [основных понятиях администрирования](../operator/azure-stack-manage-basics.md).

## <a name="helpful-tools-and-best-practices"></a>Полезные средства и рекомендации

Корпорация Майкрософт предоставляет средства и рекомендации, которые помогут вам в разработке приложений для Azure Stack.

| Рекомендации | Справочники |
| -------- | ------------- |
| Установите подходящие инструментальные средства на рабочей станции разработчика. | - [Установка PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Скачивание средств](../operator/azure-stack-powershell-download.md)<br>- [Настройка PowerShell](azure-stack-powershell-configure-user.md)<br>- [Установка Visual Studio](azure-stack-install-visual-studio.md) 
| Изучите информацию по следующим темам:<br>рекомендации по использованию шаблона Azure Resource Manager;<br>как найти шаблоны для быстрого начала работы;<br>использование модуля политики, который позволяет применять Azure для разработки решений в Azure Stack. | [Разработка для Azure Stack](azure-stack-developer.md) | 
| Изучите и соблюдайте рекомендации для шаблонов. | [Шаблоны Resource Manager для быстрого начала работы](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
| | |

## <a name="version-requirements"></a>Требования к версиям

Azure Stack поддерживает конкретные версии Azure PowerShell и API служб Azure. Следует использовать именно эти поддерживаемые версии, чтобы приложение можно было развернуть как в Azure Stack, так и в Azure.

Чтобы гарантировать правильность версии Azure PowerShell, используйте [профили версий API](azure-stack-version-profiles.md). Чтобы определить самый свежий профиль версии API, который можно использовать, узнайте номер используемой сборки Azure Stack. Эти сведения можно получить у администратора Azure Stack.

> [!NOTE]
> Если вы используете Пакет средств разработки Azure Stack и у вас есть права администратора, см. раздел [Определение текущей версии](../operator/azure-stack-updates.md#determine-the-current-version), в котором объясняется, как определить сборку Azure Stack.

Чтобы получить сведения о других API, выполните приведенную ниже команду PowerShell, с помощью которой выводятся все пространства имен, типы ресурсов и версии API, поддерживаемые для подписки Azure Stack (на уровне свойств все же могут существовать различия). (Для выполнения этой команды необходимо [установить](../operator/azure-stack-powershell-install.md) и [настроить](azure-stack-powershell-configure-user.md) среду PowerShell для Azure Stack. Также вам потребуется подписка на действующее предложение Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Пример выходных данных (фрагмент): ![Пример выходных данных командлета Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Дополнительная информация

Более подробные сведения о различиях на уровне служб см. в следующих документах.

* [Рекомендации по использованию виртуальных машин в Azure Stack](azure-stack-vm-considerations.md)
* [Рекомендации по работе с хранилищем Azure Stack](azure-stack-acs-differences.md).
* [Considerations for Azure Stack networking](azure-stack-network-differences.md) (Рекомендации по работе с сетями в Azure Stack).
