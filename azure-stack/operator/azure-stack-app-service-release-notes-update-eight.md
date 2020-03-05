---
title: Заметки о выпуске обновления 8 для Службы приложений Azure в Azure Stack Hub
description: Узнайте о том, что находится в пакете обновления 8 для Службы приложений в Azure Stack Hub, какие есть известные проблемы и откуда скачать обновление.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 02/25/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 56838a95c7c937c6fcbbe878ca284ce27d3d1397
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695603"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Заметки о выпуске обновления 8 для Службы приложений Azure в Azure Stack Hub

В этих заметках о выпуске описаны улучшения, исправления и известные проблемы в обновлении 8 для Службы приложений Azure в Azure Stack Hub. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу развертывания, обновления, и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Службу приложений Azure 1.8, примените обновление 1910 к интегрированной системе Azure Stack или разверните последний Пакет средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 8 для Службы приложений Azure в Azure Stack Hub — **86.0.2.13**.

### <a name="prerequisites"></a>Предварительные требования

Перед началом развертывания ознакомьтесь со статьей [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).

Прежде чем начать обновление Службы приложений Azure в Azure Stack до версии 1.8, сделайте следующее:

- Убедитесь, что все роли готовы в разделе администрирования Службы приложений Azure на портале администрирования Azure Stack.

- Выполните резервное копирование Службы приложений и баз данных master:
  - AppService_Hosting;
  - AppService_Metering;
  - master.

- Выполните резервное копирование общей папки с содержимым приложения клиента.

- Выполните синдикацию **расширения пользовательских сценариев** версии **1.9.3** из Marketplace.

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 8 для Службы приложений Azure в Azure Stack включает следующие улучшения и исправления:

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Согласованы с версией пакета SDK для портала Azure Stack.

- Обновление **среды выполнения Функций Azure** до версии **v1.0.12615**.

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих исполняющих сред и инструментов**:
  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - Модуль ASP.NET Core 13.1.19331.0 версии 2
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - Обновление Kudu до версии 85.11024.4154
  - 3\.5.80916.15 (MSDeploy)
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git для Windows 2.19.1.0.

- **Обновления базовой операционной системы всех ролей**.
  - [Накопительное обновление для 64-разрядных систем Windows Server 2016 за декабрь 2019 г. (KB4530689)](https://support.microsoft.com/help/4530689)

- **Поддержка управляемого диска для новых развертываний**

Все новые развертывания службы приложений Azure в Azure Stack Hub будут использовать управляемые диски для всех виртуальных машин и масштабируемых наборов виртуальных машин.  Это не повлияет на использование всеми имеющимися развертываниями неуправляемых дисков.

- **TLS 1.2, применяемый подсистемой балансировки нагрузки сервера переднего плана**

Начиная с этого обновления **TLS 1.2** будет применяться ко всем приложениям.

### <a name="known-issues-upgrade"></a>Известные проблемы (обновление)

- Обновление завершится ошибкой, если выполнена отработка отказа кластера SQL Server Always On на дополнительный узел

Во время обновления выполняется вызов для проверки существования базы данных с помощью строки подключения к базе данных master, который завершится ошибкой, так как имя входа было на предыдущем главном узле.

Выполните одно из следующих действий и нажмите кнопку "Повторить" в установщике.

- Скопируйте имя для входа appservice_hostingAdmin с дополнительного узла SQL.

**OR**

- Выполните отработку отказа кластера SQL на предыдущий активный узел.

### <a name="post-deployment-steps"></a>Действия, выполняемые после развертывания

> [!IMPORTANT]
> Если вы указали поставщик ресурсов Службы приложений с помощью экземпляра SQL Always On, [к группе доступности необходимо добавить базы данных appservice_hosting и appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), а затем синхронизировать эти базы данных, чтобы избежать прекращения работы службы в случае отработки отказа.

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Рабочим ролям не удается связаться с файловым сервером, если Служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети, как это описано в документации по развертыванию Службы приложений Azure в Azure Stack.

  Если вы решили выполнить развертывание в существующей виртуальной сети с использованием внутреннего IP-адреса для подключения к файловому серверу, необходимо добавить правило безопасности для исходящего трафика, разрешающее передачу трафика SMB между подсетью рабочей роли и файловым сервером. Для этого перейдите к группе безопасности сети WorkersNsg на портале администрирования и добавьте правило безопасности для исходящего трафика со следующими свойствами.
  - Источник: Любой
  - Диапазон исходных портов: *.
  - Назначение: IP-адреса
  - Диапазон конечных IP-адресов: диапазон IP-адресов для файлового сервера
  - Диапазон конечных портов: 445
  - Протокол: TCP
  - Действие: Allow
  - Приоритет: 700
  - Имя: Outbound_Allow_SMB445

- Для новых развертываний Службы приложений Azure в Azure Stack Hub 1.8 требуется, чтобы базы данных были преобразованы в автономные базы данных.

Ввиду регрессии в этом выпуске базы данных Службы приложений (appservice_hosting и appservice_metering) должны быть преобразованы в автономные базы данных при развертывании **новых** служб.  Эта **не** влияет на **обновленные** развертывания.

> [!IMPORTANT]
> Эта процедура занимает примерно 5–10 минут. Эта процедура включает в себя завершение существующих сеансов входа в базу данных. Спланируйте время простоя, чтобы перенести Службу приложений Azure и проверить ее на основе Azure Stack Hub после миграции
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

- Не удается расширить рабочие роли

  Новым рабочим ролям не удается получить необходимую строку подключения к базе данных.  Чтобы исправить эту ситуацию, подключитесь к одному из экземпляров контроллера, например CN0-VM, и выполните следующий сценарий PowerShell.

  ```powershell
 
    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager
    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()
            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()
            $conn.Open()

            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName
            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }
    $siteManager.CommitChanges()
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы для облачных администраторов, работающих со службой приложений Azure в Azure Stack

См. документацию в [заметках о выпуске Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Дальнейшие действия

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- Дополнительные сведения о том, как подготовиться к развертыванию службы приложений Azure в Azure Stack, см. в разделе [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).
