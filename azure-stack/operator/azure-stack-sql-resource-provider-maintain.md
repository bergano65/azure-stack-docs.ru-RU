---
title: Операции поддержки для поставщиков ресурсов SQL
titleSuffix: Azure Stack Hub
description: Узнайте об операциях поддержки для поставщиков ресурсов SQL в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: ed76e3611fe0b7b57386a7b688f08ddbdc3c36d7
ms.sourcegitcommit: b7b86e875cf04cb0fd9d48a2b830588d3ff99b6d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77125823"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Операции поддержки для поставщиков ресурсов SQL

Поставщик ресурсов SQL выполняется на заблокированной виртуальной машине. Чтобы включить операции обслуживания, вам нужно обновить систему безопасности виртуальной машины. Чтобы применить для этого принцип предоставления минимальных прав, используйте конечную точку *DBAdapterMaintenance*[PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview). Пакет установки поставщика ресурсов содержит скрипт для этого действия.

## <a name="patching-and-updating"></a>Установка исправлений и обновлений

Поставщик ресурсов SQL не обслуживается как часть Azure Stack Hub, так как это дополнительный компонент. Корпорация Майкрософт предоставляет обновления поставщика ресурсов SQL по мере необходимости. При выпуске обновленного адаптера SQL предоставляется сценарий для применения обновления. Этот сценарий создает виртуальную машину поставщика ресурсов, перенося в нее состояние старой виртуальной машины поставщика. Дополнительную информацию см. в статье [Обновление поставщика ресурсов SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Виртуальная машина поставщика

Так как поставщик ресурсов выполняется на *пользовательской* виртуальной машине, нужно применять необходимые исправления и обновления по мере их выпуска. Для применения обновлений к виртуальной машине используйте пакеты обновления Windows, предоставляемые в рамках цикла исправлений и обновлений.

## <a name="updating-sql-credentials"></a>Обновление учетных данных SQL

Вы несете ответственность за создание и обслуживание учетных записей системного администратора на серверах SQL. Поставщику ресурсов нужна учетная запись с этими привилегиями для управления базами данных для пользователей. При этом доступ к данным этих пользователей не требуется. Если требуется обновить пароли системного администратора на серверах SQL, можно использовать интерфейс администратора поставщика ресурсов, чтобы изменить сохраненный пароль. Эти пароли сохраняются в Key Vault в экземпляре Azure Stack Hub.

Чтобы изменить параметры, выберите **Обзор** &gt; **Ресурсы администрирования** &gt; **Серверы размещения SQL** &gt; **Имена входа SQL** и выберите имя пользователя. Сначала нужно внести изменения в экземпляр SQL (и любые реплики, если необходимо). В разделе **Параметры** выберите **Пароль**.

![Обновление пароля администратора SQL](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>Смена секретов

*Эти инструкции применяются только к интегрированным системам Azure Stack Hub.*

При использовании поставщиков ресурсов SQL и MySQL в интегрированных системах Azure Stack Hub оператор Azure Stack Hub отвечает за смену следующих секретов инфраструктуры поставщика ресурсов, чтобы обеспечить их актуальность:

- внешний SSL-сертификат, [предоставленный во время развертывания](azure-stack-pki-certs.md);
- пароль учетной записи локального администратора виртуальной машины поставщика ресурсов, предоставленный во время развертывания;
- пароль пользователя диагностики (dbadapterdiag) поставщика ресурсов.

### <a name="powershell-examples-for-rotating-secrets"></a>Примеры PowerShell для смены секретов

**Смена всех секретов одновременно**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Смена только пароля пользователя диагностики**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Измените пароль учетной записи локального администратора виртуальной машины.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Смена пароля для SSL-сертификата**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Параметры SecretRotationSQLProvider.ps1

|Параметр|Описание|
|-----|-----|
|AzCredential|Учетные данные учетной записи администратора службы Azure Stack Hub.|
|CloudAdminCredential|Учетные данные учетной записи домена администратора облака Azure Stack Hub.|
|PrivilegedEndpoint|Привилегированная конечная точка для получения информации о метке Azure Stack (Get-AzureStackStampInformation).|
|DiagnosticsUserPassword|Пароль учетной записи для пользователя диагностики.|
|VMLocalCredential|Учетная запись локального администратора на виртуальной машине MySQLAdapter.|
|DefaultSSLCertificatePassword|Пароль SSL-сертификата (*pfx) по умолчанию.|
|DependencyFilesLocalPath|Локальный путь к файлам зависимостей.|
|     |     |

### <a name="known-issues"></a>Известные проблемы

**Проблема**.<br>
журналы смены секретов. Не выполняется автоматический сбор журналов для смены секретов, если происходит сбой настраиваемого скрипта смены секретов при его запуске.

**Возможное решение**:<br>
используйте командлет Get-AzsDBAdapterLogs, чтобы собрать все журналы поставщика ресурсов, включая AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log в каталоге C:\Logs.

## <a name="update-the-vm-operating-system"></a>Обновление операционной системы виртуальной машины

Для обновления ОС виртуальной машины используйте один из следующих способов:

- Установка пакета последней версии поставщика ресурсов с помощью текущего исправленного образа Windows Server 2016 Core.
- Установка пакета обновления Windows во время установки или обновления поставщика ресурсов.

## <a name="update-the-vm-windows-defender-definitions"></a>Обновление определений Защитника Windows

Обновление определений Защитника Windows:

1. Скачайте обновление определений Защитника Windows на странице [обновлений механизма обнаружения угроз для программы "Защитник Windows"](https://www.microsoft.com/wdsi/definitions).

   На странице обновления определений прокрутите список вниз до пункта Manually download the update (Скачать обновление вручную). Скачайте 64-разрядный файл "Windows Defender Antivirus for Windows 10 and Windows 8.1" (Антивирусная программа "Защитник Windows" для Windows 10 и Windows 8.1).

   Вы также можете использовать [эту прямую ссылку](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64), чтобы скачать и установить файл fpam-fe.exe.

2. Создайте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов SQL.

3. Скопируйте файл обновления определений на виртуальную машину, используя сеанс конечной точки обслуживания.

4. Во время сеанса обслуживания PowerShell выполните команду *Update-DBAdapterWindowsDefenderDefinitions*.

5. После установки определений советуем удалить этот файл обновления определений с помощью команды *Remove-ItemOnUserDrive*.

**Пример скрипта PowerShell для обновления определений**

Вы можете изменить и запустить приведенный ниже сценарий, чтобы обновить определения Защитника. Замените значения в сценарии значениями для своей среды.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter VM.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики

Чтобы собрать данные журналов с заблокированной виртуальной машины, используйте конечную точку *DBAdapterDiagnostics* PowerShell Just Enough Administration (JEA). Она предоставляет следующие команды:

- **Get-AzsDBAdapterLog**. Эта команда создает пакет ZIP с журналами диагностики поставщика ресурсов и сохраняет этот файл на пользовательском диске сеанса. Эту команду можно выполнить без параметров, чтобы собрать данные журналов за последние четыре часа.
- **Remove-AzsDBAdapterLog**. Эта команда удаляет имеющиеся пакеты журналов на виртуальной машине поставщика ресурсов.

### <a name="endpoint-requirements-and-process"></a>Требования и процесс работы конечной точки

При установке или обновлении поставщика ресурсов создается учетная запись пользователя **dbadapterdiag**. Она используется для сбора журналов диагностики.

>[!NOTE]
>Пароль учетной записи dbadapterdiag такой же, как и у локального администратора на виртуальной машине, созданной при развертывании или обновлении поставщика.

Чтобы использовать команды *DBAdapterDiagnostics*, нужно создать удаленный сеанс PowerShell для виртуальной машины поставщика ресурсов и выполнить команду **Get-AzsDBAdapterLog**.

Вы можете задать период для сбора журналов с помощью параметров **FromDate** и **ToDate**. Если один или оба этих параметра не заданы, используются следующие значения по умолчанию:

- FromDate — за четыре часа до текущего момента.
- ToDate — текущий момент времени.

**Пример скрипта PowerShell для сбора журналов**

Приведенный ниже сценарий показывает, как собирать данные журналов диагностики на виртуальной машине поставщика ресурсов.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```
## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>Настройка расширения Диагностики Azure для поставщика ресурсов SQL
Расширение Диагностики Azure по умолчанию устанавливается на виртуальную машину адаптера поставщика ресурсов SQL. Далее приведены инструкции по настройке расширения для сбора операционных журналов событий поставщика ресурсов SQL и журналов IIS с целью устранения неполадок и аудита.

1. Войдите на портал администратора Azure Stack Hub.

2. Выберите **Виртуальные машины** на панели слева, найдите виртуальную машину адаптера поставщика ресурсов SQL и выберите ее.

3. В разделе **Параметры диагностики** на виртуальной машине откройте вкладку **Журналы** и выберите **Настройка**, чтобы настроить собираемые журналы событий.
![Переход к параметрам диагностики](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. Добавьте **Microsoft-AzureStack-DatabaseAdapter/Operational!\*** для сбора операционных журналов событий поставщика ресурсов SQL.
![Добавление журналов событий](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. Чтобы включить сбор журналов IIS, установите флажки **Журналы IIS** и **Журналы неудачных запросов**.
![Добавление журналов IIS](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. Наконец, нажмите кнопку **Сохранить**, чтобы сохранить все параметры диагностики.

После настройки сбора журналов событий и журналов IIS для поставщика ресурсов SQL эти журналы можно найти в системной учетной записи хранения с именем **sqladapterdiagaccount**.

См. [основные сведения о расширении Диагностики Azure](/azure/azure-monitor/platform/diagnostics-extension-overview).

## <a name="next-steps"></a>Дальнейшие действия

[Добавление серверов размещения SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
