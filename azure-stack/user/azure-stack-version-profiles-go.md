---
title: Использование профилей версий API и Go в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API и Go в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 6f9136cb92851d10deac3455b054fe3c18cb891e
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269359"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Использование профилей версий API и Go в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="go-and-version-profiles"></a>Профили версий и Go

Профиль — это сочетание разных типов ресурсов с разными версиями от различных служб. С помощью профиля можно комбинировать и сопоставлять разные типы ресурсов. Профили обеспечивают следующие преимущества:

- стабильность вашего приложения за счет блокировки определенных версий API;
- совместимость вашего приложения с Azure Stack и региональными центрами обработки данных Azure.

В пакете SDK для Go профили доступны в пути к профилям с версией в формате **ГГГГ-ММ-ДД**. **2018-03-01** — последняя версия профиля API Azure Stack на данный момент. Чтобы импортировать данную службу из профиля, импортируйте из профиля ее соответствующий модуль. Например, чтобы импортировать службу **вычислений** из профиля версии **2018-03-01**, используйте следующий код:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Установка пакета Azure SDK для Go

1. Установите Git. Инструкции см. в разделе по [установке Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Установите [язык программирования Go](https://golang.org/dl). Для профилей API для Azure необходима версия Go 1.9 или более новая.
3. Установите пакет Azure SDK для Go и его зависимости, выполнив следующую команду Bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Пакет SDK для GO

Дополнительные сведения о пакете SDK для GO можно найти по следующим ссылкам:

- [Установка пакета Azure SDK для Go](/go/azure/azure-sdk-go-install).
- Пакет Azure SDK для Go доступен на сайте GitHub в репозитории [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Зависимости Go-AutoRest

Для отправки запросов REST конечным точкам Azure Resource Manager пакету SDK для Go необходимы модули Azure **Go-AutoRest**. Вам нужно импортировать зависимости модуля Azure **Go-AutoRest** из репозитория [Go-AutoRest Azure на сайте GitHub](https://github.com/Azure/go-autorest). Команды Bash для установки можно найти в разделе **установки**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Использование профилей пакета SDK для GO в Azure Stack

Чтобы запустить пример кода GO в Azure Stack, сделайте следующее:

1. Установите пакет Azure SDK для Go и его зависимости. Инструкции см. в предыдущем разделе: [Установка пакета Azure SDK для Go](#install-azure-sdk-for-go).
2. Получите метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода Go.

   > [!NOTE]  
   > В Пакете средств разработки Azure Stack (ASDK) **ResourceManagerUrl**: `https://management.local.azurestack.external/`.  
   > В интегрированных системах **ResourceManagerUrl**: `https://management.<region>.<fqdn>/`.  
   > Чтобы получить необходимые метаданные, используйте: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.
  
   Пример JSON-файла:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Создайте подписку, если ее еще нет, и сохраните ее идентификатор для дальнейшего использования. Сведения о создании подписки см. в статье [Создание подписок для предложений в Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Создайте субъект-службу с областью **Подписка** и ролью **Владелец**. Сохраните идентификатор и секрет субъекта-службы. Сведения о создании субъекта-службы для Azure Stack см. в разделе [Создание субъекта-службы для Azure AD](azure-stack-create-service-principals.md). Среда Azure Stack теперь настроена.

5. Импортируйте модуль службы из профиля пакета SDK для Go в код. Текущая версия профиля Azure Stack — **2018-03-01**. Например, чтобы импортировать модуль сети из профиля версии **2018-03-01**, используйте следующий код:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/network/mgmt/network"
   ```

6. В функции создайте и проверьте подлинность клиента с помощью вызова функции **создания** клиента. Для создания клиента виртуальной сети можно использовать указанный ниже код.  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Задайте для параметра `<baseURI>` значение **ResourceManagerUrl**, используемое на шаге 2. Задайте для параметра `<subscriptionID>` значение **SubscriptionID**, сохраненное на шаге 3.

   Чтобы создать токен, выполните инструкции из следующего раздела.  

7. Вызовите методы API с помощью созданного на предыдущем шаге клиента. Ниже приведен пример создания виртуальной сети с помощью клиента из предыдущего шага.

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Полный пример создания виртуальной сети в Azure Stack с использованием профиля пакета SDK для Go см. [здесь](#example).

## <a name="authentication"></a>Аутентификация

Чтобы получить свойство **авторизации** из Azure Active Directory с помощью пакета SDK для Go, установите модули **Go-AutoRest**. Эти модули должны быть уже установлены с установкой "Пакет SDK для Go". В противном случае установите [пакет проверки подлинности с сайта GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Свойство авторизации необходимо задать в качестве авторизации для клиента ресурсов. Здесь представлены различные способы получения токенов авторизации в Azure Stack с использованием учетных данных клиента:

1. Если в подписке доступен субъект-служба с ролью владельца, пропустите этот шаг. В противном случае создайте [субъект-службу](azure-stack-create-service-principals.md) и назначьте ему роль "владельца" для своей [подписки](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Сохраните идентификатор приложения и секрет субъекта-службы.

2. Импортируйте пакет **adal** из Go-AutoRest в код.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Создайте **oauthConfig** с помощью метода NewOAuthConfig из модуля **adal**.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Задайте для параметра `<activeDirectoryEndpoint>` значение свойства `loginEndpoint` из метаданных `ResourceManagerUrl`, полученных при работе с предыдущим разделом этого документа. Задайте для параметра `<tenantID>` идентификатор клиента в Azure Stack.

4. Наконец, создайте токен субъекта-службы с помощью метода `NewServicePrincipalToken` из модуля **adal**.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Задайте для параметра `<activeDirectoryResourceID>` одно из значений в списке audience из метаданных **ResourceManagerUrl**, полученных при работе с предыдущим разделом этой статьи.
    Задайте для параметра `<clientID>` идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи.
    Задайте для параметра `<clientSecret>` секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи.

## <a name="example"></a>Пример

В этом примере показан код Go, который создает виртуальную сеть в Azure Stack. Полные примеры с использованием пакета SDK для Go см. в [этом репозитории](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Примеры Azure Stack можно найти в пути hybrid внутри папок службы репозитория.

> [!NOTE]  
> Чтобы запустить код в этом примере, убедитесь, что в используемой подписке для **поставщика сетевых ресурсов** указано **Зарегистрирован**. Чтобы проверить это, найдите подписку на портале Azure Stack и щелкните **Поставщики ресурсов**.

1. Импортируйте необходимые пакеты в свой код. Для импорта модуля сети необходима последняя версия профиля Azure Stack.

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Определите переменные своей среды. Для создания виртуальной сети необходима группа ресурсов.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Теперь, когда вы определили переменные среды, добавьте метод для создания токена проверки подлинности с помощью пакета **adal**. Дополнительные сведения о проверке подлинности см. в предыдущем разделе.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Добавьте метод `main`. Метод `main` сначала получает токен с помощью метода, определенного на предыдущем шаге. Затем он создает клиент с помощью модуля сети из профиля. И наконец, он создает виртуальную сеть.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2018-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletion(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
- [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)  
