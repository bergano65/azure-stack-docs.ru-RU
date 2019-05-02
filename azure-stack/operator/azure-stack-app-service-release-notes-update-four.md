---
title: Заметки о выпуске обновления 4 для службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте о том, что находится в пакете обновления 4 службы приложений в Azure Stack, об известных проблемах и откуда скачать обновление.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: fb9a6b88832dde48c47ce6361e090a7508e42332
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306446"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Служба приложений Azure в заметках о выпуске обновления 4 Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описываются улучшения и исправления в обновлении 4 Службы приложений Azure в Azure Stack и известные проблемы. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу развертывания, обновления, и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать службу приложений Azure, примените обновление 1809 к интегрированной системе Azure Stack или разверните последний пакет средств разработки Azure Stack 1.4.
>
>

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 4 Службы приложений Azure в Azure Stack — **78.0.13698.5**.

### <a name="prerequisites"></a>Предварительные требования

Перед началом развертывания ознакомьтесь со статьей [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).

Прежде чем начать обновление Службы приложений Azure в Azure Stack до версии 1.4:

- Убедитесь, что все роли готовы в разделе администрирования Службы приложений Azure на портале администрирования Azure Stack.

- Выполните резервное копирование Службы приложений и баз данных master:
  - AppService_Hosting;
  - AppService_Metering;
  - master.

- Выполните резервное копирование общей папки с содержимым приложения клиента.

- Выполните синдикацию расширения пользовательских сценариев версии 1.9 из Marketplace.

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 4 Службы приложений Azure в Azure Stack включает в себя следующие улучшения и исправления.

- Разрешение уязвимости от межсайтовых сценариев [CVE-2018-8600](https://aka.ms/CVE20188600).

- Добавлена поддержка API версии 2018-02-01 для Службы приложений.

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Согласованы с версией пакета SDK для портала Azure Stack.

- Обновления **среды выполнения Функций Azure** до версии **v1.0.11959**.

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих исполняющих сред и инструментов**:
  - Добавлено NodeJS 10.6.0.
  - Добавлено NPM 6.1.0.
  - Добавлено Zulu OpenJDK 8.31.0.2.
  - Добавлены Tomcat 8.5.34 и 9.0.12.
  - Добавлены PHP версии:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Обновление версий Python:
    - 2.7.15
    - 3.6.6
  - Обновлено Git для Windows для версии 2.17.1.2
  - Kudu обновлено до версии 78.11022.3613.
  
- **Обновления базовой операционной системы всех ролей**.
  - [Накопительное обновление для Windows Server 2016 для x64-разрядных систем от октября 2018 г. (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Устранена проблема проверки шаблона при развертывании элементов коллекции Wordpress, DNN и Orchard CMS.

- Устранена проблема с конфигурацией, когда Azure Stack заменяет сертификат клиента Azure Resource Manager.

- Восстановлены функциональные возможности в параметрах предоставления общего доступа к ресурсам независимо от источника (CORS) на портале клиента Службы приложений.

- В интерфейсе администрирования Службы приложений отображается сообщение об ошибке, когда уровень управления поставщика ресурсов не может подключиться к настроенному экземпляру SQL Server.

- Убедитесь, что конечная точка задана в строке подключения пользовательского хранилища, если она указана в новом приложении-функции.

### <a name="post-deployment-steps"></a>Действия, выполняемые после развертывания

> [!IMPORTANT]  
> Если вы указали план восстановления Службы приложений с помощью экземпляра SQL Always On, [к группе доступности необходимо добавить базы данных appservice_hosting и appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), а затем синхронизировать эти базы данных, чтобы избежать прекращения работы службы в случае отработки отказа.

### <a name="post-update-steps-optional"></a>Инструкция после обновления (необязательно)

Для клиентов, желающих перейти на автономную базу данных для существующей Службы приложений Azure на развернутой Azure Stack, нужно выполнить следующие действия после завершения обновления Службы приложений Azure в Azure Stack 1.4.

> [!IMPORTANT]
> Процедура миграции занимает примерно 5–10 минут.  Эта процедура включает в себя завершение существующих сеансов входа в базу данных.  Спланируйте время простоя, чтобы перенести и проверить работу Службы приложений Azure на основе Azure Stack после миграции.  Если вы выполнили следующие шаги после обновления Службы приложений Azure в Azure Stack 1.3, эти действия не требуются.
>
>

1. Добавьте [базы данных службы приложений (appservice_hosting и appservice_metering) в группу доступности](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Включите автономную базу данных
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. При преобразовании базы данных на базу данных с частичным наполнением произойдет простой, так как необходимо завершить все активные сеансы.

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

1. Migrate Logins to Contained Database Users

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

Проверка

1. Включите SQL Server

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Проверьте текущий режим работы
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Рабочим ролям не удается связаться с файловым сервером, если Служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети, как это описано в документации по развертыванию Службы приложений Azure в Azure Stack.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы для облачных администраторов, работающих со службой приложений Azure в Azure Stack

Обратитесь к документации в разделе [Обновление 1809 для Azure Stack](azure-stack-update-1903.md)

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- Дополнительные сведения о том, как подготовиться к развертыванию службы приложений Azure в Azure Stack, см. в разделе [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).
