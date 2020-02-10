---
title: Проверка удостоверения Azure
titleSuffix: Azure Stack Hub
description: Проверки удостоверений Azure с помощью средства проверки готовности Azure Stack Hub.
author: ihenkel
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 5c20e951e2a34c800611c8417d3b8504ed84c346
ms.sourcegitcommit: 5f53810d3c5917a3a7b816bffd1729a1c6b16d7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972472"
---
# <a name="validate-azure-identity"></a>Проверка удостоверения Azure

Средство проверки готовности Azure Stack Hub (**AzsReadinessChecker**) позволяет убедиться, что ваша служба Azure Active Directory (Azure AD) готова к работе с Azure Stack Hub. Прежде чем развертывать Azure Stack Hub, проверьте решение для работы с удостоверениями Azure.  

Средство проверки готовности позволяет определить:

- является ли Azure AD поставщиком удостоверений для Azure Stack Hub;
- предоставляет ли учетная запись Azure AD, которую вы хотите использовать, права глобального администратора в Azure AD.

Проверка гарантирует, что настроенное окружение обеспечивает все необходимое для того, чтобы инфраструктура Azure Stack Hub сохраняла в Azure AD сведения о пользователях, приложениях, группах и субъектах-службах.

## <a name="get-the-readiness-checker-tool"></a>Получение средства проверки готовности

Скачайте последнюю версию средства проверки готовности Azure Stack Hub (AzsReadinessChecker) из [коллекции PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены необходимые компоненты.

**Компьютер, на котором запускается это средство:**

- Необходимо установить Windows 10 или Windows Server 2016 и обеспечить подключение к Интернету.
- Необходимо установить PowerShell 5.1 или более поздней версии. Чтобы проверить используемую версию, выполните приведенную ниже команду PowerShell и проверьте значения **Major** (основной номер версии) и **Minor** (дополнительный номер версии).  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [Среда PowerShell, настроенная для Azure Stack Hub](azure-stack-powershell-install.md).
- Необходимо установить последнюю версию средства [проверки готовности Microsoft Azure Stack Hub](https://aka.ms/AzsReadinessChecker).

**Среда Azure AD.**

- Определите учетную запись Azure AD, которую вы хотите использовать для Azure Stack Hub, и убедитесь, что она предоставляет права глобального администратора Azure AD.
- Определите имя клиента Azure AD. Это имя должно совпадать с основным доменным именем в Azure AD. Например, **contoso.onmicrosoft.com**.
- Укажите среду Azure, которая будет использоваться. Поддерживаемые значения для параметра имени среды: **AzureCloud**, **AzureChinaCloud** или **AzureUSGovernment** (в зависимости от используемой подписки Azure).

## <a name="steps-to-validate-azure-identity"></a>Действия по проверке удостоверения Azure

1. На компьютере, который соответствует всем предварительным требованиям, откройте командную строку PowerShell с повышенными привилегиями и выполните следующую команду, чтобы установить **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. В командной строке PowerShell выполните следующую команду, чтобы назначить `$serviceAdminCredential` администратором службы для клиента Azure AD.  Замените `serviceadmin\@contoso.onmicrosoft.com` именами учетной записи и клиента:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. В командной строке PowerShell выполните приведенную ниже команду, чтобы начать проверку Azure AD.

   - Укажите значение имени среды в параметре **AzureEnvironment**. Поддерживаемые значения для параметра имени среды: **AzureCloud**, **AzureChinaCloud** или **AzureUSGovernment** (в зависимости от используемой подписки Azure).
   - Замените `contoso.onmicrosoft.com` именем своего клиента Azure AD.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Когда средство завершит работу, просмотрите выходные данные. Убедитесь, что система соответствует требованиям для установки (**OK**). При успешном завершении проверки отобразится следующий результат:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Файлы отчета и журнала

При каждом запуске проверки все результаты сохраняются в файлах **AzsReadinessChecker.log** и **AzsReadinessCheckerReport.json**. Расположение этих файлов указывается в PowerShell вместе с результатами проверки.

Эти файлы помогут передать сведения о состоянии проверки другим заинтересованным лицам перед развертыванием Azure Stack Hub или для исследования проблем, обнаруженных при проверке. В обоих файлах сохраняются результаты каждой очередной проверки. В отчете содержатся подтверждения команды развертывания по конфигурации удостоверений. Файл журнала поможет командам развертывания или поддержки диагностировать проблемы с проверкой.

По умолчанию оба файла записываются в `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Используйте параметр `-OutputPath <path>` в конце командной строки, чтобы задать другое расположение для отчетов.
- Используйте параметр `-CleanReport` в конце команды, чтобы удалить из файла **AzsReadinessCheckerReport.json** сведения о предыдущих запусках средства.

Дополнительные сведения об отчетах проверки Azure Stack Hub можно найти [здесь](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Ошибки при проверке

Если проверка завершается ошибкой, сведения о сбое отображаются в окне PowerShell. Кроме того, сведения записываются в файл AzsReadinessChecker.log.

В следующих примерах приведены некоторые рекомендации по устранению распространенных ошибок проверки.

### <a name="expired-or-temporary-password"></a>Пароль с истекшим сроком действия или временный пароль

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Причина.** Вход с помощью этой учетной записи невозможен, так как истек срок действия пароля или используется временный пароль.

**Решение.** Выполните в PowerShell следующую команду и следуйте инструкциям на экране, чтобы сбросить пароль.

```powershell
Login-AzureRMAccount
```

Также вы можете войти на [портал Azure](https://portal.azure.com) как владелец учетной записи. В таком случае пользователь должен будет сменить пароль.

### <a name="unknown-user-type"></a>Неизвестный тип пользователя 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Причина.** Вход в указанный каталог Azure AD (**AADDirectoryTenantName**) с помощью этой учетной записи невозможен. В нашем примере параметр **AzureChinaCloud** имеет значение **AzureEnvironment**.

**Решение.** Убедитесь, что эта учетная запись существует в указанной среде Azure. Выполните в PowerShell следующую команду, чтобы проверить допустимость учетной записи для указанной среды:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Учетная запись не предоставляет права администратора

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Причина.** Учетная запись позволяет успешно войти в систему, но не предоставляет права администратора в Azure AD (**AADDirectoryTenantName**).  

**Решение**. Войдите на [портал Azure](https://portal.azure.com) от имени владельца учетной записи и щелкните **Azure Active Directory**, **Пользователи**, **Выбрать пользователя**. Затем выберите **Роль каталога** и убедитесь, что пользователь является **глобальным администратором**. Если учетная запись предоставляет права **пользователя**, щелкните **Azure Active Directory** > **Имена личных доменов** и убедитесь, что указанное в качестве значения **AADDirectoryTenantName** имя домена является основным доменным именем для каталога. В нашем примере это **contoso.onmicrosoft.com**.

В Azure Stack Hub требуется, чтобы доменное имя являлось основным.

## <a name="next-steps"></a>Next Steps

[Validate Azure registration](azure-stack-validate-registration.md) (Проверка регистрации в Azure)  
[Просмотр отчета о готовности](azure-stack-validation-report.md)  
[Планирование интеграции центра обработки данных для интегрированных систем Azure Stack Hub](azure-stack-datacenter-integration.md)  
