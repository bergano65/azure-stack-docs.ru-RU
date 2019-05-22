---
title: Начало работы со средствами разработки хранилища Azure Stack | Документация Майкрософт
description: Руководство по началу работы со средствами разработки хранилища Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 02/27/2019
ms.topic: conceptual
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 28fc182404107b100c9b69ecd73d961d83bd3039
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985621"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Начало работы со средствами разработки хранилища Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Microsoft Azure Stack предоставляет набор служб хранилища, включая хранилище BLOB-объектов, таблиц и очередей.

Эту статью можно использовать как руководство по началу работы со средствами разработки хранилища Azure Stack. Более подробные сведения и пример кода можно найти в соответствующих руководствах по службе хранилища Azure.

> [!NOTE]  
> Существуют различия между хранилищем Azure Stack и службой хранилища Azure, включая некоторые особые требования для каждой платформы. Например, для Azure Stack существуют определенные клиентские библиотеки и определенные требования к суффиксу конечной точки. Дополнительные сведения см. на странице [цен на службу хранилища Azure, раздел "Отличия и рекомендации"](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Клиентские библиотеки Azure

Для клиентских библиотек хранилища вам необходимо знать версию, совместимую с REST API. Необходимо также указать конечную точку Azure Stack в коде.

### <a name="1811-update-or-newer-versions"></a>Обновление 1811 или более новые версии

| Клиентская библиотека | Поддерживаемая версия Azure Stack | Ссылка | Спецификация конечной точки |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Пакет NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0 | Файл app.config |
| Java | 7.0.0 | Пакет Maven<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0 | Настройка строки подключения |
| Node.js | 2.8.3 | Ссылка на NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Запуск: `npm install azure-storage@2.8.3`)<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3 | Объявление экземпляра службы |
| C++ | 5.2.0 | Пакет NuGet:<br>https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0 | Настройка строки подключения |
| PHP | 1.2.0 | Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common<br>Большой двоичный объект: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob<br>Очередь:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue<br>Таблица: https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table<br> <br>Установка через компоновщик (дополнительные сведения см. [ниже](#install-php-client-via-composer---current)) | Настройка строки подключения |
| Python | 1.1.0 | Выпуск GitHub:<br>Общая версия:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common<br>Большой двоичный объект:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob<br>Очередь:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue | Объявление экземпляра службы |
| Ruby | 1.0.1 | Пакет RubyGems:<br>Общая версия:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Большой двоичный объект: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Очередь: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Таблица: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Большой двоичный объект: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Очередь: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Таблица: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Настройка строки подключения |

#### <a name="install-php-client-via-composer---current"></a>Установка клиента PHP через компоновщик — текущая версия

Для установки через компоновщик сделайте следующее (возьмите для примера большой двоичный объект).

1. Создайте файл с именем **composer.json** в корневом каталоге проекта со следующим кодом:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Скачайте файл [composer.phar](https://getcomposer.org/composer.phar) в корневой каталог проекта.
3. Выполните команду `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Предыдущие версии (обновления 1802–1809)

| Клиентская библиотека | Поддерживаемая версия Azure Stack | Ссылка | Спецификация конечной точки |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Пакет NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | Файл app.config |
| Java | 6.1.0 | Пакет Maven<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Настройка строки подключения |
| Node.js | 2.7.0 | Ссылка на NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Запуск: `npm install azure-storage@2.7.0`)<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Объявление экземпляра службы |
| C++ | 3.1.0 | Пакет NuGet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Выпуск GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Настройка строки подключения |
| PHP | 1.0.0 | Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Большой двоичный объект: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Очередь:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Таблица: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Установка через компоновщик (дополнительные сведения см. ниже) | Настройка строки подключения |
| Python | 1.0.0 | Выпуск GitHub:<br>Общая версия:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Большой двоичный объект:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Очередь:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Объявление экземпляра службы |
| Ruby | 1.0.1 | Пакет RubyGems:<br>Общая версия:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Большой двоичный объект: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Очередь: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Таблица: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Выпуск GitHub:<br>Общая версия: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Большой двоичный объект: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Очередь: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Таблица: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Настройка строки подключения |

#### <a name="install-php-client-via-composer---previous"></a>Установка клиента PHP через компоновщик — предыдущая версия

Для установки через компоновщик сделайте следующее (возьмите для примера большой двоичный объект).

1. Создайте файл с именем **composer.json** в корневом каталоге проекта со следующим кодом:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Скачайте файл [composer.phar](https://getcomposer.org/composer.phar) в корневой каталог проекта.
3. Выполните команду `php composer.phar install`.

## <a name="endpoint-declaration"></a>Объявление конечной точки

Конечная точка Azure Stack содержит две части: имя региона и домен Azure Stack.
В Пакете средств разработки Azure Stack конечной точкой по умолчанию является **local.azurestack.external**.
Если у вас нет сведений о своей конечной точке, обратитесь к администратору облака.

## <a name="examples"></a>Примеры

### <a name="net"></a>.NET

Для Azure Stack суффикс конечной точки указан в файле app.config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Для Azure Stack суффикс конечной точки указан в экземпляре объявления:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Для Azure Stack суффикс конечной точки указан в экземпляре объявления:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Хранилище BLOB-объектов

Следующие руководства по хранилищу BLOB-объектов Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [Использование хранилища BLOB-объектов из Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Использование хранилища больших двоичных объектов из Node.js](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [Использование хранилища BLOB-объектов из C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [Использование хранилища BLOB-объектов из PHP](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Использование хранилища BLOB-объектов Azure из Python](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [Использование хранилища BLOB-объектов из Ruby](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Хранилище очередей

Следующие руководства по хранилищу очередей Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем очередей Azure с помощью .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [Использование хранилища очередей из Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Использование хранилища очередей из Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [Использование хранилища очередей из C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [Использование хранилища очередей из PHP](/azure/storage/queues/storage-php-how-to-use-queues)
* [Использование хранилища очередей из Python](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [Использование хранилища очередей из Ruby](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Хранилище таблиц

Следующие руководства по хранилищу таблиц Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем таблиц Azure с помощью .NET](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [Использование табличного хранилища из Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Использование табличного хранилища Azure из Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [Использование табличного хранилища из C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [Использование табличного хранилища из PHP](/azure/cosmos-db/table-storage-how-to-use-php)
* [Как использовать хранилище таблиц в Python](/azure/cosmos-db/table-storage-how-to-use-python)
* [Использование табличного хранилища из Ruby](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Дополнительная информация

* [Общие сведения о службе хранилища Microsoft Azure](/azure/storage/common/storage-introduction)
