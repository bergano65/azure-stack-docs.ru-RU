---
title: Справочник по командлету Start-AzsReadinessChecker
description: Справка по командлету PowerShell для модуля средства проверки готовности Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 067164db905e6649def7ecd06b0e15d166d286b9
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509387"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Справочник по командлету Start-AzsReadinessChecker

Модуль: **Microsoft.AzureStack.ReadinessChecker**

Этот модуль содержит только один командлет. Он выполняет одну или несколько функций перед развертыванием или обслуживанием Azure Stack Hub.

## <a name="syntax"></a>Синтаксис

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Описание

Командлет **Start-AzsReadinessChecker** проверяет сертификаты, учетные записи Azure, подписки Azure и Azure Active Directory (AAD). Выполните проверку перед развертыванием Azure Stack Hub или перед такими действиями по обслуживанию Azure Stack Hub, как смена секрета. С помощью командлета также можно создавать запросы на подпись для сертификатов инфраструктуры и, при необходимости, сертификатов PaaS. Наконец, командлет может распаковать сертификаты PFX для устранения общих проблем с упаковкой.

## <a name="examples"></a>Примеры

### <a name="example-generate-certificate-signing-request"></a>Пример. Создание запроса на подпись сертификата

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

В этом примере с помощью `Start-AzsReadinessChecker` создается несколько запросов на подпись сертификатов (CSR), которые подходят для развертывания Azure Stack Hub ADFS с именем региона **east** и внешним полным доменным именем **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Пример. Проверка сертификатов

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

В этом примере в целях безопасности запрашивается пароль PFX, а затем `Start-AzsReadinessChecker` проверяет соответствующую папку **Certificates** на наличие допустимых сертификатов для развертывания AAD в регионе с именем **east** и внешним полным доменным именем **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Пример. Проверка сертификатов с помощью данных развертывания (развертывание и поддержка)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

В этом примере развертывания и поддержки в целях безопасности запрашивается пароль PFX, а затем `Start-AzsReadinessChecker` проверяется соответствующая папка **Certificates** на наличие допустимых сертификатов для развертывания. При этом идентификатор, название региона и внешнее имя FQDN считываются из JSON-файла с данными развертывания.

### <a name="example-validate-paas-certificates"></a>Пример. Проверка сертификатов PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

В этом примере хэш-таблица создается с путями и паролями для каждого сертификата PaaS. Сертификаты могут быть пропущены. `Start-AzsReadinessChecker` проверяет существование каждого PFX-пути для региона **east** и внешнего имени FQDN **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Пример. Проверка сертификатов PaaS с помощью данных развертывания

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

В этом примере хэш-таблица создается с путями и паролями для каждого сертификата PaaS. Сертификаты могут быть пропущены. `Start-AzsReadinessChecker` проверяет существование каждого PFX-пути для названия региона и внешнего имени FQDN, считываемых из JSON-файла с данными развертывания, созданного для развертывания.

### <a name="example-validate-azure-identity"></a>Пример. Проверка удостоверения Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

В этом примере в целях безопасности запрашиваются учетные данные учетной записи администратора службы, а затем `Start-AzsReadinessChecker` проверяет, допустимы ли учетная запись Azure и AAD для развертывания AAD с именем каталога клиента **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Пример. Проверка удостоверения Azure с помощью данных развертывания (развертывание и поддержка)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

В этом примере в целях безопасности запрашиваются учетные данные учетной записи администратора службы, а затем `Start-AzsReadinessChecker` проверяет, допустимы ли учетная запись Azure и AAD для развертывания AAD. При этом **AzureCloud** и **TenantName** считываются из JSON-файла с данными развертывания.

### <a name="example-validate-azure-registration"></a>Пример. Проверка регистрации в Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

В этом примере для обеспечения безопасности запрашиваются учетные данные владельца подписки, а затем `Start-AzsReadinessChecker` проверяет учетную запись и подписку, чтобы убедиться, что их можно использовать для регистрации в Azure Stack Hub.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Пример. Проверка регистрации Azure с помощью данных развертывания (группа развертывания)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

В этом примере для обеспечения безопасности запрашиваются учетные данные владельца подписки, а затем `Start-AzsReadinessChecker` проверяет учетную запись и подписку, чтобы убедиться, что их можно использовать для регистрации в Azure Stack Hub. При этом дополнительные сведения считываются из JSON-файла с данными о развертывании, созданного для развертывания.

### <a name="example-importexport-pfx-package"></a>Пример. Импорт и экспорт пакета PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

В этом примере в целях безопасности запрашивается PFX-пароль. Файл ssl.pfx импортируется в хранилище сертификатов на локальном компьютере, а затем повторно экспортируется с тем же паролем и сохраняется как ssl_new.pfx. Эта процедура используется, если в результате проверки сертификата обнаружено, что для закрытого ключа не установлен атрибут **Local Machine**, цепочка сертификата повреждена, в PFX есть несоответствующие сертификаты или цепочка сертификатов организована в неправильном порядке.

### <a name="example-view-validation-report-deployment-and-support"></a>Пример. Просмотр отчета о проверке (развертывание и поддержка)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

В этом примере группа развертывания или поддержки получает отчет о готовности от клиента (Contoso) и использует `Start-AzsReadinessChecker`, чтобы узнать состояние выполнения проверки для Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Пример. Просмотр сводки отчета о проверке только для подтверждения сертификата (развертывание и поддержка)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

