---
title: Использование баз данных MySQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных MySQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов MySQL Server.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 603a42fcd98872a59109a1c8f6806fba72e615e0
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71908998"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Развертывание поставщика ресурсов MySQL в Azure Stack

Используйте поставщик ресурсов MySQL Server, чтобы предоставлять доступ к базам данных MySQL в качестве службы Azure Stack. Поставщик ресурсов MySQL выполняется как служба в виртуальной машине ядра сервера Windows Server 2016.

> [!IMPORTANT]
> Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.

## <a name="prerequisites"></a>Предварительные требования

Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов MySQL Azure Stack. Чтобы обеспечить соответствие этим требованиям, выполните описанные в этой статье действия на компьютере, который имеет доступ к привилегированной конечной точке виртуальной машины.

* Если вы еще этого не сделали, [зарегистрируйте Azure Stack](./azure-stack-registration.md) в Azure, чтобы можно было загружать элементы Azure Marketplace.
* В системе, в которой будет запускаться эта установка, необходимо установить модули PowerShell для Azure и Azure Stack. В системе должен быть развернут образ Windows 10 или Windows Server 2016 с последней версией среды выполнения .NET. См. статью [Установка PowerShell для Azure Stack](./azure-stack-powershell-install.md).
* Добавьте необходимую виртуальную машину ядра Windows Server в Azure Stack Marketplace, загрузив образ **Windows Server 2016 Datacenter — ядро сервера**.

* Скачайте двоичный файл поставщика ресурсов MySQL и запустите файл для самостоятельного извлечения содержимого во временный каталог.

  >[!NOTE]
  >Чтобы развернуть поставщик MySQL в системе без доступа к Интернету, скопируйте файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) в локальный каталог. Укажите путь с помощью параметра **DependencyFilesLocalPath**.

* У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack.

  |Минимальная версия Azure Stack|Версия MySQL RP|
  |-----|-----|
  |Версия 1808 (1.1808.0.97)|[MySQL RP версии 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Версия 1808 (1.1808.0.97)|[MySQL RP версии 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Версия 1804 (1.0.180513.1)|[MySQL RP версии 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Убедитесь, что выполнены предварительные требования для интеграции центра обработки данных.

    |Предварительные требования|Справочные материалы|
    |-----|-----|
    |Условное перенаправление DNS настроено правильно.|[Интеграция центра обработки данных Azure Stack — DNS](azure-stack-integrate-dns.md)|
    |Открыты входящие порты для поставщиков ресурсов.|[Интеграция центра обработки данных Azure Stack. Публикация конечных точек](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Субъект сертификата PKI и SAN настроены правильно.|[Обязательные компоненты PKI для развертывания Azure Stack ](azure-stack-pki-certs.md#mandatory-certificates)[Необходимые компоненты сертификата PaaS для развертывания Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Сертификаты

_Только для интегрированных систем_. Укажите сертификат SQL PaaS PKI, описанный в разделе о необязательных сертификатах PaaS в статье [Требования к инфраструктуре открытых ключей (PKI) для развертывания Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Поместите PFX-файл в каталог, указанный параметром **DependencyFilesLocalPath**. Не предоставляйте сертификат для систем ASDK.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

Когда установите все необходимые компоненты, запустите скрипт **DeployMySqlProvider.ps1**, чтобы развернуть поставщик ресурсов MySQL. Сценарий DeployMySqlProvider.ps1 входит в состав файла установки поставщика ресурсов MySQL, скачанного для вашей версии Azure Stack.

 > [!IMPORTANT]
 > Перед развертыванием поставщика ресурсов просмотрите примечания к выпуску, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

Чтобы развернуть поставщик ресурсов MySQL, откройте новое окно PowerShell с повышенными привилегиями (не интегрированную среду сценариев PowerShell) и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов MySQL. Рекомендуем открыть новое окно PowerShell, чтобы избежать потенциальных проблем, вызванных уже загруженными модулями PowerShell.

Запустите скрипт **DeployMySqlProvider.ps1**, который выполняет следующие задачи:

* Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
* Публикует пакеты коллекции, что позволяет развертывать базы данных MySQL с помощью коллекции.
* Публикует пакет коллекции для развертывания серверов размещения.
* Развертывает виртуальную машину с помощью загруженного образа ядра Windows Server 2016, а затем устанавливает поставщик ресурсов MySQL.
* Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
* Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager для оператора.

> [!NOTE]
> Когда вы запустите развертывание поставщика ресурсов MySQL, будет создана группа ресурсов **system.local.mysqladapter**. На выполнение обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут.

### <a name="deploymysqlproviderps1-parameters"></a>Параметры DeployMySqlProvider.ps1

Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать требуемые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов MySQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **AzureEnvironment** | Среда Azure службы учетной записи администратора, которая использовалась для развертывания Azure Stack. Требуется только для развертываний Azure AD. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, в случае использования Azure AD для Китая, — **AzureChinaCloud**. | AzureCloud; |
| **DependencyFilesLocalPath** | В случае с интегрированными системами в этот каталог нужно поместить PFX-файл сертификата. Для отключенных сред скачайте в этот каталог файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). При необходимости можно скопировать сюда один пакет MSU Центра обновления Windows. | _Необязательно_ (_обязательно_ для интегрированных систем или отключенных сред) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет |
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов MySQL с помощью пользовательского сценария

Чтобы избежать конфигурации вручную при развертывании поставщика ресурсов, настройте следующий скрипт. Измените сведения и пароли учетной записи по умолчанию для развертывания Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
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

После выполнения скрипта установки поставщика ресурсов обновите браузер, чтобы отобразить последние изменения.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

1. Войдите на портал администрирования в качестве администратора служб.
2. Выбор **Группы ресурсов**
3. Выберите группу ресурсов **system.\<расположение\>.mysqladapter**.
4. На странице сводки в разделе обзора группы ресурсов не должно быть сообщений о сбоях развертывания.
5. В конце на портале администрирования выберите **Виртуальные машины**, чтобы убедиться, что виртуальная машина поставщика ресурсов MySQL успешно создана и запущена.

## <a name="next-steps"></a>Дополнительная информация

[Добавление серверов размещения](azure-stack-mysql-resource-provider-hosting-servers.md)
