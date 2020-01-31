---
title: Заметки о выпуске обновления 4 для Службы приложений Azure в Azure Stack Hub
description: Узнайте об улучшениях, исправлениях и известных проблемах в обновлении 4 для Службы приложений в Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 54eae566f2171c471f91e3aaeaf242b90998e962
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874422"
---
# <a name="app-service-on-azure-stack-hub-update-4-release-notes"></a>Заметки о выпуске обновления 4 для Службы приложений Azure в Azure Stack Hub

В этих заметках о выпуске описаны улучшения, исправления и известные проблемы в обновлении 4 для Службы приложений Azure в Azure Stack Hub. Известные проблемы можно разделить на три категории: проблемы, которые непосредственно относятся к процессу развертывания, проблемы с обновлением и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Службу приложений Azure 1.4, примените обновление 1809 к интегрированной системе Azure Stack Hub или разверните последнюю версию Пакета средств разработки Azure Stack Hub (ASDK).

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 4 для Службы приложений Azure в Azure Stack Hub — **78.0.13698.5**.

### <a name="prerequisites"></a>Предварительные требования

Ознакомьтесь с [предварительными условиями для развертывания Службы приложений в Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) перед началом развертывания.

Прежде чем начать обновление Службы приложений Azure в Azure Stack Hub до версии 1.4, выполните приведенные ниже действия.

- Убедитесь, что в разделе администрирования Службы приложений Azure на портале администрирования Azure Stack Hub настроены все роли.

- Выполните резервное копирование Службы приложений и баз данных master:
  - AppService_Hosting;
  - AppService_Metering;
  - master.

- Выполните резервное копирование общей папки с содержимым приложения клиента.

- Выполните синдикацию **расширения пользовательских сценариев** версии **1.9** в Azure Marketplace.

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 4 для Службы приложений Azure в Azure Stack Hub включает приведенные ниже улучшения и исправления.

- Разрешение уязвимости от межсайтовых сценариев (XSS) [CVE-2018-8600](https://aka.ms/CVE20188600).

- Добавлена поддержка API версии 2018-02-01 для Службы приложений.

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Они согласованы с версией пакета SDK для портала Azure Stack Hub.

- Обновления **среды выполнения Функций Azure** до версии **v1.0.11959**.

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих платформ приложений и инструментов**:
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

- Устранена проблема с конфигурацией, когда Azure Stack Hub заменяет сертификат клиента Azure Resource Manager.

- Восстановлены функциональные возможности в параметрах предоставления общего доступа к ресурсам независимо от источника (CORS) на портале пользователя Службы приложений.

- На портале администратора Службы приложений отображается сообщение об ошибке, когда на определенном уровне управления поставщика ресурсов не удается подключиться к настроенному экземпляру SQL Server.

- Убедитесь, что конечная точка задана в строке подключения пользовательского хранилища, если она указана в новом приложении-функции.

### <a name="post-deployment-steps"></a>Действия, выполняемые после развертывания

> [!IMPORTANT]  
> Если вы указали поставщик ресурсов Службы приложений с помощью экземпляра SQL Always On, в группу доступности *необходимо* [добавить базы данных appservice_hosting и appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), а затем синхронизировать эти базы данных, чтобы предотвратить остановку службы в случае отработки отказа.

### <a name="post-update-steps-optional"></a>Инструкция после обновления (необязательно)

Клиенты, желающие перейти на автономную базу данных существующей Службы приложений Azure в развернутой инфраструктуре Azure Stack Hub, должны выполнить приведенные ниже действия после завершения обновления Службы приложений Azure в Azure Stack Hub 1.4.

> [!IMPORTANT]
> Процедура миграции занимает примерно 5–10 минут. Эта процедура включает в себя завершение существующих сеансов входа в базу данных. Спланируйте время простоя, чтобы перенести и проверить работу Службы приложений Azure на основе Azure Stack Hub после миграции. Если вы выполнили эти действия после обновления Службы приложений Azure в Azure Stack Hub 1.3, повторять их не нужно.

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
    ```

1. Перенос учетных данных в пользователи автономной базы данных.

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

- Рабочим ролям не удается связаться с файловым сервером, если служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети. Эта проблема описана в документации по развертыванию Службы приложений Azure в Azure Stack Hub.

Если вы решили выполнить развертывание в существующей виртуальной сети с использованием внутреннего IP-адреса для подключения к файловому серверу, необходимо добавить правило безопасности для исходящего трафика, разрешающее передачу трафика SMB между подсетью рабочей роли и файловым сервером. Для этого перейдите к группе безопасности сети WorkersNsg на портале администрирования и добавьте правило безопасности для исходящего трафика со следующими свойствами.

 * Источник: Любой
 * Диапазон исходных портов: *.
 * Назначение: IP-адреса
 * Диапазон конечных IP-адресов: диапазон IP-адресов для файлового сервера
 * Диапазон конечных портов: 445
 * Протокол: TCP
 * Действие: Allow
 * Приоритет: 700
 * Имя: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Известные проблемы, с которыми сталкиваются администраторы облака, работающие со службой приложений Azure в Azure Stack Hub

Обратитесь к документации в статье [Обновления Azure Stack Hub: заметки о выпуске](azure-stack-update-1903.md) (1809).

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о Службе Приложений Azure в Azure Stack Hub см. в [этой статье](azure-stack-app-service-overview.md).
- Дополнительные сведения о том, как подготовиться к развертыванию службы приложений Azure в Azure Stack Hub, см. в статье о [предварительных условиях для развертывания Службы приложений в Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
