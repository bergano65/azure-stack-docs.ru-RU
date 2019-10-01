---
title: Настройка автоматического сбора журналов Azure Stack | Документация Майкрософт
description: Как настроить автоматической сбор журналов в разделе "Справка и поддержка" в Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 4d6bc431b292fc7a124aa2b8051d0a927d736eee
ms.sourcegitcommit: 4e48f1e5af74712a104eda97757dc5f50a591936
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224954"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>Настройка автоматического сбора журналов диагностики Azure Stack

*Область применения: интегрированные системы Azure Stack*

Рекомендуется настроить функцию автоматического сбора журналов диагностики, чтобы оптимизировать сбор журналов и поддержку пользователей. Если необходимо исследовать состояния работоспособности системы, журналы могут автоматически передаваться для анализа службой поддержки пользователей Майкрософт (CSS). 

## <a name="create-an-azure-blob-container-sas-url"></a>Создание подписанного URL-адреса контейнера больших двоичных объектов Azure 

Прежде чем можно будет настроить автоматический сбор журналов, необходимо получить подписанный URL-адрес (SAS) для контейнера больших двоичных объектов. С помощью SAS можно предоставить доступ к ресурсам в учетной записи хранения, не предоставляя ее ключей. Можно сохранить файлы журнала Azure Stack в контейнере больших двоичных объектов в Azure, а затем указать подписанный URL-адрес, по которому CSS сможет собирать журналы. 

### <a name="prerequisites"></a>Предварительные требования

Вы можете использовать новый или имеющийся контейнер больших двоичных объектов в Azure. Чтобы создать контейнер больших двоичных объектов в Azure, требуется по крайней мере роль [Участник для данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) или [определенное разрешение](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). У глобальных администраторов также есть необходимое разрешение. 

