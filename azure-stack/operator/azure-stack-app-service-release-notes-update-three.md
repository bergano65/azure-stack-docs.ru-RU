---
title: Заметки о выпуске обновления 3 для Службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте об улучшениях, исправлениях и известных проблемах в обновлении 3 для Службы приложений в Azure Stack.
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
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: d226be9bad3bd6ddf775d8415329ea1fa8099eb0
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159544"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Заметки о выпуске обновления 3 для Службы приложений Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описаны улучшения, исправления и известные проблемы в обновлении 3 для Службы приложений Azure в Azure Stack. Известные проблемы можно разделить на три категории: проблемы, которые непосредственно относятся к процессу развертывания, проблемы с обновлением и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Службу приложений Azure 1.3, примените обновление 1807 к интегрированной системе Azure Stack или разверните Пакет средств разработки Azure Stack последней версии.

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 3 для Службы приложений Azure в Azure Stack — **74.0.13698.31**.

### <a name="prerequisites"></a>Предварительные требования

Ознакомьтесь с [предварительными условиями для развертывания Службы приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md) перед началом развертывания.

Прежде чем выполнять обновление Службы приложений Azure в Azure Stack до версии 1.3, убедитесь, что все роли готовы в инструменте администрирования Службы приложений Azure на портале администрирования Azure Stack.

![Состояние роли службы приложений](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 3 Службы приложений Azure в Azure Stack включает в себя следующие улучшения и исправления.

- Возможность использования SQL Server Always On для базы данных поставщика ресурсов Службы приложений Azure.

- Добавлен новый параметр среды во вспомогательный скрипт Create-AADIdentityApp, упрощающий выбор регионов Azure AD.

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Они согласованы с версией пакета SDK для портала Azure Stack.

- Обновления **среды выполнения Функций Azure** до версии **v1.0.11820**.

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих исполняющих сред и инструментов**:
  - Добавлено ASP.NET Core 2.1.2
  - Добавлено NodeJS 10.0.0
  - Добавлено Zulu OpenJDK 8.30.0.1
  - Добавлены Tomcat 8.5.31 и 9.0.8
  - Добавлены PHP версии:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Добавлено Wincache 2.0.0.8
  - Обновлено Git для Windows для версии 2.17.1.2
  - Обновлено Kudu до 74.10611.3437
  
- **Обновления базовой операционной системы всех ролей**.
  - [Обновление стека обслуживания для Windows Server 2016 для x64-разрядных систем (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Накопительное обновление для Windows Server 2016 для x64-разрядных систем от июля 2018 г. (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Инструкция после обновления (необязательно)

Клиенты, желающие перейти на автономную базу данных существующей Службы приложений Azure в развернутой инфраструктуре Azure Stack, должны выполнить приведенные ниже действия после завершения обновления Службы приложений Azure в Azure Stack 1.3.

> [!IMPORTANT]
> Эта процедура занимает примерно 5–10 минут. Эта процедура включает в себя завершение существующих сеансов входа в базу данных. Спланируйте время простоя, чтобы перенести и проверить работу Службы приложений Azure на основе Azure Stack после миграции.
>
>

1. Добавьте [базы данных Службы приложений (appservice_hosting и appservice_metering) в группу доступности](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Включите автономную базу данных.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Преобразуйте базу данных в частично автономную. Этот шаг приведет к простою, так как все активные сеансы будут завершены.

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

Ознакомьтесь с заметками о выпуске Azure Stack 1807.

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- См. сведения о том, как [подготовиться к развертыванию Службы приложений Azure в Azure Stack](azure-stack-app-service-before-you-get-started.md).
