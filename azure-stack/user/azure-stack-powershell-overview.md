---
title: PowerShell в Azure Stack | Документация Майкрософт
description: PowerShell в Azure Stack содержит множество модулей и контекстов
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0cef39147fdbc62fe0652b1e387aa23f5ecb8487
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782890"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Начало работы с PowerShell в Azure Stack

Среда PowerShell оптимизирована для администрирования ресурсов и управления ими из командной строки. С помощью Azure PowerShell можно создавать автоматизированные средства на основе модели Azure Resource Manager. Модуль PowerShell можно определить как набор функций PowerShell, объединенных с целю управления всеми аспектами определенной области. Для работы с Azure Stack вам потребуется оперировать различными наборами командлетов PowerShell.

Из этой статьи вы узнаете, как использовать различные модули PowerShell в Azure Stack. Существует четыре набора API, с которыми можно взаимодействовать при использовании PowerShell в Azure Stack.

| API | Справочник по PowerShell | Справочник по REST |
| --- | --- | --- |
| 1. глобальный Azure Resource Manager; | [Модули Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md). | [Обозреватель для REST API](https://docs.microsoft.com/rest/api/) |
| 2. Resource Manager для Azure Stack | [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack). | [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack). |
| 3. Конечные точки администрирования Azure Stack | [Модуль Azure Stack 1.6.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Обозреватель для REST API — Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Привилегированная конечная точка в Azure Stack | [Использование привилегированной конечной точки в Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Каждый интерфейс связывает поставщиков ресурсов в глобальной среде Azure или Azure Stack. Поставщики ресурсов расширяют возможности Azure. Например, поставщик ресурсов службы вычислений Azure предоставляет программный доступ к созданию и управлению виртуальными машинами и их вспомогательными ресурсами.

Поставщики ресурсов предоставляют функциональные возможности и элементы управления, используемые для управления и настройки ресурса. Использовав Azure Resource Manager, вы можете получить программный доступ к поставщикам ресурсов. В свою очередь, интерфейс обеспечивает место для PowerShell, Azure CLI и ваших собственных клиентов REST.

## <a name="where-to-find-azure-stack-powershell"></a>Расположение PowerShell для Azure Stack

На следующей блок-схеме показано отношение каждого набора модулей PowerShell. Загрузка модулей PowerShell и управление глобальной средой Azure и Azure Stack могут быть выполнены с вашего компьютера.

![PowerShell для Azure Stack](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Глобальная среда Azure

В Azure PowerShell доступен набор командлетов, которые используют текущую версию Azure Resource Manager для работы с ресурсами Azure. Azure PowerShell использует .NET Standard. Это означает, что вы можете использовать различные версии PowerShell с Windows, macOS и Linux. Среда Azure PowerShell также доступна в Azure Cloud Shell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Resource Manager для Azure Stack

PowerShell для Azure Stack предоставляет набор командлетов, которые используют предыдущие версии Azure Resource Manager. Эти командлеты совместимы с поставщиками ресурсов в Azure Stack. Каждый из поставщиков ресурсов Azure Stack использует более раннюю версию поставщика, обнаруженную в глобальной среде Azure. Для координации версий поддерживаемых в Azure Stack и используемых поставщиками, можно использовать профили API. В Azure Stack используется PowerShell 5.1, которая доступна только для ОС Windows. Дополнительные сведения см. в статье [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Администратор Azure Stack

Чтобы установить и управлять Azure Stack, оператору облака будет предоставлен набор поставщиков ресурсов. Взаимодействие в глобальной среде Azure не зависит от пользователя и обрабатывается в фоновом режиме как часть Azure. Тем не менее, Azure Stack предоставляет предприятиям возможность поддержки в частном облаке. Чтобы выполнить эти задачи, оператору необходимо взаимодействовать с API администратора Azure Stack. Дополнительные сведения см. в статье [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack).

### <a name="azure-stack-privileged-endpoint"></a>Привилегированная конечная точка в Azure Stack

Для таких действий операторов в Azure Stack, как тестирование установки и журналов доступа, им может потребоваться взаимодействие с привилегированной конечной точкой (PEP). Эта конечная точка является предварительно настроенной удаленной консолью PowerShell, которая предоставляет оператору достаточный уровень доступа для выполнения конкретных задач. Конечная точка использует PowerShell JEA (Just Enough Administration) для предоставления ограниченного набора командлетов. Дополнительные сведения см. в статье [Using the privileged endpoint in Azure Stack](../operator/azure-stack-privileged-endpoint.md) (Использование привилегированной конечной точки в Azure Stack).

### <a name="azurestack-tools"></a>AzureStack-Tools

Кроме этого, Azure Stack предоставляет сценарии и дополнительные командлеты, доступные в средствах Azure Stack репозитория GitHub. AzureStack-Tools — содержит модули PowerShell для администрирования и развертывания ресурсов в Azure Stack. Если вы планируете установить VPN-подключение, можно скачать эти модули PowerShell в Пакет средств разработки Azure Stack или внешний клиент под управлением Windows. На репозитории Github приведены дополнительные сведения о[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>Работа с PowerShell в Azure Stack

PowerShell предоставляет программный способ взаимодействия с Azure Resource Manager. Если вы автоматизируете задачи, то можете работать с интерактивным интерфейсом командной строки или писать сценарии.

Если много работать с PowerShell Azure Stack, то можно обнаружить, что установка и переустановка модулей занимает много времени. В то же время это может оказаться сложной задачей при работе в глобальной среде Azure, так как в зависимости от цели вам может потребоваться выполнить удаление и переустановку модулей. Чтобы изолировать каждую из версий PowerShell на локальном компьютере, можно использовать контейнеры Docker. В статье [Использование Docker для запуска PowerShell](azure-stack-powershell-user-docker.md) приводятся сведения об использовании контейнеров Docker. Это позволит переключатся между наборами модулей PowerShell.


## <a name="next-steps"></a>Дополнительная информация

- См. статью [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md).
- [Install Azure Stack Powershell](../operator/azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
- Чтобы обеспечить согласованность данных в облаке, см. статью [Рекомендации по использованию шаблона Azure Resource Manager](azure-stack-develop-templates.md).