Рекомендации по выбору параметров учетной записи хранения для автоматической сбора журналов см. в разделе [Рекомендации по автоматическому сбору журналов Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Дополнительные сведения о типах учетных записей хранения см. в разделе [Общие сведения об учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Создание учетной записи хранения больших двоичных объектов
 
1. Войдите на [портале Azure](https://portal.azure.com).
1. Щелкните **Учетные записи хранения** > **Добавить**. 
1. Создайте контейнер больших двоичных объектов со следующими параметрами.
   - **Подписка**: Выберите подписку Azure.
   - **Группа ресурсов.** Укажите группу ресурсов.
   - **Имя учетной записи хранения**. Укажите уникальное имя учетной записи хранения.
   - **Расположение.** Выберите центр обработки данных в соответствии с политикой своей компании.
   - **Производительность** — Выберите ценовую категорию "Стандартный".
   - **Тип учетной записи**. Выберите "StorageV2" (общего назначения версии 2). 
   - **Репликация.** Выберите локально избыточное хранилище (LRS).
   - **Уровень доступа**. Выберите "Холодный".

   ![Снимок экрана со свойствами контейнера больших двоичных объектов](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Щелкните **Просмотр и создание**, а затем — **Создать**.  

### <a name="create-a-blob-container"></a>Создание контейнера BLOB-объектов 

1. После успешного развертывания щелкните **Перейти к ресурсу**. Можно также закрепить учетную запись хранения на панели мониторинга для удобного доступа. 
1. Щелкните **Обозреватель службы хранилища (предварительная версия)** , щелкните правой кнопкой мыши **Контейнеры больших двоичных объектов** и выберите **Создать контейнер BLOB-объектов**. 
1. Введите имя нового контейнера и щелкните **ОК**.

## <a name="create-a-sas-url"></a>Создание подписанного URL-адреса

1. Щелкните правой кнопкой мыши контейнер и выберите **Получить подписанный URL-адрес**. 
   
   ![Снимок экрана, показывающий, как получить подписанный URL-адрес контейнера больших двоичных объектов](media/azure-stack-automatic-log-collection/get-sas.png)

1. Выберите следующие свойства.
   - "Время начала". При необходимости можно сместить время начала. 
   - "Время окончания срока действия". Укажите два года.
   - "Часовой пояс". Формат UTC.
   - Разрешения. Чтение, запись и список

   ![Снимок экрана, показывающий свойства подписанного URL-адреса](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Нажмите кнопку **Создать**.  

Скопируйте этот URL-адрес и введите его при [настройке автоматического сбора журналов](azure-stack-configure-automatic-diagnostic-log-collection.md). Дополнительные сведения о подписанных URL-адресах см. в разделе [Использование подписанных URL-адресов (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Настройка автоматического сбора журналов

Чтобы добавить подписанный URL-адрес в пользовательский интерфейс сбора журналов, выполните следующие действия. 

1. Войдите на портал администратора Azure Stack.
1. Откройте **Help and support Overview** (Обзор справки и поддержки).
1. Щелкните **Automatic collection settings** (Параметры автоматического сбора).

   ![Снимок экрана, на котором показано, где в разделе "Справка и поддержка" можно включить сбор журналов](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Задайте для параметра "Automatic log collection" (Автоматический сбор журналов) значение **Включено**.
1. Введите подписанный URL-адрес (SAS) контейнера больших двоичных объектов в учетной записи хранения.

   ![Снимок экрана, показывающий подписанный URL-адрес контейнера больших двоичных объектов](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Автоматический сбор журналов можно отключить и снова включить в любое время. Конфигурация подписанного URL-адреса не изменится. Если автоматический сбор журналов включается повторно, ранее указанный подписанный URL-адрес будет проходить те же проверки и подписанный URL-адрес с истекшим сроком действия будет отклонен. 

## <a name="view-log-collection"></a>Просмотр коллекции журналов

Журнал сбора журналов из Azure Stack отображается на странице **Log collection** (Сбор журналов) в разделе "Справка и поддержка" со следующими значениями даты и времени.

- **Collection time** (Время сбора). Время начала операции сбора журналов. 
- **From Date** (Дата начала). Начало периода времени сбора журналов.
- **To Date** (Дата окончания). Конец выбранного периода времени.

![Снимок экрана, показывающий коллекции журналов](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Если сбор журналов диагностики завершается сбоем, убедитесь, что подписанный URL-адрес является допустимым. Если ошибка повторяется или происходит несколько сбоев, обратитесь за помощью в службу поддержки пользователей Майкрософт (CSS). 

Операторы также могут проверить учетную запись хранения на наличие автоматически собранных журналов. Например, на этом снимке экрана показаны коллекции журналов с помощью предварительной версии Обозревателя службы хранилища на портале Azure.

![Снимок экрана, показывающий коллекции журналов](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>Оповещения об автоматическом сборе журналов диагностики 

Если этот режим включен, автоматической сбор журналов диагностики выполняется только при необходимости. Только следующая коллекция триггеров оповещений. 

|Заголовок оповещения  | FaultIdType|    
|-------------|------------|
|"Unable to connect to the remote service" (Не удается подключиться к удаленной службе) |  UsageBridge.NetworkError|
|Сбой обновления |    Urp.UpdateFailure   |          
|Storage Resource Provider infrastructure/dependencies not available (Инфраструктура или зависимости поставщика ресурсов хранилища недоступны) |  StorageResourceProviderDependencyUnavailable     |     
|Node not connected to network controller (Узел не подключен к сетевому контроллеру);|  ServerHostNotConnectedToController   |     
|"Route publication failure" (Сбой публикации маршрута) |    SlbMuxRoutePublicationFailure | 
|Storage Resource Provider internal data store unavailable (Внутреннее хранилище данных поставщика ресурсов хранилища недоступно) |    StorageResourceProvider. DataStoreConnectionFail     |       
|"Storage device failure" (Отказ устройства хранения) | Microsoft.Health.FaultType.VirtualDisks.Detached   |      
|"Health controller cannot access storage account" (Контроллеру работоспособности не удалось получить доступ к учетной записи хранения) | Microsoft.Health.FaultType.StorageError |    
|Connectivity to a physical disk has been lost (Связь с физическим диском потеряна) |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication    |    
|The blob service isn't running on a node (Служба BLOB-объектов не работает на узле) | StorageService.The.blob.service.is.not.running.on.a.node-Critical | 
|Роль инфраструктуры неработоспособна. |    Microsoft.Health.FaultType.GenericExceptionFault |        
|"Table service errors" (Ошибки службы таблиц) | StorageService.Table.service.errors-Critical |              
|A file share is over 80% utilized (Общая папка используется более чем на 80 %) |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|"Scale unit node is offline" (Узел единицы масштабирования отключен). | FRP.Heartbeat.PhysicalNode |  
|Infrastructure role instance unavailable (Экземпляр роли инфраструктуры недоступен) | FRP.Heartbeat.InfraVM   |    
|Infrastructure role instance unavailable (Экземпляр роли инфраструктуры недоступен)  |    FRP.Heartbeat.NonHaVm     |        
|Роль инфраструктуры (управление каталогами) сообщила об ошибках синхронизации времени |  DirectoryServiceTimeSynchronizationError |     
|ожидается истечение срока действия внешнего сертификата. |  CertificateExpiration.ExternalCert.Warning |
|ожидается истечение срока действия внешнего сертификата. |  CertificateExpiration.ExternalCert.Critical |
|"Unable to provision virtual machines for specific class and size due to low memory capacity" (Не удается подготовить виртуальные машины для определенного класса и размера из-за недостаточного объема памяти) |  AzureStack.ComputeController.VmCreationFailure.LowMemory |
|"Node inaccessible for virtual machine placement" (Узел недоступен для замены виртуальной машины); |  AzureStack.ComputeController.HostUnresponsive | 
|Сбой резервного копирования  | AzureStack.BackupController.BackupFailedGeneralFault |    
|The scheduled backup was skipped due to a conflict with failed operations (Запланированное резервное копирование пропущено из-за конфликта с неудачными операциями)  | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>См. также

[Обработка журнала Azure Stack и данных клиента](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Использование подписанных URL-адресов (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Рекомендации по автоматическому сбору журналов Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





