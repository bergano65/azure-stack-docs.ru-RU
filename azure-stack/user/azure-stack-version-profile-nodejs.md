---
title: Использование профилей версий API и Node.js в Azure Stack | Документация Майкрософт
description: Узнайте об использовании профилей версий API и Node.js в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: 35093371ede6e3f5f776b981eaaf8463df9e7f36
ms.sourcegitcommit: 7961fda0bfcdd3db8cf94a8c405b5c23a23643af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68616840"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack"></a>Использование профилей версий API с пакетом средств разработки (SDK) для Node.js в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="nodejs-and-api-version-profiles"></a>Node.js и профили версий API

Пакет SDK для Node.js можно использовать для создания инфраструктуры приложений и управления ею. Профили API в пакете SDK для Node.js помогают создавать гибридные облачные решения, позволяя переключаться между глобальными ресурсами Azure и ресурсами Azure Stack. Вы можете создать один код, а затем нацелить его как на глобальное облако Azure, так и на Azure Stack. 

В этой статье вы можете использовать [Visual Studio Code](https://code.visualstudio.com/) в качестве средства разработки. Visual Studio Code позволяет выполнить отладку пакета SDK для Node.js и запустить приложение, а также отправить приложение в экземпляр Azure Stack. Можно выполнить отладку из Visual Studio Code или через окно терминала, выполнив команду `node <nodefile.js>`.

## <a name="the-nodejs-sdk"></a>Пакет SDK для Node.js

Пакет SDK для Node.js предоставляет средства Resource Manager для Azure Stack. В этом пакете SDK представлены поставщики ресурсов вычислений, сети, хранилища, служб приложений и KeyVault. Существуют 10 клиентских библиотек поставщиков ресурсов, которые можно установить в приложении Node.js. Можно также скачать и указать поставщик ресурсов, который будет использоваться для профиля **2018-03-01-hybrid** или **2019-03-01-profile**, чтобы оптимизировать использование памяти в приложении. Каждый модуль содержит поставщик ресурсов, соответствующую версию API и профиль API. 

Профиль API определяет поставщик ресурсов и версии API. С помощью профиля API можно получить последнюю и наиболее стабильную версию ресурса любого типа из представленных в пакете поставщика ресурсов.

  -   Чтобы получить последние версии всех служб, используйте профиль **latest** в пакете.

  -   Чтобы использовать службы, совместимые с Azure Stack, используйте **\@azure/arm-resources-profile-hybrid-2019-03-01** или **\@azure/arm-storage-profile-2019-03-01-hybrid**.

### <a name="packages-in-npm"></a>Пакеты в npm

Каждый поставщик ресурсов имеет собственный пакет. Этот пакет можно получить из [реестра npm](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid).

Доступны следующие пакеты.

| Поставщик ресурсов | Package |
| --- | --- |
| [Служба приложений](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Подписки Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Политика Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Служба DNS Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Авторизация](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Среда выполнения приложений](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Хранилище](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Сеть](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Ресурсы](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Key Vault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

Чтобы применить последнюю версию API службы, используйте **последний** профиль определенной клиентской библиотеки. Например, если вы хотите использовать только последнюю версию API службы ресурсов, укажите профиль `azure-arm-resource` пакета **клиентской библиотеки управления ресурсами** .

Используйте конкретные версии API, определенные в этом пакете, для соответствующих версий API поставщика ресурсов.

  > [!Note]  
  > Можно объединить все параметры в одном приложении.

## <a name="install-the-nodejs-sdk"></a>Установите пакет SDK для Node.js.

1. Установите Git. Инструкции см. в разделе по [установке Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Установите компонент [Node.js](https://nodejs.org/en/download/) или обновите его до текущей версии. Node.js также включает в себя диспетчер пакетов JavaScript [npm](https://www.npmjs.com/).

3. Установите или обновите [Visual Studio Code](https://code.visualstudio.com/) и установите [расширение Node.js](https://code.visualstudio.com/docs/Node.js/nodejs-debugging) для Visual Studio Code.

2.  Установите клиентские пакеты для Resource Manger Azure Stack. Дополнительные сведения см. в разделе об [установке клиентских библиотек](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  Набор устанавливаемых пакетов зависит от версии профиля, который вам нужен. Список поставщиков ресурсов можно найти в разделе [Пакеты в npm](#packages-in-npm).

4. Установите клиентскую библиотеку поставщика ресурсов с помощью npm. В командной строке выполните команду `npm install <package-name>`. Например, можно выполнить `npm install @azure/arm-authorization-profile-2019-03-01-hybrid`, чтобы установить библиотеку поставщика ресурсов авторизации.

5.  Создайте подписку и запишите идентификатор подписки при использовании пакета SDK. Инструкции см. в разделе [Создание подписок для предложений в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Создайте субъект-службу и сохраните идентификатор клиента и секрет клиента. Идентификатор клиента при создании субъекта-службы называется идентификатором приложения. Инструкции см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md).

7.  Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Необходимые компоненты Node.js 

Чтобы использовать пакет SDK Azure для Node.js с Azure Stack, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для соответствующей операционной системы.

| Значение | Переменные среды | ОПИСАНИЕ |
| --- | --- | --- |
| Tenant ID | TENANT\_ID | Значение [идентификатора клиента](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) Azure Stack. |
| Идентификатор клиента | CLIENT\_ID | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше).  |
| Идентификатор подписки | AZURE\_SUBSCRIPTION\_ID: [идентификатор подписки](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) для доступа к предложениям в Azure Stack.  |
| Секрет клиента | APPLICATION\_SECRET | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы (см. выше). |
| Конечная точка Resource Manager | ARM\_ENDPOINT | Дополнительные сведения см. в разделе [Конечная точка Resource Manager для Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Настройка переменных среды для Node.js

Чтобы настроить переменные среды, сделайте следующее.

  - **Microsoft Windows**  

    Используйте следующий формат, чтобы настроить переменные среды в командной строке Windows.
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **macOS, Linux и системы на основе Unix**  

    Используйте следующий формат команды, чтобы настроить переменные среды в командной строке bash.

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Конечная точка Resource Manager для Azure Stack**

Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Вы можете получить метаданные из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска кода.

> [!Note]  
> **ResourceManagerUrl** в Пакете средств разработки Azure Stack (ASDK): `https://management.local.azurestack.external`. **ResourceManagerUrl** в интегрированных системах: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com`. Чтобы получить необходимые метаданные, используйте `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Пример JSON-файла:

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Существующие профили API

-  **\@azure/arm-resourceprovider-profile-2019-03-01-hybrid**

    последний профиль, созданный для Azure Stack. Используйте этот профиль для служб, которым нужна максимальная совместимость с Azure Stack с меткой 1808 или более новой.

-  **\@azure-arm-resource**

    Профиль включает последние версии всех служб. Используйте последние версии всех служб в Azure.

См. дополнительные сведения о [профилях API и Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles).

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для Node.js

Чтобы создать профиль клиента, используйте следующие строки. Этот параметр требуется только для Azure Stack и других частных облаков. В глобальном облаке Azure эти параметры уже используются по умолчанию со значением @azure-arm-resource или @azure-arm-storage.

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Следующий код необходим для проверки подлинности субъекта-службы в Azure Stack. Он создает маркер, используя идентификатор клиента и базу аутентификации для Azure Stack.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

Это позволит использовать клиентскую библиотеку профилей API для успешного развертывания приложения в Azure Stack.

В приведенном ниже фрагменте кода используется конечная точка Azure Resource Manager, указанная для экземпляра Azure Stack, и собираются данные, показанные выше, такие как конечная точка коллекции, конечная точка графа, аудитории и конечная точка портала.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Параметры среды

Для аутентификации субъекта-службы в среде Azure Stack используйте следующий код. Используя этот код и настроив переменные среды в командной строке, разработчик обеспечит автоматическое создание этого сопоставления.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры в качестве эталона при создании решений с профилями API для Azure Stack и Node.js. Примеры можно получить на портале GitHub из следующих репозиториев:

- [Приступая к работе с поставщиком ресурсов узла хранилища](https://github.com/sijuman/storage-node-resource-provider-getting-started)
- [Управление вычислительными узлами](https://github.com/sijuman/compute-node-manage-vm)
- [Ресурсы и группы узла Resource Manager](https://github.com/sijuman/resource-manager-node-resources-and-groups)

### <a name="sample-create-storage-account"></a>Пример создания учетной записи хранения 

1.  Клонируйте репозиторий.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  Создайте субъект-службу Azure и назначьте роль для доступа к подписке. Инструкции см. в статье [Использование Azure PowerShell для создания субъекта-службы с сертификатом](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3.  Получите следующие обязательные значения:
    - Tenant ID
    - Идентификатор клиента
    - Секрет клиента
    - идентификатор подписки Azure;
    - конечная точка Resource Manager для Azure Stack.

4.  С помощью командной строки настройте следующие переменные среды, используя данные, полученные от созданного субъекта-службы.

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Resource manager URL>
    ```

    > [!Note]  
    > В Windows используйте **set** вместо **export**.

5.  Откройте файл `index.js` примера приложения.

6.  Для переменной расположения задайте расположение своего экземпляра Azure Stack. Например, `LOCAL = "local"`.

7.  Задайте учетные данные, которые позволят пройти аутентификацию в Azure Stack. Эта часть кода включена в файл index.js в этом примере.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Убедитесь, что заданы правильные клиентские библиотеки, указав сочетание клиентских библиотек, оговоренное выше. В этом примере мы использовали следующее.

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  С помощью [поиска модулей npm](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) найдите профиль **2019-03-01-hybrid** и установите все связанные с ним пакеты поставщиков ресурсов служб вычислений, приложений, хранилища, сети и Key Vault.

    Это можно сделать, открыв командную строку, перейдя в корневую папку репозитория и выполнив команду `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` для каждого используемого поставщика ресурсов.

10.  В командной строке выполните команду `npm install`, чтобы установить все модули Node.js.

11.  Запустите пример.

        ```Node.js  
        node index.js
        ```

12.  Чтобы очистить среду после использования index.js, запустите сценарий очистки.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  Пример успешно завершен. Дополнительные сведения о примере приведены ниже.

### <a name="what-does-indexjs-do"></a>Что делает файл index.js?

Пример создает учетную запись хранения, выводит список учетных записей хранения в подписке или группе ресурсов, выводит ключи учетной записи хранения, повторно создает ключи учетной записи хранения, изменяет свойства учетной записи хранения, обновляет номер SKU учетной записи хранения и проверяет доступность имени учетной записи хранения.

Сначала пример выполняет вход в систему с использованием субъекта-службы и создает объекты **ResourceManagementClient** и **StorageManagementClient** с помощью ваших учетных данных и идентификатора подписки.

Затем пример настраивает группу ресурсов, в которой будет создана учетная запись хранения.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Создание новой учетной записи хранения

После этого пример создает учетную запись хранения, связанную с группой ресурсов, созданной на предыдущем шаге.

В этом случае имя учетной записи хранения создается случайным образом, чтобы гарантировать его уникальность. Однако вызов для создания учетной записи хранения будет выполнен, даже если учетная запись с таким именем уже существует в подписке.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Вывод списка учетных записей хранения в подписке или группе ресурсов

Пример выводит список всех учетных записей хранения в заданной подписке.

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>Чтение и повторное создание ключей учетной записи хранения

Пример выводит ключи для созданной учетной записи хранения и группы ресурсов.

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

Он также повторно создает ключи доступа к этой учетной записи.

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Получение свойств учетной записи хранения

Пример считывает свойства учетной записи хранения.

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>Проверка доступности имени учетной записи хранения

Пример проверяет, доступно ли в Azure заданное имя учетной записи хранения.

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>Удаление учетной записи хранения и группы ресурсов

При запуске сценария cleanup.js удаляется учетная запись хранения, созданная примером.

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Он также удаляет группу ресурсов, созданную примером.

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о профилях API:

- [Manage API version profiles in Azure Stack](azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).
- [Версии API поставщика ресурсов, поддерживаемые профилями в Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)
