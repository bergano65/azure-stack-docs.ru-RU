---
title: Резервное копирование для Azure Stack Hub
description: Сведения о том, как выполнять резервное копирование по требованию для Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2019
ms.openlocfilehash: 10a7c60a18faa5528fb9c1917c62994b43bee51a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701043"
---
# <a name="back-up-azure-stack-hub"></a>Резервное копирование для Azure Stack Hub

В этой статье рассказывается о том, как выполнять резервное копирование по требованию для Azure Stack Hub. Инструкции по настройке среды PowerShell приведены в статье [Установка PowerShell для Azure Stack Hub](azure-stack-powershell-install.md). Чтобы войти в Azure Stack Hub, см. [Использование портала администрирования в Azure Stack Hub](azure-stack-manage-portals.md).

## <a name="start-azure-stack-hub-backup"></a>Запуск резервного копирования Azure Stack Hub

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Запуск нового резервного копирования без отслеживания хода выполнения задания
Используйте команду Start-AzSBackup для немедленного запуска нового резервного копирования без отслеживания прогресса выполнения задания.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>Запуск резервного копирования Azure Stack Hub с отслеживанием хода выполнения задания
Запустите новое резервное копирование с помощью команды Start-AzSBackup с параметром **-AsJob** и сохраните его в качестве переменной, позволяющей отслеживать ход выполнения создания резервной копии.

> [!NOTE]
> Задание резервного копирования будет обозначено на портале как успешно выполненное на 10–15 минут раньше, чем завершится само задание.
>
> Реальное состояние лучше узнавать с помощью приведенного ниже кода.

> [!IMPORTANT]
> Вводится начальная задержка в 1 миллисекунду, так как код слишком быстро правильно регистрирует задание, из-за чего не возвращает результат для **PSBeginTime** и **состояние** задания.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
```

## <a name="confirm-backup-has-completed"></a>Подтверждение завершения резервного копирования

### <a name="confirm-backup-has-completed-using-powershell"></a>Подтверждение завершения резервного копирования с помощью PowerShell
Используйте следующие команды PowerShell, чтобы убедиться, что резервное копирование успешно завершено:

```powershell
   Get-AzsBackup
```

В результате должно отобразиться следующее:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>Подтверждение завершения резервного копирования с помощью портала администрирования
Используйте портал администрирования Azure Stack Hub для проверки успешности завершения резервного копирования, выполнив следующие шаги:

1. Откройте [портал администрирования Azure Stack Hub](azure-stack-manage-portals.md).
2. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Инфраструктура резервного копирования**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о рабочем процессе по [восстановлению после потери данных](azure-stack-backup-recover-data.md).
