---
title: Развертывание поставщика ресурсов MySQL в Azure Stack Hub | Документация Майкрософт
description: Узнайте, как развернуть адаптер поставщика ресурсов MySQL и базы данных MySQL в качестве службы в Azure Stack Hub.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: aecc96bc9e96c39ad1df1111b57bf17ca0d9b59a
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534963"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>Развертывание поставщика ресурсов MySQL в Azure Stack Hub

Используйте поставщик ресурсов MySQL Server, чтобы предоставлять доступ к базам данных MySQL в качестве службы Azure Stack Hub. Поставщик ресурсов MySQL выполняется как служба в виртуальной машине ядра сервера Windows Server 2016.

> [!IMPORTANT]
> Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается Элементы, созданные на сервере узла, не созданные поставщиком ресурсов, могут привести к несоответствию состояния.

## <a name="prerequisites"></a>Предварительные требования

Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов MySQL Azure Stack Hub. Чтобы обеспечить соответствие этим требованиям, выполните описанные в этой статье действия на компьютере, который имеет доступ к привилегированной конечной точке виртуальной машины.

* Если вы еще этого не сделали, [зарегистрируйте Azure Stack Hub](./azure-stack-registration.md) в Azure, чтобы скачивать элементы Azure Marketplace.

* Добавьте необходимую виртуальную машину Windows Server Core в Azure Stack Hub Marketplace, скачав образ **Windows Server 2016 Datacenter — основные серверные компоненты**.

* Скачайте двоичный файл поставщика ресурсов MySQL и запустите файл для самостоятельного извлечения содержимого во временный каталог.

  >[!NOTE]
  >Чтобы развернуть поставщик MySQL в системе без доступа к Интернету, скопируйте файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) в локальный путь. Укажите путь с помощью параметра **DependencyFilesLocalPath**.

