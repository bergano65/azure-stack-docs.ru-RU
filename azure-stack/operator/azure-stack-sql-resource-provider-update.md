---
title: Обновление поставщика ресурсов SQL в Azure Stack
titleSuffix: Azure Stack
description: Узнайте, как обновить поставщик ресурсов SQL в Azure Stack.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 2669ed87e46307bb84aa9639d42b100cdc0cfd46
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954440"
---
# <a name="update-the-sql-resource-provider"></a>Обновление поставщика ресурсов SQL

*Область применения: интегрированные системы Azure Stack*.

При обновлении сборки Azure Stack может быть выпущен новый поставщик ресурсов SQL. Хотя существующий поставщик ресурсов может продолжать работать, мы рекомендуем как можно скорее обновить его до последней сборки.

Начиная с выпуска поставщика ресурсов SQL версии 1.1.33.0, обновления являются накопительными и не обязательно должны устанавливаться в порядке, в котором они были выпущены (если вы начинаете с версии 1.1.24.0 или более поздней). Например, если вы работаете с версией 1.1.24.0 поставщика ресурсов SQL, вы можете выполнить обновление до версии 1.1.33.0 или более поздней без предварительной установки версии 1.1.30.0. Чтобы просмотреть доступные версии поставщика ресурсов и версию Azure Stack, в которой они поддерживаются, см. список версий в разделе [Предварительные требования](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

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

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные для локальной учетной записи администратора виртуальной машины поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **AzureEnvironment** | Среда Azure службы учетной записи администратора, которая использовалась для развертывания Azure Stack. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В этот каталог также нужно поместить PFX-файл сертификата. | _Необязательно для одного узла, но обязательно для нескольких узлов._ |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** |Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы. | Нет |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет |

## <a name="update-script-powershell-example"></a>Пример скрипта обновления на PowerShell
> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам Azure Stack.

При обновлении поставщика ресурсов SQL до версии 1.1.33.0 или предыдущих версий необходимо установить в PowerShell определенные версии модулей AzureRm.BootStrapper и Azure Stack. Если вы обновляете поставщик ресурсов SQL до версии 1.1.47.0, этот шаг можно пропустить.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

Ниже приведен пример сценария *UpdateSQLProvider.ps1*, который можно запустить из консоли PowerShell с повышенными привилегиями. Не забудьте изменить данные переменной и пароли, если это требуется.  

```powershell
# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
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

## <a name="next-steps"></a>Дополнительная информация

[Обслуживание поставщика ресурсов SQL](azure-stack-sql-resource-provider-maintain.md)
