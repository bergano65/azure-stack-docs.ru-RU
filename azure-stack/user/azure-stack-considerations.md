---
title: Различия между Azure и Azure Stack Hub при использовании служб и создании приложений
description: Узнайте о различиях между Azure и Azure Stack Hub при использовании служб и создании приложений.
author: sethmanheim
ms.topic: overview
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 74e2e6986f3fac7ee6503c1b7417dffea44842b5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883871"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Различия между Azure и Azure Stack Hub при использовании служб и создании приложений

Если вы используете службы или создаете приложения в Azure Stack Hub, важно иметь представление о различиях между Azure Stack Hub и Azure. В этой статье описаны разные возможности и ключевые моменты, которые необходимо учитывать при выборе Azure Stack Hub в качестве гибридной облачной среды разработки.

## <a name="overview"></a>Обзор

Azure Stack Hub ― это гибридная облачная платформа, позволяющая использовать службы Azure из центра обработки данных вашей компании или поставщика услуг. Вы можете создать приложение в Azure Stack Hub и развернуть его в Azure Stack Hub, Azure или гибридном облаке Azure.

У оператора Azure Stack Hub можно узнать, какие службы доступны для использования и как получить поддержку. Эти службы предоставляются в составе настраиваемых планов и предложений.

В [технической документации по Azure](/azure) предполагается, что приложения разрабатываются для службы Azure, а не для Azure Stack Hub. При разработке и развертывании приложений в Azure Stack Hub важно учитывать несколько важных различий.

* Azure Stack Hub предоставляет разные службы и функции, доступные в Azure.
* Компания или поставщик услуг могут самостоятельно выбрать, какие службы они будут предоставлять. Это могут быть настраиваемые службы или приложения. Также они могут предоставлять собственные версии документации.
* Необходимо использовать правильные конечные точки, предназначенные для работы с Azure Stack Hub (например, URL-адреса портала и конечной точки Azure Resource Manager).
* Необходимо использовать версии PowerShell и API, которые поддерживаются в Azure Stack Hub. Использование поддерживаемых версий гарантирует, что приложения будут хорошо работать и в Azure Stack Hub, и в Azure.

## <a name="cheat-sheet-high-level-differences"></a>Краткий справочник. Основные различия

В следующей таблице перечислены основные различия между Azure Stack Hub и Azure. Не забывайте о них, когда осуществляете разработку для Azure Stack Hub или используете службы Azure Stack Hub.

