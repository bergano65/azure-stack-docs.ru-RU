---
title: Выполнение запросов API к Azure Stack | Документация Майкрософт
description: Узнайте, как получить аутентификацию Azure для отправки запросов через API Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 822d05c53db2d55b3cddac44fa919c72e9af2efe
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714649"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack"></a>Выполнение запросов API к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

REST API Azure Stack можно использовать для автоматизации таких операций, как добавление виртуальной машины в облако Azure Stack.

Чтобы использовать такие API, клиенту нужно выполнить аутентификацию в конечной точке входа Microsoft Azure. Эта конечная точка возвращает маркер, который нужно указать в заголовке каждого запроса к API Azure Stack. Microsoft Azure использует OAuth 2.0.

В этой статье приведены примеры, в которых для создания запросов к Azure Stack используется служебная программа **cURL**. cURL является средством командной строки с библиотекой для передачи данных. В этих примерах демонстрируется пошаговый процесс получения маркера для доступа к API Azure Stack. Большинство языков программирования предоставляет библиотеки OAuth 2.0, в которых реализованы надежные возможности управления маркерами и обработки таких задач, как обновление маркеров.

Изучив полный процесс применения REST API Azure Stack с универсальным клиентом REST на примере **cURL**, вы сможете составить представление о базовых запросах и полезных данных, которые можно получить в ответ на них.

В этой статье не рассматриваются некоторые методы получения маркеров, например интерактивный вход или создание выделенных идентификаторов приложений. Сведения на эту тему см. в [справочнике по REST API Azure](/rest/api/).

## <a name="get-a-token-from-azure"></a>Получение маркера от Azure

Чтобы получить маркер доступа, создайте текст запроса, отформатированный с использованием типа содержимого `x-www-form-urlencoded`. Отправьте его в запросе POST в конечную точку REST для аутентификации и входа в Azure.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Идентификатор клиента** может иметь следующие значения:

- Ваш домен клиента, такой как `fabrikam.onmicrosoft.com`
- Идентификатор клиента, такой как `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- Значение по умолчанию для ключей, не привязанных к конкретному клиенту, `common`

### <a name="post-body"></a>Текст запроса POST

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Описание значений:

- **grant_type**  
   Тип схемы аутентификации, которую вы будете использовать. В этом примере используется значение `password`.

- **resource**  
   Ресурс, к которому маркер предоставляет доступ. Ресурс можно узнать с помощью запроса к конечной точке метаданных управления Azure Stack. Нужные данные содержатся в разделе **audiences**.

- **Конечная точка управления Azure Stack**

   ```bash
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Если вам, как администратору, нужен доступ к API клиента, обязательно используйте конечную точку клиента, например `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`.

  Пример для конечной точки на основе Пакета средств разработки Azure Stack приведен ниже:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Ответ:

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Пример

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  Для этого параметра жестко задано значение по умолчанию:

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Для альтернативных сценариев доступны другие варианты:

  | Приложение | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **username**

  Например, учетная запись Azure AD Azure Stack:

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **password**

  Пароль администратора Azure AD Azure Stack.

### <a name="example"></a>Пример

Запрос:

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Ответ:

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Запросы к API

Получив маркер доступа, указывайте его в заголовке каждого запроса к API. Чтобы добавить его в качестве заголовка, создайте заголовок **authorization** со значением `Bearer <access token>`. Например:

Запрос:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Ответ:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Структура URL-адреса и синтаксис запросов

Стандартный URI запроса состоит из: `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **Схема URI:**  
URI, обозначающий протокол для отправки запроса. Например, `http` или `https`.
- **Узел URI:**  
узел обозначает доменное имя или IP-адрес сервера, на котором размещается конечная точка REST, например `graph.microsoft.com` или `adminmanagement.local.azurestack.external`.
- **Путь к ресурсу:**  
путь однозначно определяет ресурс или коллекцию ресурсов и может содержать несколько сегментов, которые служба использует для идентификации нужных ресурсов. Например: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` запрашивает список владельцев определенного приложения в коллекции приложений.
- **Строка запроса:**  
эта строка предоставляет дополнительные простые параметры, например нужную версию API или критерий для фильтрации ресурсов.

## <a name="azure-stack-request-uri-construct"></a>Конструкция URI для запроса к Azure Stack

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Синтаксис URI

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Пример URI запроса

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании конечных точек REST в Azure см. в [Справочнике по REST API Azure](/rest/api/).
