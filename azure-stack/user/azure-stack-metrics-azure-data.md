---
title: Azure Monitor в Azure Stack | Документация Майкрософт
description: Сведения о службе Azure Monitor в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: a4905951910a220185a8ae0651f5297c97af41f2
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311450"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Monitor в Azure Stack

*Область применения: интегрированные системы Azure Stack*.

В этой статье приведены общие сведения о службе Azure Monitor в Azure Stack. В ней рассматривается принцип работы Azure Monitor, а также дополнительная информация о том, как использовать Azure Monitor в Azure Stack. 

Ознакомительные сведения, обзор и информацию о начале работы с Azure Monitor см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Колонка Monitor в Azure Stack](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Служба платформы Azure Monitor — это единый источник для мониторинга ресурсов Azure. С помощью Azure Monitor можно визуализировать, запрашивать, маршрутизировать, архивировать метрики и журналы, полученные от ресурсов Azure, и выполнять другие действия с ними. Эти операции можно выполнять с помощью портала администрирования Azure Stack, командлетов Monitor PowerShell, кроссплатформенного интерфейса командной строки и интерфейсов REST API Azure Monitor. Дополнительные сведения об определенных подключениях, поддерживаемых Azure Stack, см. в [этой статье](azure-stack-metrics-monitor.md).

> [!Note]
> Метрики и журналы диагностики недоступны для Пакета средств разработки Azure Stack.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Предварительные требования для Azure Monitor в Azure Stack

Зарегистрируйте поставщик ресурсов **Microsoft.insights** в параметрах поставщика ресурсов для предложения подписки. Убедитесь, что поставщик ресурсов доступен в предложении, связанном с подпиской:

1. Откройте портал администрирования Azure Stack.
2. Выберите **Предложения**.
3. Выберите предложение, связанное с подпиской.
4. В разделе **Параметры** выберите **Поставщики ресурсов**. 
5. Найдите **Microsoft.Insights** в списке и убедитесь, что он находится в состоянии **Зарегистрировано**.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Обзор Azure Monitor в Azure Stack

Как и Azure Monitor в Azure, Azure Monitor в Azure Stack предоставляет метрики инфраструктуры базового уровня, а также журналы для большинства служб.

## <a name="azure-monitor-sources-compute-subset"></a>Источники Azure Monitor: подмножество вычислений

![Источники Azure Monitor в Azure Stack: подмножество вычислений](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Поставщик ресурсов **Microsoft.Compute** в Azure Stack включает в себя:
 - Виртуальные машины 
 - Масштабируемые наборы виртуальных машин

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Приложения — журналы диагностики, журналы приложений и метрики.

Приложения могут выполняться в операционной системе виртуальной машины, работающей с использованием поставщика ресурсов **Microsoft.Compute**. Эти приложения и виртуальные машины выдают собственный набор журналов и метрик. Azure Monitor использует расширение системы диагностики Azure (Windows или Linux) для сбора множества метрик и журналов на уровне приложения, 

К типам метрик относятся:
 - Счетчики производительности
 - Журналы приложений
 - Журналы событий Windows
 - источник событий .NET;
 - Журналы IIS
 - Трассировка событий Windows на основе манифестов
 - Аварийные дампы
 - пользовательские журналы ошибок.

> [!Note]  
> Расширение диагностики для Linux не поддерживается в Azure Stack.

### <a name="host-and-guest-vm-metrics"></a>Метрики виртуальной машины узла и гостевой виртуальной машины

Перечисленные выше вычислительные ресурсы имеют выделенную виртуальную машину узла и гостевую ОС. Виртуальная машина узла и гостевая ОС являются эквивалентом корневой и гостевой виртуальной машины в гипервизоре Hyper-V. Собирать метрики можно как для виртуальной машины узла, так и для гостевой ОС. Кроме того, вы также можете собирать журналы диагностики для гостевой ОС. Список собираемых службой Azure Monitor метрик узла и гостевой виртуальной машины в Azure Stack см. в [этой статье](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Журнал действий

В журналах действий вы можете искать информацию о ресурсах вычисления в контексте инфраструктуры Azure Stack. Эти журналы содержат такие сведения, как время создания или удаления ресурсов. Журналы действий в Azure Stack согласуются с Azure. Дополнительные сведения см. в статье [Мониторинг действий подписки с помощью журнала действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Источники мониторинга Azure: все остальное

![Источники Azure Monitor в Azure Stack: все остальное](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Ресурс — метрики и журналы диагностики

Доступные для сбора метрики и журналы диагностики отличаются в зависимости от типа ресурса. Список доступных для сбора метрик для каждого ресурса в Azure Stack можно найти на странице сведений о поддерживаемых метриках. Дополнительные сведения см. в статье [Метрики, поддерживаемые Azure Monitor в Azure Stack](azure-stack-metrics-supported.md).

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
 - Использование портала пользователя и администратора Azure Stack.
 - Отправка данных в Microsoft Power BI.
 - отправка данных в стороннее средство визуализации с помощью потоковой трансляции или использования инструмента для чтения из архива в службе хранилища Azure.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Методы получения доступа к Azure Monitor в Azure Stack

В большинстве случаев для управления отслеживанием, маршрутизацией и извлечением данных можно использовать один из следующих методов. Не все методы доступны для всех действий или типов данных.

 - [Портал Azure Stack](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Кроссплатформенный интерфейс командной строки](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [ПАКЕТ SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о вариантах мониторинга данных потребления в Azure Stack см. в [этой статье](azure-stack-metrics-monitor.md).
