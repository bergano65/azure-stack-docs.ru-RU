---
title: Развертывание поставщика ресурсов SQL Server
titleSuffix: Azure Stack Hub
description: Сведения о развертывании поставщика ресурсов SQL Server в Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.openlocfilehash: 2faeb2c714099a3b055343445dc94a57879d2cd6
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491838"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack-hub"></a>Развертывание поставщика ресурсов SQL Server в Azure Stack Hub

Используйте поставщик ресурсов SQL Server Azure Stack Hub, чтобы предоставлять доступ к базам данных SQL в качестве службы Azure Stack Hub. Поставщик ресурсов SQL выполняется как служба в виртуальной машине ядра сервера Windows Server 2016.

> [!IMPORTANT]
> Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается Элементы, созданные на сервере узла, не созданные поставщиком ресурсов, могут привести к несоответствию состояния.

## <a name="prerequisites"></a>Предварительные требования

Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов SQL Azure Stack Hub. Чтобы обеспечить соответствие этим требованиям, выполните следующие действия на компьютере, который имеет доступ к привилегированной конечной точке виртуальной машины:

- Если вы еще этого не сделали, [зарегистрируйте Azure Stack Hub](azure-stack-registration.md) в Azure, чтобы скачивать элементы Azure Marketplace.

- Добавьте необходимую виртуальную машину Windows Server Core в Azure Stack Hub Marketplace, скачав образ **Windows Server 2016 Datacenter — основные серверные компоненты**.

- Загрузите двоичный файл поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack Hub.

  |Минимальная версия Azure Stack Hub|Версия SQL RP|
  |-----|-----|
  |Версия 1910 (1.1910.0.58)|[Поставщик ресурсов SQL версии 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |Версия 1808 (1.1808.0.97)|[Поставщик ресурсов SQL версии 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Версия 1808 (1.1808.0.97)|[SQL RP версии 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Версия 1804 (1.0.180513.1)|[SQL RP версии 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Прежде чем развертывать поставщик ресурсов SQL версии 1.1.47.0, необходимо обновить систему Azure Stack Hub до версии 1910 или более поздних. Версия поставщика ресурсов SQL 1.1.47.0 для предыдущих неподдерживаемых версий Azure Stack Hub не работает.

- Убедитесь, что выполнены предварительные требования для интеграции центра обработки данных.

    |Предварительные требования|Справочник|
    |-----|-----|
    |Условное перенаправление DNS настроено правильно.|[Интеграция DNS центра обработки данных Azure Stack Hub](azure-stack-integrate-dns.md)|
    |Открыты входящие порты для поставщиков ресурсов.|[Порты и протоколы (входящие)](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Субъект сертификата PKI и SAN настроены правильно.|[Обязательные сертификаты](azure-stack-pki-certs.md#mandatory-certificates)<br>[Необязательные сертификаты PaaS](azure-stack-pki-certs.md#optional-paas-certificates)|
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

## <a name="deploy-the-sql-resource-provider"></a>Развертывание поставщика ресурсов SQL

После установки всех необходимых компонентов запустите скрипт **DeploySqlProvider.ps1** на компьютере с доступом к конечной точке управления ресурсами Azure для администратора Azure Stack Hub и привилегированной конечной точке для развертывания поставщика ресурсов SQL. Скрипт DeploySqlProvider.ps1 входит в состав двоичного файла поставщика ресурсов SQL, загруженного для вашей версии Azure Stack Hub.

 > [!IMPORTANT]
 > Перед развертыванием поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

Чтобы развернуть поставщик ресурсов SQL, откройте **новое** окно PowerShell с повышенными привилегиями (не интегрированную среду сценариев PowerShell) и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов SQL. Рекомендуем открыть новое окно PowerShell, чтобы избежать потенциальных проблем, вызванных уже загруженными модулями PowerShell.

Запустите скрипт DeploySqlProvider.ps1, который выполняет следующие задачи:

- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack Hub.
- Публикует пакеты коллекции, что позволяет развертывать базы данных SQL с помощью коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину с помощью загруженного образа ядра Windows Server 2016, а затем устанавливает поставщик ресурсов SQL.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager для оператора.

> [!NOTE]
> Когда вы запустите развертывание поставщика ресурсов SQL, будет создана группа ресурсов **system.local.sqladapter**. На выполнение обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут. Не следует размещать другие ресурсы в группе ресурсов **system.local.sqladapter**.

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1

Следующие параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | Описание | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательно_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack Hub. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack Hub. | _Обязательно_ |
| **VMLocalCredential** | Учетные данные для локальной учетной записи администратора виртуальной машины поставщика ресурсов SQL. | _Обязательно_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательно_ |
| **AzureEnvironment** | Среда Azure учетной записи администратора службы, которая использовалась для развертывания Azure Stack Hub. Требуется только для развертываний Azure AD. Поддерживаемые имена среды: **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В случае с интегрированными системами в этот каталог нужно поместить PFX-файл сертификата. При необходимости можно скопировать сюда один пакет MSU Центра обновления Windows. | _Необязательный_ (_обязательно_ для интегрированных систем) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательно_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | нет |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | нет |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов SQL с помощью пользовательского сценария

При развертывании поставщика ресурсов SQL версии 1.1.33.0 или предыдущих версий необходимо установить в PowerShell определенные версии модулей AzureRm.BootStrapper и Azure Stack Hub. При развертывании версии поставщика ресурсов SQL 1.1.47.0 сценарий развертывания автоматически загрузит и установит необходимые модули PowerShell по пути C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> В отключенном сценарии необходимо скачать необходимые модули PowerShell и зарегистрировать репозиторий вручную в качестве необходимого компонента.

Чтобы избежать конфигурации вручную при развертывании поставщика ресурсов, настройте следующий скрипт. Измените сведения и пароли учетной записи по умолчанию для развертывания Azure Stack Hub.

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

После выполнения скрипта установки поставщика ресурсов обновите браузер, чтобы отобразить последние изменения, и закройте текущий сеанс PowerShell.

## <a name="verify-the-deployment-using-the-azure-stack-hub-portal"></a>Проверка развертывания с использованием портала Azure Stack Hub

Выполните следующие действия, чтобы проверить успешное развертывания поставщика ресурсов SQL.

1. Войдите на портал администрирования с правами администратора служб.
2. Выберите **Группы ресурсов**.
3. Выберите группу ресурсов **system.\<расположение\>.sqladapter**.
4. На странице сводки в разделе обзора группы ресурсов не должно быть сообщений о сбоях развертывания.

    ![Проверка развертывания поставщика ресурсов SQL на портале администрирования Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Наконец, выберите **Виртуальные машины** на портале администратора, чтобы убедиться, что виртуальная машина поставщика ресурсов SQL успешно создана и работает.

## <a name="next-steps"></a>Дальнейшие действия

[Добавление серверов размещения](azure-stack-sql-resource-provider-hosting-servers.md)
