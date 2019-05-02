---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.lastreviewed: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: ee64106c97a07e1b3ceb84c4ca932b19bc6d83b8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307616"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Развертывание поставщика ресурсов SQL Server в Azure Stack

Используйте поставщик ресурсов SQL Server Azure Stack, чтобы предоставлять доступ к базам данных SQL в качестве службы Azure Stack. Поставщик ресурсов SQL выполняется как служба в виртуальной машине ядра сервера Windows Server 2016.

> [!IMPORTANT]
> Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.

## <a name="prerequisites"></a>Предварительные требования

Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов SQL Azure Stack. Чтобы обеспечить соответствие этим требованиям, выполните следующие действия на компьютере, который имеет доступ к привилегированной конечной точке виртуальной машины:

- Если вы еще этого не сделали, [зарегистрируйте Azure Stack](azure-stack-registration.md) в Azure, чтобы можно было загружать элементы Azure Marketplace.
- В системе, в которой будет запускаться эта установка, необходимо установить модули PowerShell для Azure и Azure Stack. В системе должен быть развернут образ Windows 10 или Windows Server 2016 с последней версией среды выполнения .NET. См. статью [Установка PowerShell для Azure Stack](./azure-stack-powershell-install.md).
- Добавьте необходимую виртуальную машину ядра Windows Server в Azure Stack Marketplace, загрузив образ **Windows Server 2016 Datacenter — ядро сервера**.
- Загрузите двоичный файл поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack.

  |Минимальная версия Azure Stack|Версия SQL RP|
  |-----|-----|
  |Версия 1808 (1.1808.0.97)|[Поставщик ресурсов SQL версии 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Версия 1808 (1.1808.0.97)|[SQL RP версии 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |Версия 1804 (1.0.180513.1)|[SQL RP версии 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

- Убедитесь, что выполнены предварительные требования для интеграции центра обработки данных.

    |Предварительные требования|Справочные материалы|
    |-----|-----|
    |Условное перенаправление DNS настроено правильно.|[Интеграция центра обработки данных Azure Stack — DNS](azure-stack-integrate-dns.md)|
    |Открыты входящие порты для поставщиков ресурсов.|[Интеграция центра обработки данных Azure Stack. Публикация конечных точек](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Субъект сертификата PKI и SAN настроены правильно.|[Обязательные компоненты PKI для развертывания Azure Stack ](azure-stack-pki-certs.md#mandatory-certificates)[Необходимые компоненты сертификата PaaS для развертывания Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Сертификаты

_Только для интегрированных систем_. Укажите сертификат SQL PaaS PKI, описанный в разделе о необязательных сертификатах PaaS в статье [Требования к инфраструктуре открытых ключей (PKI) для развертывания Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Поместите PFX-файл в каталог, указанный параметром **DependencyFilesLocalPath**. Не предоставляйте сертификат для систем ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Развертывание поставщика ресурсов SQL

После установки всех необходимых компонентов запустите скрипт **DeploySqlProvider.ps1**, чтобы развернуть поставщик ресурсов SQL. Сценарий DeploySqlProvider.ps1 входит в состав двоичного файла поставщика ресурсов SQL, загруженного для вашей версии Azure Stack.

 > [!IMPORTANT]
 > Перед развертыванием поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.
 
Чтобы развернуть поставщик ресурсов SQL, откройте **новое** окно PowerShell с повышенными привилегиями (не интегрированную среду сценариев PowerShell) и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов SQL. Рекомендуем открыть новое окно PowerShell, чтобы избежать потенциальных проблем, вызванных уже загруженными модулями PowerShell.

Запустите скрипт DeploySqlProvider.ps1, который выполняет следующие задачи:

- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
- Публикует пакеты коллекции, что позволяет развертывать базы данных SQL с помощью коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину с помощью загруженного образа ядра Windows Server 2016, а затем устанавливает поставщик ресурсов SQL.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager для оператора.

> [!NOTE]
> Когда вы запустите развертывание поставщика ресурсов SQL, будет создана группа ресурсов **system.local.sqladapter**. На выполнение обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут.

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1

Следующие параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **AzureEnvironment** | Среда Azure службы учетной записи администратора, которая использовалась для развертывания Azure Stack. Требуется только для развертываний Azure AD. Поддерживаемые имена среды: **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В случае с интегрированными системами в этот каталог нужно поместить PFX-файл сертификата. При необходимости можно скопировать сюда один пакет MSU Центра обновления Windows. | _Необязательный_ (_обязательно_ для интегрированных систем) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Число повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов SQL с помощью пользовательского сценария

Чтобы избежать конфигурации вручную при развертывании поставщика ресурсов, настройте следующий скрипт.  

Измените сведения и пароли учетной записи по умолчанию для развертывания Azure Stack.


```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

После выполнения скрипта установки поставщика ресурсов обновите браузер, чтобы отобразить последние изменения.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

Выполните следующие действия, чтобы проверить успешное развертывания поставщика ресурсов SQL.

1. Войдите на портал администрирования в качестве администратора служб.
2. Выберите **Группы ресурсов**.
3. Выберите группу ресурсов **system.\<расположение\>.sqladapter**.
4. На странице сводки в разделе обзора группы ресурсов не должно быть сообщений о сбоях развертывания.
      ![Проверка развертывания поставщика ресурсов SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. В конце на портале администрирования выберите **Виртуальные машины**, чтобы убедиться, что виртуальная машина поставщика ресурсов SQL успешно создана и запущена.

## <a name="next-steps"></a>Дополнительная информация

[Добавление серверов размещения](azure-stack-sql-resource-provider-hosting-servers.md)
