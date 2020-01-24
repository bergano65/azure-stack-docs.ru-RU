---
title: Использование PowerShell в Azure Stack Hub | Документация Майкрософт
description: В Azure Stack Hub поддерживаются различные модули и контексты PowerShell.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e1025fe8860fc084114ef67f7c4ad7cf204c3511
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819528"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Начало работы с PowerShell в Azure Stack Hub

Среда PowerShell оптимизирована для администрирования ресурсов и управления ими из командной строки. С помощью Azure PowerShell можно создавать автоматизированные средства на основе модели Azure Resource Manager. Модуль PowerShell — это набор функций PowerShell, объединенных для управления всеми аспектами определенной области. Для работы с Azure Stack Hub вам потребуется использовать разные наборы командлетов PowerShell.

Из этой статьи вы узнаете, как использовать разные модули PowerShell в Azure Stack Hub. Есть четыре набора API, с которыми можно взаимодействовать при использовании PowerShell в Azure Stack Hub. Они представлены в таблице ниже.

| API | Справочник по PowerShell | Справочник по REST |
| --- | --- | --- |
| глобальный Azure Resource Manager; | [Модули Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Обозреватель для REST API](https://docs.microsoft.com/rest/api/) |
| Resource Manager для Azure Stack Hub | [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md) | [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md) |
| Конечные точки администрирования Azure Stack Hub | [Модуль администрирования Azure Stack Hub](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Обозреватель для REST API в Azure Stack Hub](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Привилегированная конечная точка Azure Stack Hub | [Использование привилегированной конечной точки в Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md) | |

Каждый интерфейс связывает поставщиков ресурсов в глобальной среде Azure или Azure Stack Hub. Поставщики ресурсов расширяют возможности Azure. Например, поставщик ресурсов служб вычислений Azure предоставляет программный доступ для создания и администрирования виртуальных машин и их вспомогательных ресурсов.

Поставщики ресурсов предоставляют функции и элементы управления для управления ресурсами и их настройки. Вы можете программным образом получить доступ к поставщикам ресурсов с помощью Azure Resource Manager. Интерфейс, в свою очередь, предоставляет доступ к возможностям PowerShell, Azure CLI и ваших клиентов REST.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Расположение PowerShell для Azure Stack Hub

На следующей блок-схеме показаны связи между наборами модулей PowerShell. Загрузка модулей PowerShell и управление глобальной средой Azure и Azure Stack Hub могут быть выполнены с вашего компьютера.

![PowerShell для Azure Stack Hub](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Глобальная среда Azure

В Azure PowerShell доступен набор командлетов, которые используют текущую версию Azure Resource Manager для работы с ресурсами Azure. Azure PowerShell использует стандартную версию .NET. Это означает, что вы можете использовать разные версии PowerShell с Windows, macOS и Linux. Среда Azure PowerShell также доступна в Azure Cloud Shell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Resource Manager для Azure Stack Hub

PowerShell для Azure Stack Hub предоставляет набор командлетов, которые используют предыдущие версии Azure Resource Manager. Эти командлеты совместимы с поставщиками ресурсов в Azure Stack Hub. Каждый из поставщиков ресурсов Azure Stack Hub использует более раннюю версию поставщика, обнаруженную в глобальной среде Azure. Для координации версий, поддерживаемых в Azure Stack Hub и используемых поставщиками, можно использовать профили API. В Azure Stack Hub используется PowerShell версии 5.1, которая доступна только в Windows. Дополнительные сведения см. в статье [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Администратор Azure Stack Hub

Чтобы установить и администрировать Azure Stack Hub, оператору облака предоставляется набор поставщиков ресурсов. Взаимодействие в глобальной среде Azure не зависит от пользователя и выполняется в фоновом режиме как часть нагрузки Azure. При этом благодаря Azure Stack Hub предприятия могут использовать частные облака. Чтобы выполнить эти задачи, оператор использует API администратора Azure Stack Hub. Дополнительные сведения об установке PowerShell для Azure Stack Hub см. в [этой статье](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Привилегированная конечная точка Azure Stack Hub

Для выполнения таких действий в Azure Stack Hub, как тестирование установки и доступ к журналам, операторы могут использовать привилегированную конечную точку (PEP). Эта конечная точка является предварительно настроенной удаленной консолью PowerShell, которая предоставляет оператору требуемый уровень доступа для выполнения определенных задач. Конечная точка использует PowerShell JEA (Just Enough Administration) для предоставления ограниченного набора командлетов. См. подробнее об [использовании привилегированной конечной точки в Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-hub-tools"></a>Средства Azure Stack Hub

Azure Stack Hub предоставляет скрипты и дополнительные командлеты, доступные в репозитории *AzureStack-Tools* на GitHub. AzureStack-Tools — содержит модули PowerShell для администрирования и развертывания ресурсов в Azure Stack Hub. Если вы планируете установить VPN-подключение, можно скачать эти модули PowerShell в Пакет средств разработки Azure Stack или внешний клиент под управлением Windows. См. подробнее на странице [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack-hub"></a>Использование PowerShell в Azure Stack Hub

PowerShell предоставляет программный способ взаимодействия с Azure Resource Manager. Вы можете работать с интерактивным интерфейсом командной строки или создавать скрипты, если требуется автоматизировать задачи.

Если много работать с PowerShell Azure Stack Hub, то можно обнаружить, что установка и переустановка модулей занимает много времени. Это может быть утомительным при работе в глобальной среде Azure, так как вам нужно удалять и переустанавливать модули в зависимости от задач. 

Чтобы изолировать каждую из версий PowerShell на локальном компьютере, можно использовать контейнеры Docker. См. подробнее об [использовании контейнеров Docker для переключения между модулями PowerShell](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Дальнейшие действия

- См. подробнее об [использовании профилей API для PowerShell](azure-stack-version-profiles.md) в Azure Stack Hub.
- [Установка PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md).
- Чтобы обеспечить согласованность данных в облаке, см. статью [Рекомендации по использованию шаблона Azure Resource Manager](azure-stack-develop-templates.md).
