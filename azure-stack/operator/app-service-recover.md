---
title: Восстановление Службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте об аварийном восстановлении Службы приложений в Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 82498781e83aedf13a3ba33da24f484bc7e80d4b
ms.sourcegitcommit: 4eb1766c7a9d1ccb1f1362ae1211ec748a7d708c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69579025"
---
# <a name="app-service-recovery-on-azure-stack"></a>Восстановление Службы приложений в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*  

Этот раздел содержит инструкции по аварийному восстановлению Службы приложений.

Для восстановления Службы приложений в Azure Stack из резервной копии необходимо выполнить следующие действия:
1. Восстановление баз данных Службы приложений.
2. Восстановление содержимого общей папки файлового сервера.
3. Восстановление служб и ролей Службы приложений.

Если хранилище Azure Stack использовано для хранения приложений-функций, тогда вы также должны выполнить действия для их восстановления.

## <a name="restore-the-app-service-databases"></a>Восстановление баз данных Службы приложений
Базы данных Службы приложений SQL Server необходимо восстановить на готовом к работе экземпляре SQL Server. 

После [подготовки экземпляра SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) для размещения баз данных Службы приложений выполните следующие действия, чтобы восстановить базы данных из резервной копии:

1. Войдите на сервер SQL Server, на котором будут размещаться восстановленные базы данных Службы приложений, с правами администратора.
2. Используйте следующие команды для восстановления баз данных Службы приложений из командной строки с правами администратора.
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Убедитесь, что обе базы данных в Службе приложений успешно восстановлены, и выйдите из SQL Server Management Studio.

> [!NOTE]
> Чтобы выполнить восстановление после сбоя экземпляра отказоустойчивого кластера, обратитесь к разделу [Восстановление по журналу после сбоя экземпляра отказоустойчивого кластера](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Восстановление содержимого общей папки Службы приложений
После [подготовки файлового сервера](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) для размещения общей папки Службы приложений вам необходимо восстановить содержимое общей папки клиента из резервной копии. Вы можете использовать любой доступный метод копирования файлов в только что созданное расположение общей папки Службы приложений. При выполнении этого примера на файловом сервере для подключения к удаленной общей папке и копирования файлов в общую папку будут использоваться PowerShell и robocopy:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Помимо копирования содержимого общей папки, также следует сбросить разрешения для этой папки. Для этого откройте командную строку с правами администратора на компьютере файлового сервера и выполните файл **ReACL.cmd**. Файл **ReACL.cmd** находится в файлах установки Службы приложений в каталоге **BCDR**.

## <a name="restore-app-service-roles-and-services"></a>Восстановление служб и ролей Службы приложений
После восстановления баз данных Службы приложений и содержимого общей папки необходимо восстановить службы и роли Службы приложений с помощью PowerShell. Выполнив эти шаги, вы восстановите секреты Службы приложений и конфигурации службы.  

1. Войдите в виртуальную машину контроллера Службы приложений **CN0-VM** в качестве **roleadmin**, используя пароль, предоставленный во время установки Службы приложений. 
    > [!TIP]
    > Вам необходимо изменить группу безопасности сети виртуальной машины, чтобы разрешить подключения по протоколу удаленного рабочего стола. 
2. Скопируйте файл **SystemSecrets.JSON** локально на виртуальную машину контроллера. На следующем шаге необходимо указать путь к файлу в качестве параметра `$pathToExportedSecretFile`.
3. Используйте следующие команды в консоли PowerShell с повышенными привилегиями для восстановления служб и ролей Службы приложений:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Мы настоятельно рекомендуем закрыть этот сеанс PowerShell после выполнения команды.

## <a name="restore-function-apps"></a>Восстановление приложений-функций 
Служба приложений для Azure Stack не поддерживает восстановление пользовательских приложений клиента или данных, отличных от содержимого общей папки. Резервное копирование и восстановление всех прочих данных необходимо выполнять вне операций восстановления и создания резервных копий Службы приложений. Если хранилище Azure Stack использовано для хранения приложений-функций, чтобы восстановить утерянные данные, нужно выполнить следующие действия:

1. Создайте учетную запись хранения, которая будет использоваться приложением-функцией. Это может быть хранилище Azure Stack, Azure или любое совместимое хранилище.
2. Получите строку подключения к хранилищу.
3. Откройте портал функции и перейдите к приложению-функции.
4. Перейдите на вкладку **Функции платформы** и щелкните **Параметры приложения**.
5. Измените **AzureWebJobsDashboard** и **AzureWebJobsStorage** на новую строку подключения и щелкните **Сохранить**.
6. Переключитесь на **Обзор**.
7. Перезапустите приложение. На устранение всех ошибок может уйти несколько попыток.

## <a name="next-steps"></a>Дополнительная информация
[Обзор службы приложений в Azure Stack](azure-stack-app-service-overview.md)