В этом примере группа развертывания или поддержки получает отчет о готовности от клиента (Contoso) и использует `Start-AzsReadinessChecker`, чтобы просмотреть сводку по состоянию проверки сертификата для Contoso.

## <a name="required-parameters"></a>Необходимые параметры

### <a name="-regionname"></a>-RegionName

Определяет имя региона развертывания Azure Stack Hub

|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |None          |
|Принимает входные данные конвейера:      |False         |
|Принимает подстановочные знаки: |False         |

### <a name="-fqdn"></a>-FQDN

Определяет внешнее полное доменное имя для развертывания Azure Stack Hub, которое также имеет псевдонимы **ExternalFQDN** и **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |ExternalFQDN, ExternalDomainName |
|Принимает входные данные конвейера:      |False         |
|Принимает подстановочные знаки: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Указывает допустимые значения идентификационной системы развертывания Azure Stack Hub, AAD или ADFS для Azure Active Directory и федеративных служб Active Directory соответственно.

|  |  |
|----------------------------|--------------|
|Тип:                       |Строка        |
|Позиция:                   |именованная         |
|Значение по умолчанию:              |None          |
|Допустимые значения:               |'AAD','ADFS'  |
|Принимает входные данные конвейера:      |False         |
|Принимает подстановочные знаки: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Указывает пароль, связанный с PFX-файлами сертификатов.

|  |  |
|----------------------------|---------|
|Тип:                       |SecureString |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Указывает хэш-таблицу, содержащую пути и пароли для сертификатов PaaS.

|  |  |
|----------------------------|---------|
|Тип:                       |Хэш-таблицы |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Указывает JSON-файл конфигурации с данными о развертывании Azure Stack Hub. Этот файл создается для развертывания.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-pfxpath"></a>-PfxPath

Указывает путь к проблемному сертификату, которому для исправления требуется процедура импорта и экспорта, как определено в ходе проверки сертификатов в этом инструменте.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Указывает конечный путь для PFX-файла, полученного в результате процедуры импорта и экспорта.  

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-subject"></a>-Subject

Указывает упорядоченный словарь субъекта для создания запроса на сертификат.

|  |  |
|----------------------------|---------|
|Тип:                       |OrderedDictionary   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-requesttype"></a>-RequestType

Указывает тип сети хранения данных для запроса на сертификат. Допустимые значения: **MultipleCSR**, **SingleCSR**.

- **MultipleCSR**. Создает несколько запросов на сертификаты, по одному для каждой службы.
- **SingleCSR**. Создает один запрос на сертификат для всех служб.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Допустимые значения:               |'MultipleCSR','SingleCSR' |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Указывает путь назначения для файлов запроса на сертификат. Каталог уже должен существовать.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Указывает администратора службы Azure AD, который будет использоваться в развертывании Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Тип:                       |PSCredential   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Указывает имя службы Azure AD, которое будет использоваться в развертывании Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Указывает экземпляр служб Azure с учетными записями, каталогами и подписками для развертывания и регистрации Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Допустимые значения:               |AzureCloud, AzureChinaCloud, AzureGermanCloud |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Указывает учетную запись для регистрации в Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Указывает идентификатор подписки для регистрации в Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Тип:                       |Guid     |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |None     |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-reportpath"></a>-ReportPath

Указывает путь для отчета о готовности. По умолчанию используется текущий каталог и стандартное имя отчета.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |All      |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

## <a name="optional-parameters"></a>Необязательные параметры

### <a name="-certificatepath"></a>-CertificatePath

Указывает путь, по которому присутствуют только необходимые папки с сертификатами.

Необходимые папки для развертывания Azure Stack Hub с системой идентификации Azure AD:

- ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Необходимые папки для развертывания Azure Stack Hub с системой идентификации службы федерации Active Directory:

- ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |.\Certificates |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Указывает, нужно ли добавлять к запросам на сертификат службы PaaS или имена узлов.

|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |False             |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |

### <a name="-reportsections"></a>-ReportSections

Показывает только сводку отчета, опуская подробности.

|  |  |
|----------------------------|---------|
|Тип:                       |Строка   |
|Позиция:                   |именованная    |
|Значение по умолчанию:              |All      |
|Допустимые значения:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Принимает входные данные конвейера:      |False    |
|Принимает подстановочные знаки: |False    |

### <a name="-summary"></a>-Summary

Показывает только сводку отчета, опуская подробности.

|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |False             |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |

### <a name="-cleanreport"></a>-CleanReport

Удаляет журналы предыдущего выполнения и проверки и записывает проверки в новый отчет.

|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |CF                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |False             |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |

### <a name="-outputpath"></a>-OutputPath

Указывает пользовательский путь для сохранения отчета о готовности в формате JSON и подробного файла журнала. Если путь не существует, команда попытается создать каталог.

|  |  |
|----------------------------|------------------|
|Тип:                       |Строка            |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |$ENV:TEMP\AzsReadinessChecker  |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |

### <a name="-confirm"></a>-Confirm

Запрашивает подтверждение перед выполнением командлета.

|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |CF                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |False             |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |

### <a name="-whatif"></a>-WhatIf

Показывает, что произойдет при запуске командлета. Командлет не выполняется.

|  |  |
|----------------------------|------------------|
|Тип:                       |SwitchParameter   |
|Псевдонимы:                    |wi                |
|Позиция:                   |именованная             |
|Значение по умолчанию:              |False             |
|Принимает входные данные конвейера:      |False             |
|Принимает подстановочные знаки: |False             |
