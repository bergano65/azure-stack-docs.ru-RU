---
title: Обновление поставщика ресурсов SQL в Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Узнайте, как обновить поставщик ресурсов SQL в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 07f372f1974780d2310b12cc8d874808e010ac3c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881265"
---
# <a name="update-the-sql-resource-provider"></a>Обновление поставщика ресурсов SQL

При обновлении сборки Azure Stack Hub может быть выпущен новый поставщик ресурсов SQL. Хотя существующий поставщик ресурсов может продолжать работать, мы рекомендуем как можно скорее обновить его до последней сборки.

Начиная с выпуска поставщика ресурсов SQL версии 1.1.33.0, обновления являются накопительными и не обязательно должны устанавливаться в порядке, в котором они были выпущены (если вы начинаете с версии 1.1.24.0 или более поздней). Например, если вы работаете с версией 1.1.24.0 поставщика ресурсов SQL, вы можете выполнить обновление до версии 1.1.33.0 или более поздней без предварительной установки версии 1.1.30.0. Чтобы просмотреть доступные версии поставщика ресурсов и версию Azure Stack Hub, в которой они поддерживаются, см. список версий в разделе [Предварительные требования](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Чтобы обновить поставщик ресурсов, воспользуйтесь скриптом *UpdateSQLProvider.ps1*. Этот скрипт входит в состав скачиваемых ресурсов нового поставщика ресурсов SQL. Процесс обновления осуществляется так же, как и [развертывание поставщика ресурсов](./azure-stack-sql-resource-provider-deploy.md). Скрипт обновления использует те же аргументы, что и скрипт DeploySqlProvider.ps1, и вам потребуется предоставить сведения о сертификате.

 > [!IMPORTANT]
 > Перед обновлением поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

## <a name="update-script-processes"></a>Процессы скрипта обновления

Скрипт *UpdateSQLProvider.ps1* создает виртуальную машину с кодом последней версии поставщика ресурсов.

> [!NOTE]
> Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, **один** MSU-пакет можно поместить в локальный каталог зависимого элемента. Если поместить несколько MSU-файлов в этом расположении, выполнение скрипта завершится ошибкой.

Когда скрипт *UpdateSQLProvider.ps1* создаст виртуальную машину, он перенесет следующие параметры из старой виртуальной машины поставщика:

* сведения о базе данных;
* сведения о сервере размещения;
* необходимые записи DNS.

## <a name="update-script-parameters"></a>Обновление параметров сценария

Следующие параметры можно указать в командной строке при выполнении сценария PowerShell **UpdateSQLProvider.ps1**. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | Описание | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательно_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack Hub. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack Hub. | _Обязательно_ |
| **VMLocalCredential** | Учетные данные для локальной учетной записи администратора виртуальной машины поставщика ресурсов SQL. | _Обязательно_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательно_ |
| **AzureEnvironment** | Среда Azure учетной записи администратора службы, которая использовалась для развертывания Azure Stack Hub. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В этот каталог также нужно поместить PFX-файл сертификата. | _Необязательно для одного узла, но обязательно для нескольких узлов._ |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательно_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** |Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы. | нет |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | нет |

## <a name="update-script-powershell-example"></a>Пример скрипта обновления на PowerShell
> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам Azure Stack Hub.

При обновлении поставщика ресурсов SQL до версии 1.1.33.0 или предыдущих версий необходимо установить в PowerShell определенные версии модулей AzureRm.BootStrapper и Azure Stack Hub. При обновлении до версии поставщика ресурсов SQL 1.1.47.0 сценарий развертывания автоматически загрузит и установит необходимые модули PowerShell в пути C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> В отключенном сценарии необходимо скачать необходимые модули PowerShell и зарегистрировать репозиторий вручную в качестве необходимого компонента. Дополнительные сведения можно получить в разделе [Развертывание поставщика ресурсов SQL](azure-stack-sql-resource-provider-deploy.md)

Ниже приведен пример сценария *UpdateSQLProvider.ps1*, который можно запустить из консоли PowerShell с повышенными привилегиями. Не забудьте изменить данные переменной и пароли, если это требуется.  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert

 ```

После завершения скрипта обновления поставщика ресурсов закройте текущий сеанс PowerShell.

## <a name="next-steps"></a>Дальнейшие действия

[Обслуживание поставщика ресурсов SQL](azure-stack-sql-resource-provider-maintain.md)
