---
title: Использование профилей версий API и .NET в Azure Stack Hub
description: Узнайте, как использовать профили версий API с помощью пакета SDK для .NET в Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 6f8220f9a8683569c23460acf2890c9aa8407f30
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883088"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack-hub"></a>Использование профилей версий API и .NET в Azure Stack Hub

Пакет SDK .NET для Azure Stack Hub Resource Manager предоставляет средства для создания и администрирования инфраструктуры. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и [Key Vault](/azure/key-vault/key-vault-whatis). Пакет SDK для .NET включает в себя 14 пакетов NuGet. Эти пакеты необходимо скачивать в решение каждый раз при компиляции проекта. Но вы можете явным образом указать поставщика ресурсов, который будет использоваться для версии **2019-03-01-hybrid** или **2018-03-01-hybrid**, чтобы оптимизировать использование памяти приложением. Каждый пакет содержит поставщик ресурсов, соответствующую версию API и профиль API, к которой она принадлежит. Профили API в пакете SDK .NET упрощают разработку гибридных облачных приложений, помогая переключаться между глобальными ресурсами Azure и ресурсами в Azure Stack Hub.

## <a name="net-and-api-version-profiles"></a>Профили версии API и .NET

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

- Чтобы получить последние версии всех служб, используйте профиль **latest** в пакете. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

- Чтобы использовать службы, совместимые с Azure Stack Hub, воспользуйтесь одним из следующих пакетов:
  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**
  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  Сегмент **ResourceProvider** в имени пакета NuGet всегда должен соответствовать выбранному поставщику.

- Чтобы использовать последнюю версию API службы, укажите профиль **Latest** для определенного пакета NuGet. Например, если вы хотите использовать версию **latest-API** службы вычислений, выберите профиль **latest** пакета **Compute**. Профиль **latest** входит в пакет NuGet **Microsoft.Azure.Management**.

- Чтобы использовать конкретные версии API для некоторого типа ресурса от конкретного поставщика, выберите соответствующую версию API, определенную в пакете.

Можно объединить все параметры в одном приложении.

## <a name="install-the-azure-net-sdk"></a>Установка пакета SDK .NET для Azure

- Установите Git. Инструкции см. в разделе по [Getting Started - Installing Git][].

- Чтобы установить правильные пакеты NuGet, воспользуйтесь [Finding and installing a package][].

- Набор устанавливаемых пакетов зависит от версии профиля, который вы намерены использовать. Имена пакетов для версий профилей:

  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

- Чтобы установить правильные пакеты NuGet для Visual Studio Code, скачайте [NuGet Package Manager instructions][].

- Создайте подписку, если ее еще нет, и сохраните ее идентификатор для дальнейшего использования. Сведения о создании подписок для предложений в Azure Stack Hub см. в [Создание подписок для предложений в Azure Stack Hub][].

- Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Информацию о создании субъекта-службы для Azure Stack Hub см. в статье [Предоставление приложениям доступа к Azure Stack Hub][]. Идентификатор клиента при создании субъекта-службы называется идентификатором приложения.

- Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Предоставление приложениям доступа к Azure Stack Hub][].

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для .NET и Azure Stack Hub, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для используемой операционной системы.

| Значение                     | Переменные среды   | Описание                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID                 | `AZURE_TENANT_ID `      | Значение [*идентификатора клиента*][] Azure Stack Hub.                                                                          |
| Идентификатор клиента                 | `AZURE_CLIENT_ID `      | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. |
| Идентификатор подписки           | `AZURE_SUBSCRIPTION_ID` | [*Идентификатор подписки*][] для доступа к предложениям в Azure Stack Hub.                                                      |
| Секрет клиента             | `AZURE_CLIENT_SECRET`   | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы.                                      |
| Конечная точка Resource Manager | `ARM_ENDPOINT`          | Дополнительные сведения см. в разделе [*Конечная точка Resource Manager для Azure Stack Hub*][].                                                                    |
| Расположение                  | `RESOURCE_LOCATION`     | Расположение Azure Stack Hub.

Чтобы узнать идентификатор клиента для Azure Stack Hub, выполните приведенные [в этой статье](../operator/azure-stack-csp-ref-operations.md) инструкции. Чтобы настроить переменные среды, сделайте следующее:

### <a name="windows"></a>Windows

Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:

```shell
set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix используйте такую команду:

```shell
export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack Hub

Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Получить метаданные можно из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска вашего кода.

Обратите внимание на следующие моменты:

- **ResourceManagerUrl** в Пакете средств разработки Azure Stack (ASDK) имеет значение https://management.local.azurestack.external/.

- **ResourceManagerUrl** в интегрированной системе: `https://management.region.<fqdn>/`, где `<fqdn>` — полное доменное имя.
Чтобы получить необходимые метаданные, используйте `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Пример JSON-файла:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
         "loginEndpoint": "https://login.windows.net/",
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Существующие профили API

- **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: последний профиль, созданный для Azure Stack Hub. Используйте этот профиль, чтобы обеспечить максимальную совместимость служб и Azure Stack Hub, если вы используете версию 1904 или более позднюю.

- **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: Используйте этот профиль, чтобы обеспечить совместимость служб и Azure Stack Hub для версии 1808 или более поздней.

- **Latest**: профиль с новейшими версиями всех служб. Используйте последние версии всех служб. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

См. дополнительные сведения об [Summary of API profiles][].

## <a name="azure-net-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для .NET

Чтобы создать экземпляр клиента управления ресурсами, используйте следующий код. Подобный код можно использовать для создания экземпляров других клиентов поставщика ресурсов (например, вычисления, сети и хранилища).

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Параметр `credentials` в приведенном выше коде необходим для создания экземпляра клиента. Следующий код создает маркер аутентификации на основе идентификатора клиента и субъекта-службы:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

Вызов `getActiveDirectoryServiceSettings` в этом коде извлекает конечные точки Azure Stack Hub из конечной точки метаданных. Он использует переменные среды из выполняемого вызова.

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Эти шаги позволяют использовать пакеты NuGet профиля API для успешного развертывания приложения в Azure Stack Hub.

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры в качестве образцов при создании решений с профилями API для Azure Stack Hub и .NET:

- [Управление группами ресурсов](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Управление учетными записями хранения](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Управление виртуальной машиной.](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm) В этом примере используется профиль версии **2019-03-01-hybrid**, поддерживаемый Azure Stack Hub.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о профилях API:

- [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md)
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)

  [Getting Started - Installing Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git (Приступая к работе — установка Git)
  [Finding and installing a package]: /nuget/tools/package-manager-ui (Поиск и установка пакета)
  [NuGet Package Manager instructions]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager (Инструкции по диспетчеру пакетов NuGet)
  [Создание подписок для предложений в Azure Stack Hub]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Предоставление приложениям доступа к Azure Stack Hub]: ../operator/azure-stack-create-service-principals.md
  [*идентификатора клиента*]: ../operator/azure-stack-identity-overview.md
  [*Идентификатор подписки*]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [*Конечная точка Resource Manager для Azure Stack Hub*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint
  [Summary of API profiles]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles (Сводка по профилям API)
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
