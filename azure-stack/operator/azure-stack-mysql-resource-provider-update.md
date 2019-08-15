---
title: Обновление поставщика ресурсов MySQL в Azure Stack | Документы Майкрософт
description: Сведения о том, как обновить поставщика ресурсов MySQL в Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 533a56e9dcfe4b3fbdc857a042a3e90d91d8090c
ms.sourcegitcommit: 94669fe8a55fadd3103e80be307e9e8c823bf746
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68940254"
---
# <a name="update-the-mysql-resource-provider"></a>Обновление поставщика ресурсов MySQL 

*Область применения: интегрированные системы Azure Stack*.

При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов MySQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки. 

Начиная с выпуска поставщика ресурсов MySQL версии 1.1.33.0, обновления являются накопительными и не обязательно должны устанавливаться в порядке, в котором они были выпущены (до тех пор, пока вы начинаете с версии 1.1.24.0 или более поздней). Например, если вы работаете с версией 1.1.24.0 поставщика ресурсов MySQL, вы можете выполнить обновление до версии 1.1.33.0 или более поздней без предварительной установки версии 1.1.30.0. Чтобы просмотреть доступные версии поставщика ресурсов и версию Azure Stack, в которой они поддерживаются, см. список версий в разделе [Предварительные требования](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием **UpdateMySQLProvider.ps1**. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в этой статье в разделе "Развертывание поставщика ресурсов". Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов. 

 > [!IMPORTANT]
 > Перед обновлением поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

## <a name="update-script-processes"></a>Процессы скрипта обновления

Сценарий **UpdateMySQLProvider.ps1** создает виртуальную машину с кодом последней версии поставщика ресурсов и переносит параметры из старой виртуальной машины на новую. Переносятся данные о базе данных и сервере размещения, а также необходимая запись DNS. 

>[!NOTE]
>Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, **один** MSU-пакет можно поместить в локальный каталог зависимого элемента. Если поместить несколько MSU-файлов в этом расположении, выполнение скрипта завершится ошибкой.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeployMySqlProvider.ps1. Также укажите сертификат.  


## <a name="update-script-parameters"></a>Обновление параметров сценария 
Следующие параметры можно указать в командной строке при выполнении сценария PowerShell **UpdateMySQLProvider.ps1**. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры. 

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию | 
| --- | --- | --- | 
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ | 
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ | 
| **VMLocalCredential** |Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ | 
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ | 
| **AzureEnvironment** | Среда Azure службы учетной записи администратора, которая использовалась для развертывания Azure Stack. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). | 
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ | 
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 | 
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 | 
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет | 
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет | 
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Обновление примера сценария
Ниже приведен пример сценария *UpdateMySQLProvider.ps1*, который можно запустить из консоли PowerShell с повышенными привилегиями. Не забудьте изменить данные переменной и пароли, если это требуется.  

> [!NOTE] 
> Этот процесс обновления применим только к интегрированным системам. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
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

## <a name="next-steps"></a>Дополнительная информация
[Обслуживание поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-maintain.md)
