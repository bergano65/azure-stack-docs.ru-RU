---
title: Обновление поставщика ресурсов MySQL в Azure Stack Hub
description: Узнайте, как обновить поставщика ресурсов MySQL в Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 7bd1e47c87d0d746f862f64284eb1c1f915c883f
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492042"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Обновление поставщика ресурсов MySQL в Azure Stack Hub

При обновлении сборки Azure Stack Hub может быть выпущен новый адаптер поставщика ресурсов MySQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки.

Начиная с выпуска поставщика ресурсов MySQL версии 1.1.33.0, обновления являются накопительными и не обязательно должны устанавливаться в порядке, в котором они были выпущены (если вы начинаете с версии 1.1.24.0 или более поздней). Например, если вы работаете с версией 1.1.24.0 поставщика ресурсов MySQL, вы можете выполнить обновление до версии 1.1.33.0 или более поздней без предварительной установки версии 1.1.30.0. Чтобы просмотреть доступные версии поставщика ресурсов и версию Azure Stack Hub, в которой они поддерживаются, см. список версий в разделе [Предварительные требования](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием **UpdateMySQLProvider.ps1**. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в этой статье в разделе "Развертывание поставщика ресурсов". Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов. 

 > [!IMPORTANT]
 > Перед обновлением поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

## <a name="update-script-processes"></a>Процессы скрипта обновления

Сценарий **UpdateMySQLProvider.ps1** создает новую виртуальную машину (ВМ) с последним кодом поставщика ресурсов и переносит параметры из старой ВМ в новую ВМ. Переносимые параметры включают в себя информацию о базе данных и сервере размещения, а также необходимую запись DNS.

>[!NOTE]
>Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, **один** MSU-пакет можно поместить в локальный каталог зависимого элемента. Если поместить несколько MSU-файлов в этом расположении, выполнение скрипта завершится ошибкой.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeployMySqlProvider.ps1. Также укажите сертификат.  


## <a name="update-script-parameters"></a>Обновление параметров сценария 
Укажите следующие параметры из командной строки при запуске сценария PowerShell **UpdateMySQLProvider.ps1**. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | Описание | Комментарий или значение по умолчанию | 
| --- | --- | --- | 
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательно_ | 
| **AzCredential** | Учетные данные администратора службы Azure Stack Hub. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack Hub. | _Обязательно_ | 
| **VMLocalCredential** |Учетные данные для локальной учетной записи администратора виртуальной машины поставщика ресурсов SQL. | _Обязательно_ | 
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательно_ | 
| **AzureEnvironment** | Среда Azure учетной записи администратора службы, которая использовалась для развертывания Azure Stack Hub. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). | 
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательно_ | 
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 | 
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 | 
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | нет | 
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | нет | 
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Обновление примера сценария

> [!NOTE] 
> Этот процесс обновления применим только к интегрированным системам.

При обновлении поставщика ресурсов MySQL до версии 1.1.33.0 или предыдущих версий необходимо установить в PowerShell определенные версии модулей AzureRm.BootStrapper и Azure Stack Hub. При обновлении до версии поставщика ресурсов MySQL 1.1.47.0 сценарий развертывания автоматически загрузит и установит необходимые модули PowerShell для пути C:\Program Files\SqlMySqlPsh.

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> В отключенном сценарии необходимо скачать необходимые модули PowerShell и зарегистрировать репозиторий вручную в качестве необходимого компонента. Дополнительные сведения можно получить в разделе [Развертывание поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-deploy.md)

В следующем примере показан сценарий *UpdateMySQLProvider.ps1*, который можно запустить из консоли PowerShell с повышенными привилегиями. Не забудьте изменить данные переменной и пароли, если это требуется.

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
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
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

После завершения скрипта обновления поставщика ресурсов закройте текущий сеанс PowerShell.

## <a name="next-steps"></a>Дальнейшие действия
[Обслуживание поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-maintain.md)
