---
title: Выполнение резервного копирования учетных записей хранения в Azure Stack | Документация Майкрософт
description: Узнайте как выполнять резервное копирование учетных записей хранения в Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 3f6ed5604bd2d32d9d030ceb4b5f67567362cc34
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145838"
---
# <a name="back-up-your-storage-accounts-on-azure-stack"></a>Резервное копирование учетных записей хранения в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье рассматривается защита и восстановление учетных записей хранения в учетных записях Службы хранилища Azure в Azure Stack.

## <a name="elements-of-the-solution"></a>Элементы решения

В этом разделе рассматривается общая структура решения и основные части.

![Резервное копирование Службы хранилища Azure Stack](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Уровень приложения

Данные можно реплицировать между учетными записями хранения в отдельных единицах масштабирования Azure Stack, выполняя несколько операций [Размещение больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/put-blob) или [Размещение блоков](https://docs.microsoft.com/rest/api/storageservices/put-block) для записи объектов в несколько расположений. Кроме того, приложение может выдать операцию [Копирование больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/copy-blob), чтобы скопировать большой двоичный объект в учетную запись хранения, размещенную в отдельной единице масштабирования после завершения операции размещения в первичной учетной записи.

### <a name="scheduled-copy-task"></a>Задача копирования по расписанию

AzCopy — это отличное средство, которое можно использовать для копирования данных из локальных файловых систем, облачного хранилища Azure, хранилища Azure Stack и s3. В настоящее время AzCopy не может копировать данные между двумя учетными записями Службы хранилища Azure Stack. Для копирования объектов из исходной учетной записи хранения Azure Stack в целевую учетную запись хранения Azure Stack требуется промежуточная локальная файловая система.

Дополнительные сведения см. в разделе AzCopy в статье [Использование инструментов передачи данных в хранилище Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy).

### <a name="azure-stack-source"></a>Azure Stack (источник)

Это источник данных учетной записи хранения для резервного копирования.

Вам потребуется URL-адрес исходной учетной записи хранения и маркер SAS. Инструкции по работе с учетной записью хранения см. в разделе [Начало работы с средствами разработки хранилища Azure Stack](azure-stack-storage-dev.md).

### <a name="azure-stack-target"></a>Azure Stack (целевой объект)

Это целевой объект, в котором будут храниться данные учетной записи для резервного копирования. Целевой экземпляр Azure Stack должен находиться в расположении, отличном от расположения целевого объекта Azure Stack. А источник должен иметь возможность подключения к целевому объекту.

Вам потребуется URL-адрес исходной учетной записи хранения и маркер SAS. Инструкции по работе с учетной записью хранения см. в разделе [Начало работы с средствами разработки хранилища Azure Stack](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Промежуточная локальная файловая система

Вам потребуется место для запуска AzCopy и хранения данных при копировании из источника и последующей записи в целевой Azure Stack. Это промежуточный сервер в исходном Azure Stack.

В качестве промежуточного сервера можно создать сервер Linux или Windows. Серверу потребуется достаточно места для хранения всех объектов в контейнерах исходных учетных записей хранения.
- Инструкции по настройке сервера Linux см. в разделе [Краткое руководство. Создание виртуальной машины с сервером Linux с помощью портала Azure Stack](azure-stack-quick-linux-portal.md).  
- Инструкции по настройке сервера Windows см. в разделе [Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack](azure-stack-quick-windows-portal.md).  

После настройки сервера Windows следует установить [Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) и [Azure Stack Tools](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Настройка резервного копирования для учетных записей хранения

1. Получите конечную точку BLOB-объекта для исходных и целевых учетных записей хранения.

    ![Резервное копирование Службы хранилища Azure Stack](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Создание и запись маркеров SAS для исходной и целевой учетных записей хранения.

    ![Резервное копирование Службы хранилища Azure Stack](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Установите [AzCopy](https://github.com/Azure/azure-storage-azcopy) на промежуточном сервере и задайте версию API для учетных записей Службы хранилища Azure Stack.

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

6.  Используйте Cron или планировщик задач Windows, чтобы запланировать копирование из исходной учетной записи хранения Azure Stack в локальное хранилище на промежуточном сервере. Затем скопируйте из локального хранилища на промежуточном сервере в целевую учетную запись хранения Azure Stack.

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

Каждая учетная запись Службы хранилища Azure Stack имеет уникальное DNS-имя, производное от имени самого региона Azure Stack, например `https://krsource.blob.east.asicdc.com/`. Приложения, предназначенные для записи и чтения данных из этого DNS-имени, должны учитывать изменение DNS-имени учетной записи хранения, если целевая учетная запись, например `https://krtarget.blob.west.asicdc.com/`, должна использоваться в случае сбоя.

Строки подключения приложения можно изменить после объявления сбоя, чтобы учитывать перемещение объектов или, если запись CNAME используется перед балансировщиком нагрузки на переднем конце исходной и целевой учетных записей хранения, балансировщик нагрузки можно настроить с помощью алгоритма отработки отказа вручную, который позволяет администратору объявить целевой объект

Если приложение использует SAS, а не AAD или AD FS, описанный выше метод не будет работать, и строки подключения приложения потребуется обновить с помощью URL-адреса целевой учетной записи хранения и ключей SAS, созданных для целевой учетной записи хранения.

## <a name="next-steps"></a>Дополнительная информация

[Начало работы со средствами разработки хранилища Azure Stack](azure-stack-storage-dev.md)