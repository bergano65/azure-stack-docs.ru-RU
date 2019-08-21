---
title: Создание резервной копии Службы приложений Azure в Azure Stack | Документация Майкрософт
description: Узнайте, как выполнять резервное копирование служб приложений в Azure Stack.
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
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b49390434990ac2efb81692c1177c634aee4bab0
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959527"
---
# <a name="back-up-app-service-on-azure-stack"></a>Создание резервной копии Службы приложений в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*  

Этот документ содержит инструкции по созданию резервной копии службы приложений в Azure Stack.

> [!IMPORTANT]
> Резервная копия службы приложений в Azure Stack не создается при [резервном копировании инфраструктуры Azure Stack](azure-stack-backup-infrastructure-backup.md). Как оператор Azure Stack, вы должны выполнить шаги, чтобы убедиться, что при необходимости Службу приложений можно успешно восстановить.

В Службе приложений Azure в Azure Stack есть четыре основных компонента, которые следует учитывать при планировании аварийного восстановления:
1. Инфраструктура поставщика ресурсов, роли сервера, уровни рабочей роли и т. д. 
2. Секреты службы приложений.
3. Базы данных размещения и измерений службы приложений SQL Server.
4. Содержимое рабочей нагрузки пользователя службы приложений, хранимой в общей папке службы приложений.

## <a name="back-up-app-service-secrets"></a>Создание резервной копии секретов Службы приложений
При восстановлении службы приложений из резервной копии вы должны предоставить ключи службы приложений, используемые при первоначальном развертывании. Эти сведения следует сохранить сразу после успешного развертывания Службы приложений и ее сохранения в безопасном расположении. Конфигурация инфраструктуры поставщика ресурсов будет повторно создана из резервной копии во время восстановления с использованием командлетов PowerShell для восстановления службы приложений.

Для создания резервной копии секретов Службы приложений используйте портал администрирования, выполнив следующие шаги: 

1. Войдите на портал администрирования Azure Stack с правами администратора служб.

2. Последовательно выберите **Служба приложений** -> **Секреты**. 

3. Выберите **Загрузка секретов**.

   ![Скачивание секретов с портала администрирования Azure Stack](./media/app-service-back-up/download-secrets.png)

4. Когда секреты будут готовы к скачиванию, щелкните **Сохранить** и сохраните файл секретов Службы приложений (**SystemSecrets.JSON**) в безопасном расположении. 

   ![Сохранение секретов с помощью портала администрирования Azure Stack](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Повторяйте эти шаги после каждой смены секретов Службы приложений.

## <a name="back-up-the-app-service-databases"></a>Создание резервной копии баз данных Службы приложений
Чтобы восстановить службу приложений, потребуются резервные копии баз данных **Appservice_hosting** и **Appservice_metering**. Мы рекомендуем использовать планы обслуживания SQL Server или Azure Backup Server, чтобы обеспечить регулярное резервное копирование и безопасное сохранение этих баз данных. Тем не менее можно использовать любой метод обеспечения регулярного резервного копирования SQL.

Для ручного резервного копирования этих баз данных при входе в SQL Server используйте следующие команды PowerShell.

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Если необходимо создать резервную копию баз данных группы доступности AlwaysOn SQL, следуйте [этим инструкциям](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

После успешного создания резервных копий всех баз данных скопируйте BAK-файлы в безопасное расположение вместе с информацией о секретах службы приложений.

## <a name="back-up-the-app-service-file-share"></a>Создание резервной копии общей папки Службы приложений
Служба приложений хранит информацию о приложении клиента в общей папке. Чтобы при восстановлении терялось как можно меньше данных, необходимо регулярно создавать резервные копии этой общей папки, а также баз данных службы приложений.

Для создания резервной копии содержимого общей папки службы приложений можно использовать Azure Backup Server или другой метод регулярного копирования содержимого общей папки в расположение, в котором сохранялась вся предыдущая информация о восстановлении.

Например, выполните приведенные ниже действия, чтобы использовать Robocopy в сеансе консоли Windows PowerShell (не интегрированной среды сценариев PowerShell).

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Дополнительная информация
[Recovery of App Service on Azure Stack](app-service-recover.md) (Восстановление Службы приложений в Azure Stack)