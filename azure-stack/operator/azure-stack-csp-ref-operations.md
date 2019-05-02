---
title: Регистрация клиентов для отслеживания использования Azure Stack | Документация Майкрософт
description: Сведения об операциях для управления регистрацией клиентов и о том, как отслеживать использование клиента в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 7714d051c21a84ea1acdedff608f3c5ab4df1d51
ms.sourcegitcommit: 24d5c16132d4c40a760ad6f631739af86188a09f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64910133"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Управление регистрацией клиента в Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье содержатся сведения об операциях регистрации. Они позволяют выполнить такие задачи:
- Управление регистрацией клиента.
- Управление отслеживанием использования клиента.

Кроме того, вы узнаете, как добавлять, перечислять и удалять сопоставления клиентов. Отслеживанием использования можно управлять при помощи PowerShell или конечных точек API выставления счетов. Кроме того, вы узнаете, как добавлять, перечислять и удалять сопоставления клиентов. Отслеживанием использования можно управлять при помощи PowerShell или конечных точек API выставления счетов.

## <a name="add-tenant-to-registration"></a>Добавление клиента в регистрацию

Чтобы добавить новый клиент в регистрацию, нужно выполнить операцию. Данные об использовании клиента передаются подписке Azure, связанной с клиентом Azure Active Directory (Azure AD).

Кроме того, операция позволяет изменить подписку, связанную с клиентом. Вызовите PUT/New-AzureRMResource, чтобы перезаписать предыдущее сопоставление.

Вы можете связать одну подписку Azure с клиентом. Если добавить вторую подписку в существующий клиент, первая подписка перезаписывается.

### <a name="use-api-profiles"></a>Использование профилей API

Командлеты регистрации, представленные в этой статье, требуют указания профиля API при выполнении PowerShell. Профили API представляют набор поставщиков ресурсов Azure и их версий API. Они помогают использовать правильную версию API при взаимодействии с несколькими облаками Azure. Например, вы используете несколько облаков при работе с глобальной средой Azure и Azure Stack. В профилях задается имя, которое соответствует их дате выпуска. В этой статье необходимо использовать профиль **2017-09-03**.

Дополнительные сведения о профилях API и Azure Stack см. в статье [Управление профилями версий API в Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Параметры

| Параметр                  | ОПИСАНИЕ |
|---                         | --- |
| registrationSubscriptionID | Подписка Azure, которая использовалась для первоначальной регистрации. |
| customerSubscriptionID     | Подписка Azure (не Azure Stack), принадлежащая клиенту, для которого выполняется регистрация. Ее нужно создать в предложении поставщика облачных служб в Центре партнеров. Если у пользователя имеется несколько клиентов, подписку нужно создать в клиенте, который будет использоваться для входа в Azure Stack. |
| resourceGroup              | Группа ресурсов Azure, в которой хранятся данные об этой регистрации. |
| registrationName           | Имя регистрации Azure Stack. Это объект, который хранится в Azure. Имя обычно представлено в формате azurestack-CloudID, где CloudID — это идентификатор облака для развертывания Azure Stack. |

> [!Note]  
> Клиенты должны быть зарегистрированы в каждом используемым ими экземпляре Azure Stack. Если клиент использует несколько экземпляров Azure Stack, обновите первоначальные регистрации каждого развертывания с использованием подписки клиента.

### <a name="powershell"></a>PowerShell

Для добавления арендатора используйте командлет New-AzureRmResource. [Подключитесь к Azure Stack](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Вызов API

**Operation:** ОТПРАВКА  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 201 Создано  
**Текст ответа**: Empty  

## <a name="list-all-registered-tenants"></a>Перечисление всех зарегистрированных клиентов

Вы можете получить список всех клиентов, добавленных в регистрацию.

 > [!Note]  
 > Если клиенты не зарегистрированы, вы не получите ответ.

### <a name="parameters"></a>Параметры

| Параметр                  | ОПИСАНИЕ          |
|---                         | ---                  |
| registrationSubscriptionId | Подписка Azure, которая использовалась для первоначальной регистрации.   |
| resourceGroup              | Группа ресурсов Azure, в которой хранятся данные об этой регистрации.    |
| registrationName           | Имя регистрации Azure Stack. Это объект, который хранится в Azure. Обычно имя указывается в формате **azurestack**-***CloudID***, где ***CloudID*** — это идентификатор облака для развертывания Azure Stack.   |

### <a name="powershell"></a>PowerShell

Для перечисления всех зарегистрированных клиентов воспользуйтесь командлетом Get-AzureRmResource. [Подключитесь к Azure Stack](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Список всех сопоставлений арендатора можно получить с помощью операции GET.

**Operation:** ПОЛУЧЕНИЕ  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 200  
**Текст ответа**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Удаление сопоставления клиента

Вы можете удалить клиент, добавленный в регистрацию. Если этот клиент по-прежнему использует ресурсы Azure Stack, их использование относится на счет подписки, которая применялась при первоначальной регистрации Azure Stack.

### <a name="parameters"></a>Параметры

| Параметр                  | ОПИСАНИЕ          |
|---                         | ---                  |
| registrationSubscriptionId | Идентификатор подписки для регистрации.   |
| resourceGroup              | Группа ресурсов для регистрации.   |
| registrationName           | Имя регистрации.  |
| customerSubscriptionId     | Идентификатор подписки клиента.  |

### <a name="powershell"></a>PowerShell

Для удаления арендатора используйте командлет Remove-AzureRmResource. [Подключитесь к Azure Stack](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Вы можете удалить сопоставления клиента с помощью операции DELETE.

**Operation:** УДАЛИТЬ  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 204 No Content (содержимое отсутствует)  
**Текст ответа**: Empty

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения см. в статье о [потреблении ресурсов и выставлении счетов в Azure Stack](azure-stack-billing-and-chargeback.md).
