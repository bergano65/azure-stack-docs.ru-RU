---
title: Выполнение резервного копирования учетных записей хранения в Azure Stack Hub | Документация Майкрософт
description: Узнайте как выполнять резервное копирование учетных записей хранения в Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 0067a490df899d696524f661e30dfcb2fd0ed662
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536544"
---
# <a name="back-up-your-storage-accounts-on-azure-stack-hub"></a>Резервное копирование учетных записей хранения в Azure Stack Hub

В этой статье рассматривается защита и восстановление учетных записей хранения в учетных записях службы хранилища Azure в Azure Stack Hub.

## <a name="elements-of-the-solution"></a>Элементы решения

В этом разделе рассматривается общая структура решения и основные части.

![Резервное копирование службы хранилища Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Уровень приложения

Данные можно реплицировать между учетными записями хранения в отдельных единицах масштабирования Azure Stack Hub, выполняя несколько операций [Put Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob) или [Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block) для записи объектов в несколько расположений. Кроме того, приложение может выдать операцию [Копирование больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/copy-blob), чтобы скопировать большой двоичный объект в учетную запись хранения, размещенную в отдельной единице масштабирования после завершения операции размещения в первичной учетной записи.

### <a name="scheduled-copy-task"></a>Задача копирования по расписанию

AzCopy — это отличное средство, которое можно использовать для копирования данных из локальных файловых систем, облачного хранилища Azure, хранилища Azure Stack Hub и S3. В настоящее время AzCopy не может копировать данные между двумя учетными записями службы хранилища Azure Stack Hub. Для копирования объектов из исходной учетной записи хранения Azure Stack Hub в целевую учетную запись хранения Azure Stack Hub требуется промежуточная локальная файловая система.

Дополнительные сведения см. в разделе AzCopy в статье [Использование инструментов передачи данных в хранилище Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy).

### <a name="azure-stack-hub-source"></a>Azure Stack Hub (источник)

Это источник данных учетной записи хранения для резервного копирования.

Вам потребуется URL-адрес исходной учетной записи хранения и маркер SAS. Инструкции по работе с учетной записью хранения см. в статье [Начало работы со средствами разработки хранилища Azure Stack Hub](azure-stack-storage-dev.md).

### <a name="azure-stack-hub-target"></a>Azure Stack Hub (целевой объект)

Это целевой объект, в котором будут храниться данные учетной записи для резервного копирования. Целевой экземпляр Azure Stack Hub должен находиться в расположении, отличном от расположения целевого объекта Azure Stack Hub. А источник должен иметь возможность подключения к целевому объекту.

Вам потребуется URL-адрес исходной учетной записи хранения и маркер SAS. Инструкции по работе с учетной записью хранения см. в статье [Начало работы со средствами разработки хранилища Azure Stack Hub](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Промежуточная локальная файловая система

Вам потребуется место для запуска AzCopy и хранения данных при копировании из источника и последующей записи в целевой Azure Stack Hub. Это промежуточный сервер в исходном Azure Stack Hub.

В качестве промежуточного сервера можно создать сервер Linux или Windows. Серверу потребуется достаточно места для хранения всех объектов в контейнерах исходных учетных записей хранения.
- Инструкции по настройке сервера Linux см. в статье [Краткое руководство. Создание виртуальной машины с сервером Linux с помощью портала Azure Stack Hub](azure-stack-quick-linux-portal.md).  
- Инструкции по настройке сервера Windows см. в статье [Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack Hub](azure-stack-quick-windows-portal.md).  

После настройки сервера Windows следует установить [Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) и [средства Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Настройка резервного копирования для учетных записей хранения

1. Получите конечную точку BLOB-объекта для исходных и целевых учетных записей хранения.

    ![Резервное копирование службы хранилища Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Создание и запись маркеров SAS для исходной и целевой учетных записей хранения.

    ![Резервное копирование службы хранилища Azure Stack Hub](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Установите [AzCopy](https://github.com/Azure/azure-storage-azcopy) на промежуточном сервере и задайте версию API для учетных записей службы хранилища Azure Stack Hub.

    - Для Windows Server:

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Для сервера Linux (Ubuntu):

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. Создайте сценарий на промежуточном сервере. Обновите эту команду, указав **учетную запись хранения**, **ключ SAS**и **путь к локальному каталогу**. Сценарий будет запущен для добавочного копирования данных из **исходной** учетной записи хранения.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Введите **учетную запись хранения**, **ключ SAS** и **путь к локальному каталогу.  Он будет использоваться для добавочного копирования данных в **целевую** учетную запись хранения
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  Используйте Cron или планировщик задач Windows, чтобы запланировать копирование из исходной учетной записи хранения Azure Stack Hub в локальное хранилище на промежуточном сервере. Затем скопируйте из локального хранилища на промежуточном сервере в целевую учетную запись хранения Azure Stack Hub.

    Целевая точка восстановления, которую можно достичь с помощью этого решения, определяется значением параметра /MO и пропускной способностью сети между исходной учетной записью и промежуточным сервером, а также промежуточным сервером и целевой учетной записью.

    - Для сервера Linux (Ubuntu):

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Параметр | Примечание | 
    | ---- | ---- |
    | /SC | Используйте поминутное расписание. |
    | /MO | Интервал *XX* минут. |
    | /TN | Имя задачи. |
    | /TR | Путь к файлу `script.bat`. |


    - Для Windows Server:

    Сведения об использовании планировщика задач Windows см. в разделе [Task Scheduler for developers](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page) (Планировщик задач для разработчиков)
    

## <a name="use-your-storage-account-in-a-disaster"></a>Использование учетной записи хранения в случае сбоя

Каждая учетная запись службы хранилища Azure Stack Hub имеет уникальное DNS-имя, производное от имени самого региона Azure Stack Hub, например `https://krsource.blob.east.asicdc.com/`. Приложения, предназначенные для записи и чтения данных из этого DNS-имени, должны учитывать изменение DNS-имени учетной записи хранения, если целевая учетная запись, например `https://krtarget.blob.west.asicdc.com/`, должна использоваться в случае сбоя.

Строки подключения приложения можно изменить после объявления сбоя, чтобы учитывать перемещение объектов или, если запись CNAME используется перед балансировщиком нагрузки на переднем конце исходной и целевой учетных записей хранения, балансировщик нагрузки можно настроить с помощью алгоритма отработки отказа вручную, который позволяет администратору объявить целевой объект

Если приложение использует SAS, а не AAD или AD FS, описанный выше метод не будет работать, и строки подключения приложения потребуется обновить с помощью URL-адреса целевой учетной записи хранения и ключей SAS, созданных для целевой учетной записи хранения.

## <a name="next-steps"></a>Дальнейшие действия

[Начало работы со средствами разработки хранилища Azure Stack Hub](azure-stack-storage-dev.md)