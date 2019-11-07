---
title: Использование инструментов передачи данных в хранилище Azure Stack | Документация Майкрософт
description: Узнайте об инструментах передачи данных хранилища Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: e9b474a47c0ab80d34330aff463bcd9d8ada5ab8
ms.sourcegitcommit: 8203490cf3ab8a8e6d39b137c8c31e3baec52298
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73712746"
---
# <a name="use-data-transfer-tools-in-azure-stack-storage"></a>Использование инструментов передачи данных в хранилище Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack предоставляет набор служб хранения для дисков, больших двоичных объектов, таблиц, очередей и функций управления учетными записями. Если необходимо управлять данными в хранилище Azure Stack либо требуется перенести их из этого хранилища или в него, то можно использовать некоторые из инструментов хранилища Azure. В этой статье содержится обзор доступных инструментов.

Выбор оптимальных средств из приведенного ниже списка зависит от ваших требований.

* [AzCopy](#azcopy)

    Программа командной строки хранилища, которую вы можете загрузить для копирования данных из одного объекта в другой в пределах одной учетной записи хранения или между учетными записями хранения.

* [Azure PowerShell](#azure-powershell)

    Оболочка командной строки для выполнения задач и язык сценариев, разработанный специально для администрирования системы.

* [Интерфейс командной строки Azure](#azure-cli)

    Кроссплатформенное средство с открытым кодом, которое предоставляет набор команд для работы с платформами Azure и Azure Stack.

* [Обозреватель службы хранилища Microsoft Azure](#microsoft-azure-storage-explorer)

    Простое в использовании автономное приложение с пользовательским интерфейсом.

* [Blobfuse](#blobfuse)

    Виртуальный драйвер файловой системы для хранилища BLOB-объектов Azure, позволяющий получать доступ к имеющимся данным блочного BLOB-объекта в учетной записи хранения в файловой системе Linux.

Из-за различий между службами хранения в Azure и Azure Stack для каждого инструмента могут действовать некоторые специфические требования, описанные в следующих разделах. Сравнение службы хранилища Azure и хранилища Azure Stack приведено в статье [Хранилище Azure Stack. Отличия и рекомендации](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy — это служебная программа командной строки. Она предназначена для копирования данных из хранилища BLOB-объектов и хранилища таблиц Microsoft Azure и обратно с помощью простых команд, обеспечивающих оптимальную производительность. Кроме того, она позволяет копировать данные из одного объекта в другой в пределах одной учетной записи хранения или между учетными записями хранения.

### <a name="download-and-install-azcopy"></a>Скачивание и установка AzCopy

::: moniker range=">=azs-1811"
* Для обновления 1811 и более поздней версии [скачайте AzCopy 10 или более поздней версии](/azure/storage/common/storage-use-azcopy-v10#download-azcopy).
::: moniker-end

::: moniker range="<azs-1811"
* Для предыдущих версий (1802–1809) [скачайте AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>Настройки и ограничения AzCopy 10.1

Теперь AzCopy 10.1 можно использовать для настройки более ранних версий API. Сюда относится (ограниченная) поддержка Azure Stack.
Чтобы для AzCopy настроить версию API, которая поддерживает Azure Stack, установите для переменной среды `AZCOPY_DEFAULT_SERVICE_API_VERSION` значение `2017-11-09`.

| Операционная система | Команда  |
|--------|-----------|
| **Windows** | В командной строке используйте `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`.<br> В PowerShell используйте `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`.|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

AzCopy 10.1 поддерживает для Azure Stack следующие функции:

| Функция | Поддерживаемые действия |
| --- | --- |
|Управление контейнером|Создание контейнера<br>Список содержимого контейнеров
|Управление заданием|Отображение заданий<br>Возобновление задания
|Удаление больших двоичных объектов|Удаление одного большого двоичного объекта<br>Полное или частичное удаление виртуального каталога
|Отправка файла|Отправка файла<br>Отправка каталога<br>Отправка содержимого каталога
|Загрузка файла|скачать файл;<br>Загрузка каталога<br>Загрузка содержимого каталога
|Синхронизация файла|Синхронизация контейнера с локальной файловой системой<br>Синхронизация локальной файловой системы с контейнером

   > [!NOTE]
   > * Azure Stack не поддерживает предоставление учетных данных авторизации в AzCopy с помощью Azure Active Directory (AD). Доступ к объектам хранилища Azure Stack необходимо осуществлять с помощью маркера подписанного URL-адреса (SAS).
   > * Azure Stack не поддерживает синхронную передачу данных между двумя расположениями больших двоичных объектов Azure Stack, а также между службой хранилища Azure и Azure Stack. Команду azcopy cp невозможно использовать для перемещения данных из Azure Stack в службу хранилища Azure напрямую (или в обратном направлении) с помощью AzCopy 10.1.

### <a name="azcopy-command-examples-for-data-transfer"></a>Примеры команд AzCopy для передачи данных

В следующих примерах описаны стандартные сценарии копирования данных в большие двоичные объекты Azure Stack и обратно. Дополнительные сведения см. в статье [Начало работы с AzCopy](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Скачивание всех больших двоичных объектов на локальный диск

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Отправка одного файла в виртуальный каталог

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>Известные проблемы при использовании AzCopy

 - Операцию AzCopy невозможно выполнить в хранилище файлов, так как оно еще не доступно в Azure Stack.
 - Если требуется перенести данные между двумя расположениями больших двоичных объектов Azure Stack или между Azure Stack и службой хранилища Azure с помощью AzCopy 10.1, то сначала необходимо скачать эти данные в локальное расположение, а затем повторно передать их в целевой каталог в Azure Stack или службе хранилища Azure. Или можно использовать AzCopy 7.1 и указать передачу с помощью параметра **/SyncCopy**, чтобы копировать данные.  
 - Версия AzCopy для Linux поддерживает только обновление 1802 или более поздние версии и не поддерживает службу таблиц.
 - Чтобы копировать данные в службу хранилища таблиц Azure и из нее, [установите AzCopy версии 7.3.0](https://aka.ms/azcopyforazurestack20171109)
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell — это модуль, предоставляющий командлеты для управления службами Azure и Azure Stack. Это оболочка командной строки для выполнения задач и язык сценариев, разработанный специально для администрирования системы.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Установка и настройка PowerShell для использования с Azure Stack

Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack. Дополнительные сведения см. в статьях [Установка PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md) и [Настройка пользовательской среды PowerShell в Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>Пример скрипта PowerShell для Azure Stack 

В этом примере предполагается, что вы успешно [установили PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md). Этот скрипт поможет вам выполнить настройку и запросить учетные данные клиента Azure Stack для добавления учетной записи в локальную среду PowerShell. Затем сценарий настроит подписку Azure по умолчанию, создаст учетную запись хранения в Azure, создаст контейнер в этой новой учетной записи хранения и передаст существующий файл образа (большой двоичный объект) в этот контейнер. После этого сценарий выводит список всех BLOB-объектов в этом контейнере, создает каталог назначения на локальном компьютере и загружает файл образа.

1. Установите [совместимые с Azure Stack модули Azure PowerShell](../operator/azure-stack-powershell-install.md).
2. Скачайте [средства, необходимые для работы с Azure Stack](../operator/azure-stack-powershell-download.md).
3. Откройте **интегрированную среду сценариев Windows PowerShell**, **войдите в нее как администратор** и щелкните **Файл** > **Создать**, чтобы создать файл сценария.
4. Скопируйте приведенный ниже сценарий и вставьте его в новый файл сценария.
5. Обновите переменные сценария на основе заданных параметров конфигурации.
   > [!NOTE]
   > Этот скрипт должен быть запущен в корневом каталоге **AzureStack_Tools**.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Известные проблемы при работе с PowerShell

Текущая совместимая версия модуля Azure PowerShell для Azure Stack — 1.2.11 для пользовательских операций. Она отличается от последней версии Azure PowerShell. Это различие влияет на операции служб хранилища следующим образом.

Формат возвращаемого значения `Get-AzureRmStorageAccountKey` в версии 1.2.11 имеет два свойства: `Key1` и `Key2`. Текущая версия Azure возвращает массив, содержащий все ключи учетной записи.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Дополнительные сведения см. в статье [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Azure CLI — это интерфейс командной строки Azure для управления ресурсами Azure. Его можно установить на macOS, Linux и Windows и запускать из командной строки.

Интерфейс Azure CLI предназначен для администрирования ресурсов Azure из командной строки, а также для создания скриптов автоматизации, которые работают с Azure Resource Manager. Он предоставляет практически те же функции, что и портал Azure Stack, а также различные возможности доступа к данным.

Для работы с Azure Stack требуется Azure CLI версии 2.0 и выше. Дополнительные сведения об установке и настройке Azure CLI для работы с Azure Stack см. в статье [Install and configure CLI for use with Azure Stack](azure-stack-version-profiles-azurecli2.md) (Установка и настройка интерфейса командной строки для использования с Azure Stack). Дополнительные сведения о том, как использовать Azure CLI для выполнения нескольких задач с ресурсами в учетной записи хранения Azure Stack, см. в разделе [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](/azure/storage/storage-azure-cli).

### <a name="azure-cli-sample-script-for-azure-stack"></a>Пример скрипта Azure CLI для Azure Stack

После завершения установки и настройки CLI можно приступать к выполнению следующих шагов взаимодействия с ресурсами хранилища Azure Stack в работе с небольшим образцом сценария оболочки. Этот скрипт выполняет следующие действия:

* Создает контейнер в учетной записи хранения.
* Загружает в контейнер существующий файл (в виде большого двоичного объекта).
* Создает список всех больших двоичных объектов в контейнере.
* Загружает файл в место назначения на указанном локальном компьютере.

Прежде чем выполнить этот сценарий, убедитесь, что подключение к целевой среде Azure Stack и вход в нее выполняются успешно.

1. Откройте текстовый редактор по выбору, а затем скопируйте и вставьте в него предыдущий скрипт.
2. Обновите переменные скрипта в соответствии с параметрами конфигурации.
3. После обновления необходимых переменных сохраните скрипт и выйдите из редактора. В описанных ниже действиях предполагается, что скрипту присвоено имя **my_storage_sample.sh**.
4. При необходимости пометьте скрипт как исполняемый файл: `chmod +x my_storage_sample.sh`
5. Выполните скрипт, например в Bash: `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Обозреватель службы хранилища Microsoft Azure

Обозреватель службы хранилища Microsoft Azure — это изолированное приложение от корпорации Майкрософт. Оно позволяет легко работать с данными из службы хранилища Azure и хранилища Azure Stack на компьютерах Windows, macOS и Linux. Если требуется простой способ управления данными хранилища Azure Stack, рассмотрите возможность использования обозревателя службы хранилища Microsoft Azure.

* Дополнительные сведения о настройке обозревателя службы хранилища Azure для работы с Azure Stack см. в статье [Подключение обозревателя службы хранилища к подписке Azure Stack](azure-stack-storage-connect-se.md).
* Дополнительные сведения об Обозревателе службы хранилища Microsoft Azure см. в статье [Начало работы с Обозревателем службы хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer).

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) — это виртуальный драйвер файловой системы для хранилища BLOB-объектов Azure, позволяющий получать доступ к имеющимся данным блочного BLOB-объекта в учетной записи хранения в файловой системе Linux. Хранилище BLOB-объектов Azure — это служба хранения объектов, не имеющая иерархического пространства имен. Драйвер blobfuse предоставляет это пространство имен, используя схему виртуального каталога с прямой косой чертой (`/`) в качестве разделителя. Blobfuse работает в Azure и Azure Stack. 

Дополнительные сведения о подключении хранилища BLOB-объектов в качестве файловой системы с использованием blobfuse в Linux см. в разделе [Как подключить хранилище BLOB-объектов в качестве файловой системы с использованием blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

При настройке учетных данных учетной записи хранения на этапе подготовки к подключению для Azure Stack необходимо указать *blobEndpoint* в дополнение к accountName, accountKey/sasToken, containerName.

В Пакете средств разработки Azure Stack для *blobEndpoint* должно быть задано значение `myaccount.blob.local.azurestack.external`. Если вы не знаете конечную точку для интегрированной системы Azure Stack, обратитесь к администратору облака.

Значения *accountKey* и *sasToken* можно настраивать только поочередно. Если указан ключ учетной записи хранения, файл конфигурации учетных данных имеет следующий формат.

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Если указан общий маркер доступа, файл конфигурации учетных данных имеет следующий формат.

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Дополнительная информация

* [Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения](azure-stack-storage-connect-se.md)
* [Начало работы с Обозревателем службы хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Хранилище Azure Stack. Отличия и рекомендации](azure-stack-acs-differences.md)
* [Общие сведения о службе хранилища Microsoft Azure](/azure/storage/common/storage-introduction)
