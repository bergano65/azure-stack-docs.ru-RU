---
title: Поддержка поставщика ресурсов MySQL в Azure Stack | Документация Майкрософт
description: Сведения о поддержке службы поставщика ресурсов MySQL в Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: d9d91dce762265da498a0a1232b023c2956edefd
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617848"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Операции поддержки для поставщиков ресурсов MySQL

Поставщик ресурсов MySQL выполняется на заблокированной виртуальной машине. Чтобы включить операции обслуживания, вам нужно обновить систему безопасности виртуальной машины. Чтобы применить для этого принцип предоставления минимальных прав, можно использовать конечную точку DBAdapterMaintenance PowerShell Just Enough Administration (JEA). Пакет установки поставщика ресурсов содержит сценарий для этой операции.

## <a name="update-the-virtual-machine-operating-system"></a>Обновление ОС виртуальной машины

Так как поставщик ресурсов выполняется на *пользовательской* виртуальной машине, нужно применять необходимые исправления и обновления по мере их выпуска. Для применения обновлений к виртуальной машине можно использовать пакеты обновления Windows, предоставляемые в рамках цикла исправлений и обновлений.

Обновите поставщика виртуальной машины с помощью одного из следующих способов:

- Установка пакета последней версии поставщика ресурсов с помощью текущего исправленного образа Windows Server 2016 Core.
- Установка пакета обновления Windows во время установки или обновления поставщика ресурсов.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Обновление определений Защитника Windows на виртуальной машине

Чтобы обновить определения Защитника, сделайте следующее:

1. Загрузите обновление определений Защитника Windows на странице [обновлений определений для антивирусной программы "Защитник Windows" и других антивирусных программ корпорации Майкрософт](https://www.microsoft.com/en-us/wdsi/definitions).

    На странице определений прокрутите список вниз до пункта "Скачать и установить определения вручную". Скачайте 64-разрядный файл "Windows Defender Antivirus for Windows 10 and Windows 8.1" (Антивирусная программа "Защитник Windows" для Windows 10 и Windows 8.1).

    Кроме того, можно использовать [эту прямую ссылку](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64), чтобы скачать и установить файл fpam-fe.exe.

2. Откройте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов MySQL.

3. Скопируйте файл обновления определений на виртуальную машину адаптера поставщика ресурсов, используя сеанс обслуживания конечной точки.

4. Во время сеанса обслуживания PowerShell выполните команду _Update-DBAdapterWindowsDefenderDefinitions_.

5. После установки определений рекомендуется удалить этот файл обновления определений с помощью команды _Remove-ItemOnUserDrive_.

**Пример сценария PowerShell для обновления определений.**

Вы можете изменить и запустить приведенный ниже сценарий, чтобы обновить определения Защитника. Замените значения в сценарии значениями для своей среды.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
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

## <a name="secrets-rotation"></a>Смена секретов

*Эти инструкции применяются только к интегрированным системам Azure Stack.*

При использовании поставщиков ресурсов SQL и MySQL в интегрированных системах Azure Stack оператор Azure Stack отвечает за смену следующих секретов инфраструктуры поставщика ресурсов, чтобы обеспечить их актуальность:

- внешний SSL-сертификат, [предоставленный во время развертывания](azure-stack-pki-certs.md);
- пароль учетной записи локального администратора виртуальной машины поставщика ресурсов, предоставленный во время развертывания;
- пароль пользователя диагностики (dbadapterdiag) поставщика ресурсов.

### <a name="powershell-examples-for-rotating-secrets"></a>Примеры PowerShell для смены секретов

**Смена всех секретов одновременно**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Смена только пароля пользователя диагностики**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**Смена пароля учетной записи локального администратора виртуальной машины**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Смена пароля для SSL-сертификата**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Параметры SecretRotationMySQLProvider.ps1

|Параметр|ОПИСАНИЕ|
|-----|-----|
|AzCredential|Учетные данные учетной записи администратора службы Azure Stack.|
|CloudAdminCredential|Учетные данные учетной записи домена администратора облака Azure Stack.|
|PrivilegedEndpoint|Привилегированная конечная точка для получения информации о метке Azure Stack (Get-AzureStackStampInformation).|
|DiagnosticsUserPassword|Пароль учетной записи для пользователя диагностики.|
|VMLocalCredential|Учетная запись локального администратора виртуальной машины MySQLAdapter.|
|DefaultSSLCertificatePassword|Пароль SSL-сертификата (*pfx) по умолчанию.|
|DependencyFilesLocalPath|Локальный путь к файлам зависимостей.|
|     |     |

### <a name="known-issues"></a>Известные проблемы

**Проблема.**<br>
Не выполняется автоматический сбор журналов для смены секретов, если происходит сбой скрипта смены секретов при его запуске.

**Возможное решение.**<br>
Используйте командлет Get-AzsDBAdapterLogs, чтобы собрать все журналы поставщика ресурсов, включая AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log в каталоге C:\Logs.

## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики

Чтобы собрать данные журналов с заблокированной виртуальной машины, можно использовать конечную точку DBAdapterDiagnostics PowerShell Just Enough Administration (JEA). Она предоставляет следующие команды:

- **Get-AzsDBAdapterLog**. Эта команда создает пакет ZIP с журналами диагностики поставщика ресурсов и сохраняет этот файл на пользовательском диске сеанса. Эту команду можно выполнить без параметров, чтобы собрать данные журналов за последние четыре часа.

- **Remove-AzsDBAdapterLog**. Эта команда удаляет имеющиеся пакеты журналов на виртуальной машине поставщика ресурсов.

### <a name="endpoint-requirements-and-process"></a>Требования и процесс работы конечной точки

При установке или обновлении поставщика ресурсов создается учетная запись пользователя dbadapterdiag. Она используется для сбора журналов диагностики.

>[!NOTE]
>Пароль учетной записи dbadapterdiag такой же, как и у локального администратора на виртуальной машине, созданной при развертывании или обновлении поставщика.

Чтобы использовать команды _DBAdapterDiagnostics_, нужно создать удаленный сеанс PowerShell для виртуальной машины поставщика ресурсов и выполнить команду **Get-AzsDBAdapterLog**.

Вы можете задать период для сбора журналов с помощью параметров **FromDate** и **ToDate**. Если один или оба этих параметра не заданы, используются следующие значения по умолчанию:

* FromDate — за четыре часа до текущего момента.
* ToDate — текущий момент времени.

**Пример сценария PowerShell для сбора журналов.**

Приведенный ниже сценарий показывает, как собирать данные журналов диагностики на виртуальной машине поставщика ресурсов.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
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

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Дополнительная информация

[Удаление поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-remove.md)
