---
title: API использования ресурсов для клиентов | Документация Майкрософт
description: Справочные сведения об API использования ресурсов, который получает сведения об использовании Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6aaeff9ef708bcb89147d89205aa6526ebdb874f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268225"
---
# <a name="tenant-resource-usage-api"></a>API использования ресурсов для клиентов

Клиент может использовать API клиента для просмотра данных об использовании собственных ресурсов. Этот API соответствует API использования в Azure (сейчас доступен в режиме закрытой предварительной версии).

Как и в Azure, чтобы получить данные об использовании, вы можете использовать командлет Windows PowerShell **Get-UsageAggregates**.

## <a name="api-call"></a>Вызов API
### <a name="request"></a>Запрос
Запрос возвращает сведения о потреблении для указанной подписки и указанного периода времени. Запроса не содержит текст.

| **Метод** | **URI запроса** |
| --- | --- |
| ПОЛУЧЕНИЕ |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Аргументы
| **Аргумент** | **Описание** |
| --- | --- |
| *Armendpoint* |Конечная точка Azure Resource Manager среды Azure Stack. В соответствии с соглашением Azure Stack имя конечной точки Azure Resource Manager должно быть в формате `https://management.{domain-name}`. Например, если для пакета средств разработки доменное имя — это local.azurestack.external, конечная точка Azure Resource Manager будет `https://management.local.azurestack.external`. |
| *subId* |Идентификатор подписки пользователя, который выполняет вызов. Этот API служит для получения сведений об использовании только в пределах одной подписки. Чтобы получить сведения для всех клиентов, поставщики могут использовать API использования поставщика ресурсов. |
| *reportedStartTime* |Время начала выполнения запроса. Значение *DateTime* должно быть в формате UTC и соответствовать началу нужного часа (например, 13:00). Для сбора сведений за сутки это значение должно соответствовать полуночи в формате UTC. В этом формате используется *экранирование* символов ISO 8601. Например, значение 2015-06-16T18%3a53%3a11%2b00%3a00Z можно использовать в составе URI, так как символ двоеточия преобразован в %3a, а плюс — в %2b. |
| *reportedEndTime* |Время завершения выполнения запроса. См. ограничения, которые применяются к аргументу *reportedStartTime*. Значение *reportedEndTime* не может быть в будущем. |
| *aggregationGranularity* |Необязательный дискретный параметр, который имеет два возможных значения: daily (за сутки) или hourly (за час). Эти значения возвращают данные с разной степенью детализации: за сутки и за час. Вариант с детализацией за сутки используется по умолчанию. |
| *api-version* |Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию 2015-06-01-preview. |
| *continuationToken* |Маркер, который получен из последнего вызова поставщика API использования. Этот маркер требуется, когда ответ содержит больше 1000 строк. Он служит как закладка в процессе выполнения. Если он отсутствует, данные извлекаются с начала суток или часа, в зависимости от уровня детализации. |

### <a name="response"></a>Ответ
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>Сведения об ответе
| **Аргумент** | **Описание** |
| --- | --- |
| *id* |Уникальный идентификатор статистического выражения использования |
| *name* |Имя статистического выражения использования |
| *type* |Определение ресурса |
| *subscriptionId* |Идентификатор подписки пользователя Azure |
| *usageStartTime* |Начальное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC) |
| *usageEndTime* |Конечное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC) |
| *instanceData* |Пары "ключ-значение" из сведений об экземпляре (в новом формате):<br>  *resourceUri*: полный идентификатор ресурса, включая группы ресурсов и имя экземпляра <br>  *location*: регион, в котором выполнялась эта служба <br>  *tags*: теги ресурсов, указанные пользователем <br>  *additionalInfo*: подробные сведения об использованном ресурсе, например версия ОС или тип образа |
| *quantity* |Объем потребления ресурса за указанный промежуток времени |
| *meterId* |Уникальный идентификатор использованного ресурса (также обозначается *ResourceID*) |


## <a name="next-steps"></a>Дополнительная информация
[API использования ресурсов для поставщиков](azure-stack-provider-resource-api.md)

[Часто задаваемые вопросы об использовании](azure-stack-usage-related-faq.md)

