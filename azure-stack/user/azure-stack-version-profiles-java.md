---
title: Использование профилей версий API с помощью Java в Azure Stack Hub | Документация Майкрософт
description: Узнайте, как использовать профили версий API с помощью Java в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 37481cee1e7bc5b9bee0e68878077e084369ca39
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883190"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack-hub"></a>Использование профилей версий API с помощью Java в Azure Stack Hub

Пакет SDK для Resource Manager Azure Stack Hub для Java предоставляет средства для создания и администрирования инфраструктуры. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и [Key Vault](/azure/key-vault/key-vault-whatis).

Пакет SDK для Java добавляет профили API, включая зависимости в файл **Pom.xml**, который загружает правильные модули из **JAVA-файла**. Но вы можете указать в качестве зависимостей несколько профилей Azure, например **2019-03-01-hybrid** или **latest**. Применение этих зависимостей позволяет загрузить правильный модуль, чтобы при создании типа ресурса вы могли выбрать из этих профилей версию API, которую нужно использовать. Это позволяет использовать в Azure последние стабильные версии, а для разработки — самые свежие версии API для Azure Stack Hub.

Пакет SDK для Java позволяет создать полноценную гибридную облачную среду. Профили API в пакете SDK для Java позволяют выполнять разработку гибридных облачных приложений, легко переключаясь между глобальными ресурсами Azure и ресурсами в Azure Stack Hub.

## <a name="java-and-api-version-profiles"></a>Использование профилей версии API с помощью Java

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

- Чтобы получить последние версии всех служб, укажите профиль **Latest** в качестве зависимости.

  - Чтобы использовать самый новый профиль, укажите зависимость **com.microsoft.azure**.

  - Чтобы использовать последние службы, поддерживаемые Azure Stack Hub, укажите профиль **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**.

    - Этот профиль значение нужно указать в качестве зависимости в файле **Pom.xml**, чтобы модули загружались автоматически при выборе нужного класса из раскрывающегося списка, как в .NET.

  - Зависимости оформляются так:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Чтобы использовать конкретные версии API для определенного типа ресурса от конкретного поставщика, выберите соответствующую версию API, определенную с помощью IntelliSense.

Можно объединить все параметры в одном приложении.

## <a name="install-the-azure-java-sdk"></a>Установка пакета Azure SDK для Java

Чтобы установить пакет SDK для Java, выполните следующие шаги.

1. Выполните официальные инструкции по установке Git. См. статью [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Приступая к работе — установка Git)

