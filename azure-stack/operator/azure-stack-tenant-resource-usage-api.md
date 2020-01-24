---
title: Справочник по API использования ресурсов для клиента
titleSuffix: Azure Stack
description: Справочные сведения об API использования ресурсов, который получает сведения об использовании Azure Stack Hub.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 5e6fd1042edcf59955a6e766d2ffb215c49c2949
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914722"
---
# <a name="tenant-resource-usage-api-reference"></a>API использования ресурсов для клиентов

Клиент может использовать API клиента для просмотра данных о том, как он использует ресурсы. Эти API совместимы с API использования Azure.

Как и в Azure, чтобы получить данные об использовании, вы можете использовать командлет Windows PowerShell [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates).

## <a name="api-call"></a>Вызов API

### <a name="request"></a>Запрос

Запрос возвращает сведения о потреблении для указанной подписки и указанного периода времени. Запрос не содержит текст.

| **Метод** | **URI запроса** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>Параметры

| **Параметр** | **Описание** |
| --- | --- |
| Armendpoint |Конечная точка Azure Resource Manager среды Azure Stack Hub. В соответствии с соглашением Azure Stack Hub имя конечной точки Azure Resource Manager должно иметь формат `https://management.{domain-name}`. Например, если для пакета средств разработки доменное имя — это local.azurestack.external, конечная точка Azure Resource Manager будет `https://management.local.azurestack.external`. |
| subId |Идентификатор подписки пользователя, который выполняет вызов. Этот API служит для получения сведений об использовании только в пределах одной подписки. Чтобы получить сведения по всем клиентам, поставщики могут использовать API использования для поставщика ресурсов. |
| reportedStartTime |Время начала выполнения запроса. Значение *Дата/Время* в формате UTC, указывающее начало нужного часа (например, 13:00). Для сбора сведений за сутки это значение должно соответствовать полуночи в формате UTC. В этом формате используется экранирование символов ISO 8601. Например, значение **2015-06-16T18%3a53%3a11%2b00%3a00Z** можно использовать в составе URI, так как символ двоеточия преобразован в %3a, а плюс — в %2b. |
| reportedEndTime |Время завершения выполнения запроса. Действуют те же ограничения, что и для параметра **reportedStartTime**. Значение **reportedEndTime** не может быть в будущем. |
| aggregationGranularity |Необязательный дискретный параметр, который имеет два возможных значения: **daily** (за сутки) или **hourly** (за час). Эти значения возвращают данные с разной степенью детализации: за сутки и за час. Вариант с детализацией **за сутки** используется по умолчанию. |
| api-version |Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию **2015-06-01-preview**. |
| continuationToken |Маркер, полученный из последнего вызова к API использования для поставщиков. Этот маркер требуется, когда ответ превышает 1000 строк. Он служит закладкой в процессе выполнения. Если он отсутствует, данные извлекаются с начала суток или часа, в зависимости от уровня детализации. |

### <a name="response"></a>Ответ

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

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

...
```

### <a name="response-details"></a>Сведения об ответе

| **Параметр** | **Описание** |
| --- | --- |
| идентификатор |Уникальный идентификатор статистического выражения использования. |
| name |Имя статистического выражения использования. |
| type |Определение ресурса. |
| subscriptionId |Идентификатор подписки пользователя Azure |
| usageStartTime |Начальное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC). |
| usageEndTime |Конечное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC). |
| instanceData |Пары "ключ-значение" из сведений об экземпляре (в новом формате):<br>  *resourceUri*: полный идентификатор ресурса, который содержит имя группы ресурсов и имя экземпляра <br>  *location*: регион, в котором выполнялась эта служба. <br>  *tags*: теги ресурсов, указанные пользователем. <br>  *additionalInfo*: дополнительные сведения об используемом ресурсе. Например, версия ОС или тип образа. |
| quantity |Объем потребления ресурса за указанный промежуток времени. |
| meterId |Уникальный идентификатор использованного ресурса (также обозначается **ResourceID**). |

## <a name="next-steps"></a>Дальнейшие действия

- [API использования ресурсов для поставщиков](azure-stack-provider-resource-api.md)
- [Часто задаваемые вопросы об использовании](azure-stack-usage-related-faq.md)
