---
title: Заметки о выпуске обновления 5 для Службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте об улучшениях, исправлениях и известных проблемах в обновлении 5 для Службы приложений в Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 4adad49b27b1ab1d255ccc566c95b003cfd09b3b
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974917"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Заметки о выпуске обновления 5 для Службы приложений Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описаны улучшения, исправления и известные проблемы в обновлении 5 для Службы приложений Azure в Azure Stack. Известные проблемы можно разделить на три категории: проблемы, которые непосредственно относятся к процессу развертывания, проблемы с обновлением и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Службу приложений Azure 1.5, примените обновление 1901 к интегрированной системе Azure Stack или разверните Пакет средств разработки Azure Stack последней версии.

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 5 для Службы приложений Azure в Azure Stack — **80.0.2.15**.

### <a name="prerequisites"></a>Предварительные требования

Ознакомьтесь с [предварительными условиями для развертывания Службы приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md) перед началом развертывания.

Прежде чем начать обновление Службы приложений Azure в Azure Stack до версии 1.5:

- Убедитесь, что все роли настроены в разделе администрирования Службы приложений Azure на портале администратора Azure Stack.

- Выполните резервное копирование Службы приложений и баз данных master:
  - AppService_Hosting;
  - AppService_Metering;
  - master.

- Выполните резервное копирование общей папки с содержимым приложения клиента.

- Выполните синдикацию **расширения пользовательских сценариев** версии **1.9.1** в Azure Marketplace.

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 5 Службы приложений Azure в Azure Stack включает в себя следующие улучшения и исправления:

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Согласованы с версией пакета SDK для портала Azure Stack.

- Обновления **среды выполнения Функций Azure** до версии **v1.0.12205**.

- Обновления **средств Kudu**, что позволило разрешить проблемы со стилями и функциональными возможностями, которые возникали у пользователей **с отключенной** инфраструктурой Azure Stack. 

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих платформ приложений и инструментов**:
  - Добавлено ASP.NET Core версий 2.1.6 и 2.2.0.
  - Добавлено NodeJS 10.14.1.
  - Добавлено NPM 6.4.1.
  - Kudu обновлено до версии 79.20129.3767.
  
- **Обновления базовой операционной системы всех ролей**.
  - [Накопительное обновление 64-разрядных систем Windows Server 2016 от февраля 2019 г. (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Действия, выполняемые после развертывания

> [!IMPORTANT]  
> Если вы указали поставщик ресурсов Службы приложений с помощью экземпляра SQL Always On, в группу доступности *необходимо* [добавить базы данных appservice_hosting и appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), а затем синхронизировать эти базы данных, чтобы предотвратить остановку службы в случае отработки отказа.

### <a name="post-update-steps"></a>Действия после обновления

Клиенты, желающие перейти на автономную базу данных существующей Службы приложений Azure в развернутой инфраструктуре Azure Stack, должны выполнить приведенные ниже действия после завершения обновления Службы приложений Azure в Azure Stack 1.5.

> [!IMPORTANT]
> Процедура миграции занимает примерно 5–10 минут. Эта процедура включает в себя завершение существующих сеансов входа в базу данных. Спланируйте время простоя, чтобы перенести и проверить работу Службы приложений Azure на основе Azure Stack после миграции. Если вы выполнили эти действия после обновления Службы приложений Azure в Azure Stack 1.3, повторять их не нужно.

1. Добавьте [базы данных Службы приложений (appservice_hosting и appservice_metering) в группу доступности](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Включите автономную базу данных.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Преобразование базы данных в частично автономную вызовет простой, так как для этого необходимо завершить все активные сеансы.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate logins to contained database users.

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

**Проверка**

1. Проверьте, включен ли для SQL Server автономный режим.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Проверьте текущий автономный режим работы.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Рабочим ролям не удается связаться с файловым сервером, если служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети. Это проблема описана в документации по развертыванию Службы приложений Azure в Azure Stack.

Если вы решили выполнить развертывание в существующей виртуальной сети с использованием внутреннего IP-адреса для подключения к файловому серверу, необходимо добавить правило безопасности для исходящего трафика, разрешающее передачу трафика SMB между подсетью рабочей роли и файловым сервером. Для этого перейдите к группе безопасности сети WorkersNsg на портале администрирования и добавьте правило безопасности для исходящего трафика со следующими свойствами.

 * Источник: Любой
 * Диапазон исходных портов: *.
 * Назначение: IP-адреса
 * Диапазон конечных IP-адресов: диапазон IP-адресов для файлового сервера
 * Диапазон конечных портов: 445
 * Протокол: TCP
 * Действие: РАЗРЕШИТЬ
 * Приоритет: 700
 * Имя: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы, с которыми сталкиваются облачные администраторы, работающие со службой приложений Azure в Azure Stack

См. документацию в [заметках о выпуске для Azure Stack 1809](azure-stack-update-1903.md).

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- См. сведения о том, как [подготовиться к развертыванию Службы приложений Azure в Azure Stack](azure-stack-app-service-before-you-get-started.md).