| Область | Azure (глобальная) | Azure Stack Hub |
| -------- | ------------- | ----------|
| Кто оператор? | Microsoft | Ваша организация или поставщик услуг.|
| Куда обращаться для получения поддержки? | Microsoft | В интегрированной системе за поддержкой следует обращаться к оператору Azure Stack Hub (это может быть ваша организация или поставщик услуг).<br><br>Если вы используете Пакет средств разработки Azure Stack (ASDK), посетите [форумы Майкрософт](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Так как пакет разработки предлагается как среда для оценки, служба поддержки корпорации Майкрософт не предоставляет для него официальную поддержку.
| Доступные службы | См. список [продуктов Azure](https://azure.microsoft.com/services/?b=17.04b). Выбор доступных служб зависит от региона Azure. | Azure Stack Hub поддерживает разные службы Azure. Конкретный набор зависит от политики вашей организации или поставщика услуг.
| Конечная точка Azure Resource Manager* | https://management.azure.com | При работе с интегрированной системой Azure Stack Hub используйте конечную точку, предоставляемую оператором Azure Stack Hub.<br><br>Для пакета SDK используйте следующий адрес: https://management.local.azurestack.external.
| URL-адрес портала* | [https://portal.azure.com](https://portal.azure.com) | При работе с интегрированной системой Azure Stack Hub используйте URL-адрес, предоставляемый оператором Azure Stack Hub.<br><br>Для пакета SDK используйте следующий адрес: https://portal.local.azurestack.external.
| Регион | Вы можете самостоятельно выбрать регион для развертывания. | В интегрированной системе Azure Stack Hub используется регион, доступный в этой системе.<br><br>Для Пакета средств разработки Azure Stack (ASDK) всегда используется только **локальный** регион.
| Группы ресурсов | Группа ресурсов может размещаться в нескольких регионах. | Как в интегрированной системе, так и в пакете SDK доступен только один регион.
|Поддержка пространств имен, типов ресурсов и версий API | Поддерживается последняя версия и прежние версии, о прекращении поддержки которых не было официально объявлено. | Azure Stack Hub поддерживает только определенные версии. См. также раздел [Требования к версиям](#version-requirements) в этой статье.
| | |

* Если вы — оператор Azure Stack Hub, дополнительные сведения см. в руководстве по [использованию портала администрирования](../operator/azure-stack-manage-portals.md) и описании [основных понятий администрирования](../operator/azure-stack-manage-basics.md).

## <a name="helpful-tools-and-best-practices"></a>Полезные средства и рекомендации

Корпорация Майкрософт предоставляет средства и рекомендации, которые помогут вам в разработке приложений для Azure Stack Hub.

| Рекомендация | Ссылки |
| -------- | ------------- |
| Установите подходящие инструментальные средства на рабочей станции разработчика. | - [Установка PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Скачивание средств](../operator/azure-stack-powershell-download.md)<br>- [Настройка PowerShell](azure-stack-powershell-configure-user.md)<br>- [Установка Visual Studio](azure-stack-install-visual-studio.md)
| Изучите информацию по следующим темам:<br>– рекомендации по использованию шаблона Azure Resource Manager;<br>– как найти шаблоны для быстрого начала работы;<br>– использование модуля политики, который позволяет применять Azure для разработки решений в Azure Stack Hub. | [Разработка для Azure Stack Hub](azure-stack-developer.md) |
| Изучите и соблюдайте рекомендации для шаблонов. | [Шаблоны Resource Manager для быстрого начала работы](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>Требования к версиям

Azure Stack Hub поддерживает определенные версии Azure PowerShell и API служб Azure. Следует использовать именно эти поддерживаемые версии, чтобы приложение можно было развернуть как в Azure Stack Hub, так и в Azure.

Чтобы гарантировать правильность версии Azure PowerShell, используйте [профили версий API](azure-stack-version-profiles.md). Чтобы найти последний профиль версии API, который можно использовать, определите номер используемой сборки Azure Stack Hub. Эти сведения можно получить у администратора Azure Stack Hub.

> [!NOTE]
> Если вы используете Пакет средств разработки для Azure Stack Hub и у вас есть права администратора, см. инструкции по [определению текущей версии сборки Azure Stack Hub](../operator/azure-stack-updates.md).

Чтобы получить сведения о других API, выполните приведенную ниже команду PowerShell, с помощью которой выводятся все пространства имен, типы ресурсов и версии API, поддерживаемые для подписки Azure Stack Hub (на уровне свойств допускаются различия). Для выполнения этой команды необходимо [установить](../operator/azure-stack-powershell-install.md) и [настроить](azure-stack-powershell-configure-user.md) среду PowerShell для Azure Stack Hub. Также вам потребуется подписка на действующее предложение Azure Stack Hub.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Пример выходных данных (фрагмент): ![Пример выходных данных командлета Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Дальнейшие действия

Более подробные сведения о различиях на уровне служб см. в следующих документах.

* [Рекомендации по использованию виртуальных машин в Azure Stack Hub](azure-stack-vm-considerations.md)
* [Рекомендации по работе с хранилищем Azure Stack Hub](azure-stack-acs-differences.md)
* [Considerations for Azure Stack Hub networking](azure-stack-network-differences.md) (Рекомендации по работе с сетями в Azure Stack Hub).
