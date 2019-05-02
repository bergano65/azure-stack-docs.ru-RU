---
title: Использование профилей версий API и SDK .NET в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API и .NET в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 3fd1e31c3f6c36bd5e3d789b14f7a7f38c9ccc44
ms.sourcegitcommit: 24d5c16132d4c40a760ad6f631739af86188a09f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64910031"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Использование профилей версий API и .NET в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пакет SDK .NET для Azure Stack Resource Manager предоставляет средства для создания и администрирования инфраструктуры. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и [KeyVault](/azure/key-vault/key-vault-whatis). Пакет SDK для .NET включает в себя 14 пакетов NuGet. Эти пакеты необходимо скачивать в каждое решение проекта, где используются сведения о профилях. Но вы можете явным образом указать поставщик ресурсов, который будет использоваться для профиля 2018-03-01-hybrid или 2017-03-09-profile, чтобы оптимизировать использование памяти в приложении. Каждый пакет содержит поставщик ресурсов, соответствующую версию API и профиль API, к которой она принадлежит. Профили API в пакете SDK .NET упрощают разработку гибридных облачных приложений, помогая переключаться между глобальными ресурсами Azure и ресурсами в Azure Stack.

## <a name="net-and-api-version-profiles"></a>Профили версии API и .NET

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

-   Чтобы получить последние версии всех служб, используйте профиль **latest** в пакете. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

-   Чтобы использовать службы, совместимые с Azure Stack, используйте пакеты **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** или **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**.

    -   Для каждого поставщика ресурсов в каждом профиле существует два пакета.

    -   Сегмент **ResourceProvider** в имени пакета NuGet всегда должен соответствовать выбранному поставщику.

-   Чтобы использовать последнюю версию API службы, используйте профиль **latest** определенного пакета NuGet. Например, если вы хотите использовать версию **latest-API** службы вычислений, выберите профиль **latest** пакета **compute**. Профиль **latest** входит в пакет NuGet **Microsoft.Azure.Management**.

-   Чтобы использовать конкретные версии API для некоторого типа ресурса от конкретного поставщика, выберите соответствующую версию API в пакете.

Можно объединить все параметры в одном приложении.

## <a name="install-the-azure-net-sdk"></a>Установка пакета SDK .NET для Azure

1.  Установите Git. Инструкции см. в разделе по [Getting Started - Installing Git][].

2.  Чтобы установить правильные пакеты NuGet, воспользуйтесь [Finding and installing a package][].

3.  Набор устанавливаемых пакетов зависит от версии профиля, который вам нужен. Имена пакетов для версий профилей:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  Чтобы установить правильные пакеты NuGet для Visual Studio Code, скачайте [NuGet Package Manager instructions][].

5.  Создайте подписку, если ее еще нет, и сохраните ее идентификатор для дальнейшего использования. Инструкции по созданию подписки см. в статье [Create subscriptions to offers in Azure Stack][].

6.  Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Инструкции по созданию субъекта-службы для Azure Stack см. в статье [Provide applications access to Azure Stack][]. Идентификатор клиента при создании субъекта-службы называется идентификатором приложения.

7.  Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Provide applications access to Azure Stack][].

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для .NET и Azure Stack, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для соответствующей операционной системы.

| Значение                     | Переменные среды   | ОПИСАНИЕ                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID                 | AZURE_TENANT_ID       | Значение [*идентификатора клиента*][] Azure Stack.                                                                          |
| Идентификатор клиента                 | AZURE_CLIENT_ID       | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. |
| Идентификатор подписки           | AZURE_SUBSCRIPTION_ID | [*Идентификатор подписки*][] для доступа к предложениям в Azure Stack.                                                      |
| Секрет клиента             | AZURE_CLIENT_SECRET   | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы.                                      |
| Конечная точка Resource Manager | ARM_ENDPOINT           | См. дополнительные сведения о [*конечной точке Resource Manager для Azure Stack*][].                                                                    |
| Расположение                  | RESOURCE_LOCATION     | Расположение для Azure Stack.

Чтобы узнать идентификатор клиента для Azure Stack, выполните приведенные [здесь](../operator/azure-stack-csp-ref-operations.md) инструкции. Чтобы настроить переменные среды, выполните следующие шаги.

### <a name="microsoft-windows"></a>Microsoft Windows

Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix можно использовать такую команду:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack

Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Вы можете получить метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода.

Обратите внимание на следующие моменты:

- В Пакете средств разработки Azure Stack (ASDK) **ResourceManagerUrl**: https://management.local.azurestack.external/.

- В интегрированных системах **ResourceManagerUrl**: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`. Чтобы получить необходимые метаданные, используйте `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**: последний профиль, созданный для Azure Stack. Используйте этот профиль для служб, которым нужна максимальная совместимость с Azure Stack с меткой 1808 или более новой.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**: если вы используете метку ниже, чем сборка 1808, используйте этот профиль.

3.  **Latest**: профиль с новейшими версиями всех служб. Используйте последние версии всех служб. Этот профиль входит в пакет NuGet **Microsoft.Azure.Management**.

См. дополнительные сведения о [Summary of API profiles][].

## <a name="azure-net-sdk-api-profile-usage"></a>Использование профиля API пакета Azure SDK .NET

Чтобы создать экземпляр клиента управления ресурсами, используйте следующий код. Подобный код можно использовать для создания других экземпляров клиентов поставщика ресурсов (например, вычисления, сети и хранилища). 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

Параметр `credentials` в приведенном выше коде необходим для создания экземпляра клиента. Следующий код создает токен проверки подлинности на основе идентификатора клиента и субъекта-службы.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
Вызов `getActiveDirectoryServiceSettings` в этом коде извлекает конечные точки Azure Stack из конечной точки метаданных. Он использует переменные среды из выполняемого вызова: 

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
Так вы сможете использовать пакеты NuGet профиля API для успешного развертывания приложения в Azure Stack.

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры в качестве рекомендаций при создании решений с профилями API Azure Stack и .NET.
- [Управление группами ресурсов](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Управление учетными записями хранения](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Управление виртуальной машиной](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о профилях API:

- [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)

  [Getting Started - Installing Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git (Приступая к работе — установка Git)
  [Finding and installing a package]: /nuget/tools/package-manager-ui (Поиск и установка пакета)
  [NuGet Package Manager instructions]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager (Инструкции по диспетчеру пакетов NuGet)
  [Create subscriptions to offers in Azure Stack]: ../operator/azure-stack-subscribe-plan-provision-vm.md (Создание подписок для предложений в Azure Stack)
  [Provide applications access to Azure Stack]: ../operator/azure-stack-create-service-principals.md (Предоставление приложениям доступа к Azure Stack)
  [*tenant ID*]: ../operator/azure-stack-identity-overview.md (Идентификатор клиента)
  [*subscription ID*]: ../operator/azure-stack-plan-offer-quota-overview.md#subscriptions (Идентификатор подписки)
  [*the Azure Stack Resource Manager endpoint*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint (Конечная точка Resource Manager для Azure Stack)
  [Summary of API profiles]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles (Сводка по профилям API)
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
