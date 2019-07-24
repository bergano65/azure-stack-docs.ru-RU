---
title: API использования ресурсов для поставщиков | Документация Майкрософт
description: Справочные сведения об API использования ресурсов, который получает сведения об использовании Azure Stack
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
ms.date: 07/16/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: 631d6764ca7947ddafd70ec57b607df1ea5a4ab5
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286687"
---
# <a name="provider-resource-usage-api"></a>API использования ресурсов для поставщиков

Термин *поставщик* обозначает администраторов служб и любых делегированных поставщиков. Операторы Azure Stack и делегированные поставщики с помощью API использования ресурсов для поставщиков могут просматривать данные об использовании ресурсов их непосредственными клиентами. Например, как показано на следующей схеме, с помощью API для поставщиков P0 может получить сведения о прямом использовании ресурсов для P1 и P2, а P1 — для P3 и P4.

![Концептуальная модель иерархии поставщиков](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Справка о вызовах API

### <a name="request"></a>Запрос

Запрос возвращает сведения о потреблении для указанной подписки и указанного периода времени. Запроса не содержит текст.

Этот API использования предназначен для поставщиков, поэтому вызывающей стороне должна быть назначена роль **владельца**, **участника** или **читателя** в подписке поставщика.

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Аргументы

| Аргумент | ОПИСАНИЕ |
| --- | --- |
| `armendpoint` |Конечная точка Azure Resource Manager среды Azure Stack. В соответствии с соглашением Azure Stack имя конечной точки Azure Resource Manager должно быть в формате `https://adminmanagement.{domain-name}`. Например, если для пакета средств разработки доменное имя — *local.azurestack.external*, то конечная точка Azure Resource Manager — `https://adminmanagement.local.azurestack.external`. |
| `subId` |Идентификатор подписки пользователя, который выполняет вызов. |
| `reportedStartTime` |Время начала выполнения запроса. Значение `DateTime` должно быть в формате UTC и соответствовать началу нужного часа (например, 13:00). Для сбора сведений за сутки это значение должно соответствовать полуночи в формате UTC. В этом формате используется экранирование символов ISO 8601. Например, значение `2015-06-16T18%3a53%3a11%2b00%3a00Z` можно использовать в составе URI, так как символ двоеточия преобразован в `%3a`, а плюс — в `%2b`. |
| `reportedEndTime` |Время завершения выполнения запроса. Действуют те же ограничения, что и для аргумента `reportedStartTime`. Значение `reportedEndTime` не может относится ни к будущему ни к текущему дню. В противном случае возвращается результат "Обработка не завершена". |
| `aggregationGranularity` |Необязательный дискретный параметр, который имеет два возможных значения: **daily** (за сутки) или **hourly** (за час). Эти значения возвращают данные с разной степенью детализации: за сутки и за час. Вариант с детализацией **за сутки** используется по умолчанию. |
| `subscriberId` |Идентификатор подписки. Чтобы получить отфильтрованные данные, нужно указать идентификатор подписки для прямого клиента поставщика. Если идентификатор подписки не указан, вызов возвращает данные об использовании для всех прямых клиентов поставщика. |
| `api-version` |Версия протокола, который используется для выполнения этого запроса. Этот параметр имеет значение `2015-06-01-preview`. |
| `continuationToken` |Маркер, полученный из последнего вызова к API использования для поставщиков. Этот маркер требуется, когда ответ превышает 1000 строк. Он служит закладкой в процессе выполнения. Если маркер отсутствует, данные извлекаются с начала суток или часа, в зависимости от указанного уровня детализации. |

### <a name="response"></a>Ответ

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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

| Аргумент | ОПИСАНИЕ |
| --- | --- |
|`id` |Уникальный идентификатор статистического выражения использования. |
|`name` |Имя статистического выражения использования. |
|`type` |Определение ресурса. |
|`subscriptionId` |Идентификатор подписки пользователя Azure Stack. |
|`usageStartTime`|Начальное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC).|
|`usageEndTime`|Конечное время включения в контейнер использования, к которому относится статистическое выражение использования (в формате UTC). |
|`instanceData` |Пары "ключ-значение" из сведений об экземпляре (в новом формате):<br> `resourceUri`: полный идентификатор ресурса, который содержит группы ресурсов и имя экземпляра. <br> `location`: регион, в котором выполнялась эта служба. <br> `tags`: теги ресурсов, которые указываются пользователем. <br> `additionalInfo`: Подробные сведения об использованном ресурсе, например, версия ОС или тип образа. |
|`quantity`|Объем потребления ресурса за указанный промежуток времени. |
|`meterId` |Уникальный идентификатор использованного ресурса (также обозначается `ResourceID`). |

## <a name="retrieve-usage-information"></a>Получение сведений о потреблении

### <a name="powershell"></a>PowerShell

Чтобы данные об использовании создавались, должны существовать активно работающие ресурсы, например, действующая виртуальная машина или учетная запись хранения, содержащая некоторые данные. Если вы не знаете, есть ли у вас активные ресурсы в Azure Stack Marketplace, разверните виртуальную машину, откройте для нее колонку мониторинга и проверьте, выполняется ли виртуальная машина. Следующие командлеты PowerShell позволяют просмотреть данные о потреблении:

1. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
2. [Настройка пользователя](../user/azure-stack-powershell-configure-user.md) или [оператора Azure Stack](azure-stack-powershell-configure-admin.md) в среде PowerShell.
3. Чтобы получить данные об использовании, вызовите PowerShell [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage).

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST API

Вы можете собирать сведения об использовании для удаленных подписок путем вызова службы Microsoft.Commerce.Admin.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Возврат данных об использовании по всем клиентам для удаленных подписок для активных пользователей

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Возврат данных об использовании по удаленному или активному клиенту

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>Дополнительная информация

- [API-интерфейс использования для клиентов](azure-stack-tenant-resource-usage-api.md)
- [Часто задаваемые вопросы об использовании](azure-stack-usage-related-faq.md)
