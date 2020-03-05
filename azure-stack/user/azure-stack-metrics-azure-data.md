---
title: Использование Azure Monitor в Azure Stack Hub
description: Узнайте, как использовать Azure Monitor в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 12/01/2019
ms.openlocfilehash: 9abcc23505279f417e53f896e58e76dd9205691f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702335"
---
# <a name="use-azure-monitor-on-azure-stack-hub"></a>Использование Azure Monitor в Azure Stack Hub

В этой статье приведены общие сведения о службе Azure Monitor в Azure Stack Hub. В ней рассматривается принцип работы Azure Monitor, а также дополнительная информация о том, как использовать Azure Monitor в Azure Stack Hub. 

Общие сведения о работе с Azure Monitor в Azure Stack Hub см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Колонка Monitor в Azure Stack Hub](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor — служба платформы, которая предоставляет единый источник данных для наблюдения за ресурсами Azure. С помощью Azure Monitor можно визуализировать, запрашивать, маршрутизировать, архивировать метрики и журналы, полученные от ресурсов Azure, а также выполнять с ними другие действия. Эти операции можно выполнять с помощью портала администрирования Azure Stack Hub, командлетов Monitor PowerShell, кроссплатформенного интерфейса командной строки и интерфейсов REST API Azure Monitor. Дополнительные сведения об определенных подключениях, поддерживаемых Azure Stack Hub, см. в статье [Использование данных мониторинга из Azure Stack Hub](azure-stack-metrics-monitor.md).

> [!Note]
> Метрики и журналы диагностики недоступны для Пакета средств разработки Azure Stack.

## <a name="prerequisites-for-azure-monitor-on-azure-stack-hub"></a>Предварительные требования для Azure Monitor в Azure Stack Hub

Зарегистрируйте поставщик ресурсов **Microsoft.insights** в параметрах поставщика ресурсов для предложения подписки. Убедитесь, что поставщик ресурсов доступен в предложении, связанном с подпиской:

1. Откройте портал пользователя Azure Stack Hub.
2. Выберите **Подписки**.
3. Выберите подписку, которую нужно зарегистрировать.
4. В разделе **Параметры** выберите **Поставщики ресурсов**. 
5. Найдите компонент **Microsoft.Insights** в списке и убедитесь, что он находится в состоянии **Зарегистрировано**.

## <a name="overview-of-azure-monitor-on-azure-stack-hub"></a>Обзор Azure Monitor в Azure Stack Hub

Как и Azure Monitor в Azure, Azure Monitor в Azure Stack Hub предоставляет метрики инфраструктуры базового уровня, а также журналы для большинства служб.

## <a name="azure-monitor-sources-compute-subset"></a>Источники Azure Monitor: подмножество вычислений

![Источники Azure Monitor в Azure Stack Hub: подмножество вычислений](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Поставщик ресурсов **Microsoft.Compute** в Azure Stack Hub включает в себя:
 - Виртуальные машины 
 - Масштабируемые наборы виртуальных машин

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>Приложения — журналы диагностики, журналы приложений и метрики.

Приложения могут выполняться в операционной системе виртуальной машины, работающей с использованием поставщика ресурсов **Microsoft.Compute**. Эти приложения и виртуальные машины выдают собственный набор журналов и метрик. Azure Monitor использует расширение системы диагностики Azure (Windows или Linux) для сбора множества метрик и журналов на уровне приложения.

К типам метрик относятся:
 - Счетчики производительности
 - журналы приложений;
 - Журналы событий Windows
 - источник событий .NET;
 - Журналы IIS
 - Трассировка событий Windows на основе манифестов
 - Аварийные дампы
 - пользовательские журналы ошибок.

> [!Note]  
> Расширение диагностики для Linux не поддерживается в Azure Stack Hub.

### <a name="host-and-guest-vm-metrics"></a>Метрики виртуальной машины узла и гостевой виртуальной машины

Перечисленные выше вычислительные ресурсы имеют выделенную виртуальную машину узла и гостевую ОС. Виртуальная машина узла и гостевая ОС являются эквивалентом корневой и гостевой виртуальной машины в гипервизоре Hyper-V. Собирать метрики можно как для виртуальной машины узла, так и для гостевой ОС. Вы можете также собирать журналы диагностики для гостевой ОС. Список собираемых службой Azure Monitor метрик виртуальных машин узлов и гостевых виртуальных машин в Azure Stack Hub приведен в статье [Метрики, поддерживаемые Azure Monitor в Azure Stack Hub](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Журнал действий

В журналах действий вы можете искать информацию о ресурсах вычисления в контексте инфраструктуры Azure Stack Hub. Эти журналы содержат такие сведения, как время создания или удаления ресурсов. Журналы действий в Azure Stack Hub согласовываются с Azure. Дополнительные сведения см. в статье [Мониторинг действий подписки с помощью журнала действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Источники мониторинга Azure: все остальное

![Источники Azure Monitor в Azure Stack Hub: все остальное](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Ресурсы — метрики и журналы диагностики

Доступные для сбора метрики и журналы диагностики отличаются в зависимости от типа ресурса. Список доступных для сбора метрик для каждого ресурса в Azure Stack Hub можно найти на странице сведений о поддерживаемых метриках. Дополнительные сведения см. в статье [Метрики, поддерживаемые Azure Monitor в Azure Stack Hub](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Журнал действий

Журнал действий такой же, как и для вычислительных ресурсов. 

### <a name="uses-for-monitoring-data"></a>Применение данных мониторинга

**Хранение и архивация**  

Некоторые данные мониторинга уже хранятся и доступны в Azure Monitor в течение определенного периода времени. 
 - Метрики хранятся в течение 90 дней. 
 - Записи журнала действий хранятся в течение 90 дней. 
 - Журналы диагностики не хранятся.
 - Архивируйте данные в учетную запись хранения для длительного хранения.

**Запрос**  

Для доступа к данным в системе или службе хранилища Azure можно использовать REST API Azure Monitor, кроссплатформенные команды интерфейса командной строки, командлеты PowerShell или пакет SDK для .NET. 

**Визуализация**

Визуализация данных мониторинга на графиках и диаграммах помогает находить тенденции намного быстрее, чем при просмотре самих данных. 

Ниже представлены некоторые способы визуализации.
 - Использование порталов пользователя и администрирования Azure Stack Hub.
 - Отправка данных в Microsoft Power BI.
 - Отправка данных в стороннее средство визуализации с помощью потоковой трансляции или использования инструмента для чтения из архива в службе хранилища Azure.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack-hub"></a>Методы получения доступа к Azure Monitor в Azure Stack Hub

Вы можете использовать отслеживание, маршрутизацию и извлечение данных с помощью одного из следующих методов. Не все методы доступны для всех действий или типов данных. 

 - [Пользовательский портал Azure Stack Hub](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Кроссплатформенный интерфейс командной строки](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [Пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> Если при просмотре диаграммы производительности виртуальной машины возникнет ошибка с сообщением о том, что **ресурс не найден**, убедитесь, что вы зарегистрировали Microsoft.insights в подписке, связанной с этой виртуальной машиной.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании данных мониторинга в Azure Stack Hub см. в [этой статье](azure-stack-metrics-monitor.md).
