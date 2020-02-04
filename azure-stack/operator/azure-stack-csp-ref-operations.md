---
title: Регистрация клиентов для отслеживания использования в Azure Stack Hub
description: Сведения о регистрации клиентов и отслеживании использования клиентов в Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 1f5f3011c1546970a1b94773b171e6419757c461
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882494"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>Регистрация клиентов для отслеживания использования в Azure Stack Hub

В этой статье содержатся сведения об операциях регистрации. Они позволяют выполнить такие задачи:

- Управление регистрацией клиента.
- Управление отслеживанием использования клиента.

## <a name="add-tenant-to-registration"></a>Добавление клиента в регистрацию

Вы можете использовать эту операцию, чтобы добавить новый клиент в регистрацию. Данные об использовании клиента передаются в подписку Azure, связанную с клиентом Azure Active Directory (Azure AD).

Кроме того, эта операция позволяет изменить подписку, связанную с клиентом. Вызовите PUT или командлет PowerShell **New-AzureRMResource**, чтобы перезаписать предыдущее сопоставление.

Вы можете связать одну подписку Azure с клиентом. При попытке добавить вторую подписку в существующий клиент первая подписка перезаписывается.

### <a name="use-api-profiles"></a>Использование профилей API

Указанные ниже командлеты регистрации требуют указывать профиль API при выполнении PowerShell. Профили API представляют набор поставщиков ресурсов Azure и их версий API. Они помогают использовать правильную версию API при взаимодействии с несколькими облаками Azure. Например, если вы используете несколько облаков при работе с глобальной средой Azure и Azure Stack Hub, профили API определяют имя, соответствующее их дате выпуска. Вы используете профиль **2017-09-03**.

Дополнительные сведения о профилях API и Azure Stack Hub см. в статье [Управление профилями версий API в Azure Stack Hub](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Параметры

| Параметр                  | Описание |
|---                         | --- |
| registrationSubscriptionID | Подписка Azure, которая использовалась для первоначальной регистрации. |
| customerSubscriptionID     | Подписка Azure (не Azure Stack Hub), принадлежащая клиенту, для которого выполняется регистрация. Ее нужно создать в предложении поставщика облачных решений через Центр партнеров. Если у пользователя имеется несколько клиентов, подписку нужно создать в клиенте, который будет использоваться для входа в Azure Stack Hub. |
| resourceGroup              | Группа ресурсов Azure, в которой хранятся данные об этой регистрации. |
| registrationName           | Имя регистрации Azure Stack Hub. Это объект, который хранится в Azure. Имя обычно представлено в формате **azurestack-CloudID**, где **CloudID** — это идентификатор облака для развертывания Azure Stack Hub. |

> [!NOTE]  
> Клиенты должны быть зарегистрированы в каждом используемом ими экземпляре Azure Stack Hub. Если клиент использует несколько экземпляров Azure Stack Hub, внесите в первоначальные регистрации каждого развертывания данные о подписке клиента.

### <a name="powershell"></a>PowerShell

Для добавления клиента используйте командлет **New-AzureRmResource**. [Подключитесь к Azure Stack Hub](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

**Operation:** ОТПРАВКА  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 201 Создано  
**Текст ответа**: Empty  

## <a name="list-all-registered-tenants"></a>Перечисление всех зарегистрированных клиентов

Вы можете получить список всех клиентов, добавленных в регистрацию.

 > [!NOTE]  
 > Если клиенты не зарегистрированы, вы не получите ответ.

### <a name="parameters"></a>Параметры

| Параметр                  | Описание          |
|---                         | ---                  |
| registrationSubscriptionId | Подписка Azure, которая использовалась для первоначальной регистрации.   |
| resourceGroup              | Группа ресурсов Azure, в которой хранятся данные об этой регистрации.    |
| registrationName           | Имя регистрации для развертывания Azure Stack Hub. Это объект, который хранится в Azure. Имя обычно представлено в формате **azurestack-CloudID**, где **CloudID** — это идентификатор облака для развертывания Azure Stack Hub.   |

### <a name="powershell"></a>PowerShell

Для перечисления всех зарегистрированных клиентов воспользуйтесь командлетом **Get-AzureRmResource**. [Подключитесь к Azure Stack Hub](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Список всех сопоставлений арендатора можно получить с помощью операции GET.

**Operation:** GET  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 200  
**Текст ответа**:

```json
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

Вы можете удалить клиент, добавленный в регистрацию. Если этот клиент по-прежнему использует ресурсы Azure Stack Hub, их использование относится на счет подписки, которая применялась при первоначальной регистрации Azure Stack Hub.

### <a name="parameters"></a>Параметры

| Параметр                  | Описание          |
|---                         | ---                  |
| registrationSubscriptionId | Идентификатор подписки для регистрации.   |
| resourceGroup              | Группа ресурсов для регистрации.   |
| registrationName           | Имя регистрации.  |
| customerSubscriptionId     | Идентификатор подписки клиента.  |

### <a name="powershell"></a>PowerShell

Для удаления клиента используйте командлет **Remove-AzureRmResource**. [Подключитесь к Azure Stack Hub](azure-stack-powershell-configure-admin.md), а затем в командной строке с повышенными привилегиями выполните следующий командлет:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Вызов API

Вы можете удалить сопоставления клиента с помощью операции DELETE.

**Operation:** DELETE  
**URI запроса**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Ответ**: 204 No Content (содержимое отсутствует)  
**Текст ответа**: Empty

## <a name="next-steps"></a>Дальнейшие действия

- [Потребление ресурсов и выставление счетов в Azure Stack Hub](azure-stack-billing-and-chargeback.md)