* У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack Hub.

  |Минимальная версия Azure Stack Hub|Версия MySQL RP|
  |-----|-----|
  |Версия 1910 (1.1910.0.58)|[Поставщик ресурсов MySQL версии 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|
  |Версия 1808 (1.1808.0.97)|[Поставщик ресурсов MySQL версии 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Версия 1808 (1.1808.0.97)|[MySQL RP версии 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Версия 1804 (1.0.180513.1)|[MySQL RP версии 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |
  
> [!IMPORTANT]
> Прежде чем развертывать поставщик ресурсов MySQL версии 1.1.47.0, необходимо обновить систему Azure Stack Hub до версии 1910 или более поздней. Версия поставщика ресурсов MySQL 1.1.47.0 для предыдущих неподдерживаемых версий Azure Stack Hub не работает.

* Убедитесь, что выполнены предварительные требования для интеграции центра обработки данных.

    |Предварительные требования|Справочник|
    |-----|-----|
    |Условное перенаправление DNS настроено правильно.|[Интеграция DNS центра обработки данных Azure Stack Hub](azure-stack-integrate-dns.md)|
    |Открыты входящие порты для поставщиков ресурсов.|[Интеграция центра обработки данных Azure Stack Hub. Публикация конечных точек](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Субъект сертификата PKI и SAN настроены правильно.|[Обязательные компоненты PKI для развертывания Azure Stack Hub](azure-stack-pki-certs.md#mandatory-certificates)[Необходимые компоненты сертификата PaaS для развертывания Azure Stack Hub](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

В отключенном сценарии выполните описанные ниже действия, чтобы скачать необходимые модули PowerShell и зарегистрировать репозиторий вручную.

1. Войдите на компьютер, подключенный к Интернету, и с помощью приведенных ниже скриптов скачайте модули PowerShell.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. Затем скопируйте скачанные пакеты на USB-устройство.

3. Войдите на отключенную рабочую станцию и скопируйте пакеты с USB-устройства в нужное расположение на ней.

4. Зарегистрируйте это расположение в качестве локального репозитория.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory" 
```

### <a name="certificates"></a>Сертификаты

_Только для интегрированных систем_. Укажите сертификат SQL PaaS PKI, описанный в разделе о необязательных сертификатах PaaS в статье [Требования к сертификатам инфраструктуры открытых ключей Azure Stack Hub](./azure-stack-pki-certs.md#optional-paas-certificates). Поместите PFX-файл в каталог, указанный параметром **DependencyFilesLocalPath**. Не предоставляйте сертификат для систем ASDK.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

Когда установите все необходимые компоненты, запустите скрипт **DeployMySqlProvider.ps1**, чтобы развернуть поставщик ресурсов MySQL. Скрипт DeployMySqlProvider.ps1 входит в состав файла установки поставщика ресурсов MySQL, скачанного для вашей версии Azure Stack Hub.

 > [!IMPORTANT]
 > Перед развертыванием поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

Чтобы развернуть поставщик ресурсов MySQL, откройте новое окно PowerShell с повышенными привилегиями (не интегрированную среду сценариев PowerShell) и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов MySQL. Рекомендуем открыть новое окно PowerShell, чтобы избежать потенциальных проблем, вызванных уже загруженными модулями PowerShell.

Запустите скрипт **DeployMySqlProvider.ps1**, который выполняет следующие задачи:

* Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack Hub.
* Публикует пакеты коллекции, что позволяет развертывать базы данных MySQL с помощью коллекции.
* Публикует пакет коллекции для развертывания серверов размещения.
* Развертывает виртуальную машину с помощью загруженного образа ядра Windows Server 2016, а затем устанавливает поставщик ресурсов MySQL.
* Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
* Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager для оператора.

> [!NOTE]
> Когда вы запустите развертывание поставщика ресурсов MySQL, будет создана группа ресурсов **system.local.mysqladapter**. На выполнение обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут.

### <a name="deploymysqlproviderps1-parameters"></a>Параметры DeployMySqlProvider.ps1

Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | Описание | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательно_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack Hub. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack Hub. | _Обязательно_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов MySQL. | _Обязательно_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательно_ |
| **AzureEnvironment** | Среда Azure учетной записи администратора службы, которая использовалась для развертывания Azure Stack Hub. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В случае с интегрированными системами в этот каталог нужно поместить PFX-файл сертификата. Для отключенных сред скачайте в этот каталог файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). При необходимости можно скопировать сюда один пакет MSU Центра обновления Windows. | _Необязательно_ (_обязательно_ для интегрированных систем или отключенных сред) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательно_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | нет |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | нет |
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов MySQL с помощью пользовательского сценария

При развертывании поставщика ресурсов MySQL версии 1.1.33.0 или предыдущих необходимо установить в PowerShell определенные версии модулей AzureRm.BootStrapper и Azure Stack Hub. При развертывании версии поставщика ресурсов MySQL 1.1.47.0 скрипт развертывания автоматически загрузит и установит необходимые модули PowerShell для пути C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> В отключенном сценарии необходимо скачать необходимые модули PowerShell и зарегистрировать репозиторий вручную в качестве необходимого компонента.

Чтобы избежать конфигурации вручную при развертывании поставщика ресурсов, настройте следующий скрипт. Измените сведения и пароли учетной записи по умолчанию для развертывания Azure Stack Hub.

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

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

После выполнения скрипта установки поставщика ресурсов обновите браузер, чтобы отобразить последние изменения, и закройте текущий сеанс PowerShell.

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>Проверка развертывания с использованием портала Azure Stack Hub

1. Войдите на портал администрирования с правами администратора служб.
2. Выберите **Группы ресурсов**.
3. Выберите группу ресурсов **system.\<расположение\>.mysqladapter**.
4. На странице сводки в разделе обзора группы ресурсов не должно быть сообщений о сбоях развертывания.
5. Наконец, выберите **Виртуальные машины** на портале администратора, чтобы убедиться, что виртуальная машина поставщика ресурсов MySQL успешно создана и работает.

## <a name="next-steps"></a>Дальнейшие действия

[Добавление серверов размещения](azure-stack-mysql-resource-provider-hosting-servers.md)