2. Выполните инструкции по установке [пакета SDK для Java](https://zulu.org/download/) и [Maven](https://maven.apache.org/). Следует использовать Java Developer Kit версии 8. Следует использовать Apache Maven версии 3.0 или более поздней. Для выполнения инструкций из этого краткого руководства сохраните в переменной среды `JAVA_HOME` расположение установки JDK. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Чтобы установить правильные пакеты зависимостей, откройте файл **Pom.xml** в приложении Java. Добавьте в него зависимость, как показано в следующем коде:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Набор устанавливаемых пакетов зависит от версии профиля, который вам нужен. Имена пакетов для версий профилей:

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **Актуальная**

5. При необходимости создайте подписку и сохраните ее идентификатор, чтобы использовать его позднее. Инструкции по созданию подписки для предложений в Azure Stack Hub см. в [этой статье](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Инструкции по созданию субъекта-службы для Azure Stack Hub см. в руководстве по [предоставлению приложениям доступа к Azure Stack Hub](../operator/azure-stack-create-service-principals.md). Идентификатор клиента при создании субъекта-службы называется идентификатором приложения.

7. Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье о [предоставлении приложениям доступа к Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>предварительные требования

Чтобы использовать пакет SDK Azure для Java и Azure Stack Hub, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для соответствующей операционной системы.

| Значение                     | Переменные среды | Description                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tenant ID                 | `AZURE_TENANT_ID`            | [Идентификатор клиента](../operator/azure-stack-identity-overview.md) Azure Stack Hub.                                                          |
| Идентификатор клиента                 | `AZURE_CLIENT_ID`             | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе.                                                                                              |
| Идентификатор подписки           | `AZURE_SUBSCRIPTION_ID`      | [Идентификатор подписки](../operator/service-plan-offer-subscription-overview.md#subscriptions) используется для доступа к предложениям в Azure Stack Hub.                |
| Секрет клиента             | `AZURE_CLIENT_SECRET`        | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы.                                                                                                                                   |
| Конечная точка Resource Manager | `ARM_ENDPOINT`              | Дополнительные сведения см. в разделе о [конечной точке Resource Manager для Azure Stack Hub](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint). |
| Location                  | `RESOURCE_LOCATION`    | **Локальное значение** для Azure Stack Hub.                                                                                                                                                                                                |

Чтобы узнать идентификатор клиента для Azure Stack Hub, выполните приведенные [здесь](../operator/azure-stack-csp-ref-operations.md) инструкции. Чтобы настроить переменные среды, выполните процедуры из следующих разделов.

### <a name="microsoft-windows"></a>Microsoft Windows

Используйте следующие команды, чтобы настроить переменные среды в командной строке Windows:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix используйте такую команду:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Установка доверия для корневого сертификата ЦС Azure Stack Hub

Если вы используете Пакет средств разработки Azure Stack (ASDK), нужно настроить доверите корневому сертификату ЦС на удаленном компьютере. Для интегрированных систем Azure Stack Hub доверие корневому сертификату ЦС не требуется.

#### <a name="windows"></a>Windows

1. Экспортируйте самозаверяющий сертификат Azure Stack Hub на компьютер.

1. В командной строке перейдите в каталог `%JAVA_HOME%\bin`.

1. Выполните следующую команду:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack Hub

Azure Resource Manager — это платформа управления, которая дает администраторам возможность развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Получить метаданные можно из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска вашего кода.

Обратите внимание на следующие моменты:

- **ResourceManagerUrl** в ASDK имеет следующее значение: `https://management.local.azurestack.external/`.

- **ResourceManagerUrl** в интегрированных системах имеет значение `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Существующие профили API

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**: последний профиль, созданный для Azure Stack Hub. Используйте этот профиль, чтобы обеспечить наибольшую совместимость служб и Azure Stack Hub с версией 1904 или более поздней.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: профиль, созданный для Azure Stack Hub. Используйте этот профиль, чтобы обеспечить совместимость служб и Azure Stack Hub для версии 1808 или более поздней.

- **com.microsoft.azure**: профиль с новейшими версиями всех служб. Используйте последние версии всех служб.

См. дополнительные сведения об [Azure Stack Hub и профилях API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для Java

Следующий код выполняет проверку подлинности субъекта-службы в Azure Stack Hub. Для этого создается маркер с использованием идентификатора клиента и базы проверки подлинности для Azure Stack Hub:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Этот код дает возможность использовать зависимости профиля API для успешного развертывания приложения в Azure Stack Hub.

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Определение функций параметров среды Azure Stack Hub

Чтобы зарегистрировать облако Azure Stack Hub с правильными конечными точками, используйте следующий код:

```java
// Get Azure Stack Hub cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Вызов `getActiveDirectorySettings` в коде выше извлекает конечные точки из конечных точек метаданных. Он использует переменные среды из выполняемого вызова.

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Используйте примеры из репозитория GitHub в качестве рекомендаций при создании решений с профилями API Azure Stack Hub и .NET:

- [Getting Started with Resources - Manage Resource Group - in .Net](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group) (Начало работы с ресурсами: управление группами ресурсов — .Net)

- [Управление учетными записями хранения](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Управление виртуальной машиной](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (с учетом профиля 2019-03-01-hybrid).

### <a name="sample-unit-test-project"></a>Пример проекта модульного теста

1. Клонируйте репозиторий, используя следующую команду:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Создайте субъект-службу Azure и назначьте роль для доступа к подписке. См. дополнительные сведения о [создании субъекта-службы с сертификатом с помощью Azure PowerShell](../operator/azure-stack-create-service-principals.md).

3. Получите следующие обязательные переменные среды:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. С помощью командной строки настройте следующие переменные среды, используя данные, полученные от созданного субъекта-службы.

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack Hub}`

   В Windows используйте **set** вместо **export**.

5. Используйте функцию `getActiveDirectorySettings` для получения сведений о конечных точках метаданных Azure Resource Manager.

    ```java
    // Get Azure Stack Hub cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. В файл **Pom.xml** добавьте указанную ниже зависимость, чтобы использовать профиль **2019-03-01-hybrid** для Azure Stack Hub. Зависимость позволяет установить связанные с этим профилем модули поставщиков ресурсов вычислений, сети, хранилища, Key Vault и Службы приложений.

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. В окне командной строки, которое вы ранее открыли для настройки переменных среды, введите следующую команду:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о профилях API:

- [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md)
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
