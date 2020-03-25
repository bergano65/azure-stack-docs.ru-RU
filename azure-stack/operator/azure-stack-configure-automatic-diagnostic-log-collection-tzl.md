---
title: Упреждающий сбор журналов диагностики при создании оповещений в Azure Stack Hub
description: Сведения о том, как настроить упреждающий сбор журналов в разделе "Справка и поддержка" в Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520545"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Упреждающая отправка журналов диагностики Azure Stack Hub

Вы можете тратить меньше времени на общение со службой поддержки, настроив упреждающий сбор журналов диагностики при создании оповещений в Azure Stack Hub.
Если необходимо исследовать состояния работоспособности системы, журналы могут автоматически передаваться до того, как вы обратитесь в службы поддержки пользователей Майкрософт. 

## <a name="steps-to-configure-proactive-log-collection"></a>Процедура настройки упреждающего сбора журналов

Выполните эти шаги, чтобы настроить упреждающий сбор журналов Автоматический сбор журналов можно отключить и снова включить в любое время.  

1. Войдите на портал администратора Azure Stack Hub.
1. Откройте **Help and support Overview** (Обзор справки и поддержки).
1. Если появится баннер, щелкните **Enable proactive log collection** (Включить упреждающий сбор журналов). 

   ![Снимок экрана, на котором показано, где в разделе "Справка и поддержка" можно включить сбор журналов](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   Также можно щелкнуть **Параметры** и задать для параметра **Proactive log collection** (Упреждающий сбор журналов) значение **Включить**, затем щелкнуть **Сохранить**.

   ![Снимок экрана, на котором показано, где в разделе "Справка и поддержка" можно включить сбор журналов](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Просмотр коллекции журналов

Журнал сбора журналов из Azure Stack Hub отображается на странице **Log collection** (Сбор журналов) в разделе "Справка и поддержка" со следующими значениями даты и времени.

- **Time Collected** (Время сбора): Время начала операции сбора журналов.
- **Состояние**: "Выполняется" или "Завершено".
- **Logs start** (Начало сбора журналов): Начало периода времени сбора журналов.
- **Logs end** (Конец сбора журналов): Конец выбранного периода времени.
- **Тип**. Тип сбора журналов: manual (вручную) или proactive (упреждающий). 


![Снимок экрана, показывающий коллекции журналов](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Оповещения об упреждающем сборе журналов диагностики 

Если функция упреждающего сбора журналов включена, журналы отправляются только при возникновении одного из следующих событий. 

Например, **Сбой обновления** — это оповещение, активирующее упреждающий сбор журналов диагностики. Если такая функция включена, при сбое обновления будут автоматически собраны журналы диагностики, чтобы помочь специалистам службы поддержки устранить проблему. Журналы диагностики собираются только при возникновении оповещения **Сбой обновления**. 

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


## <a name="see-also"></a>См. также раздел

[Обработка журнала Azure Stack Hub и данных клиента](azure-stack-data-collection.md)





