---
title: Регистрация Azure Stack Hub в Azure
titleSuffix: Azure Stack Hub
description: Узнайте, как регистрировать интегрированные системы Azure Stack Hub в Azure, чтобы скачивать элементы Azure Marketplace и настраивать отчеты по данным.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 115c3180044b7163887cc388d291e298f7c33bbe
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535796"
---
# <a name="register-azure-stack-hub-with-azure"></a>Регистрация Azure Stack Hub в Azure

Зарегистрируйте Azure Stack Hub в Azure, чтобы скачивать элементы Azure Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. После регистрации Azure Stack Hub данные об использовании отсылаются в Azure Commerce и их можно просматривать в области под идентификатором подписки для выставления счетов, указанной при регистрации.

В этой статье описывается, как зарегистрировать интегрированные системы Azure Stack Hub в Azure. См. сведения о том, как [зарегистрировать Azure Stack Hub](../asdk/asdk-register.md) в документации по ASDK.

> [!IMPORTANT]  
> Регистрация обеспечивает поддержку всех функций Azure Stack Hub, включая предложение элементов в Marketplace. Отсутствие регистрации при использовании модели выставления счетов с оплатой по мере использования считается нарушением условий лицензии на Azure Stack Hub. См. сведения о моделях лицензирования Azure Stack Hub в руководстве по [покупке Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Предварительные требования

Перед регистрацией необходимо выполнить следующие предварительные требования:

- проверить учетные данные;
- установить языковой режим PowerShell;
- установить PowerShell для Azure Stack Hub;
- скачать средства Azure Stack Hub;
- определить свой сценарий регистрации.

### <a name="verify-your-credentials"></a>Проверка учетных данных

Для регистрации Azure Stack Hub в Azure необходимо следующее.

- Идентификатор подписки Azure. При регистрации поддерживаются только подписки на такие общие службы, как EA, CSP или CSPSS. Поставщикам служб шифрования необходимо решить, какую подписку использовать:[CSP или APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Чтобы получить идентификатор, войдите в Azure и щелкните **Все службы**. Затем в категории **Общие** выберите **Подписки**, щелкните нужную подписку и найдите идентификатор подписки в разделе **Основные компоненты**. Для рабочей среды, сред разработки и тестирования рекомендуется использовать разные подписки. 

  > [!Note]  
  > Облачные подписки для Германии в настоящее время не поддерживаются.

- Имя пользователя и пароль учетной записи владельца подписки.

- Учетная запись пользователя должна обладать правами доступа к подписке Azure и разрешением на создание приложений удостоверений и субъектов-служб в каталоге, который с ней связан. Мы рекомендуем при регистрации Azure Stack Hub в Azure предоставлять минимальные административные привилегии. См. сведения о том, как [создать пользовательское определение роли для Azure Stack Hub, ограничивающее доступ к вашей подписке для регистрации](azure-stack-registration-role.md).

- Зарегистрированный поставщик ресурсов Azure Stack Hub (см. сведения в следующем разделе, посвященном регистрации поставщика ресурсов Azure Stack Hub).

После регистрации разрешение глобального администратора Azure Active Directory (Azure AD) не требуется. Однако для некоторых операций могут потребоваться учетные данные глобального администратора (например, сценарий установщика поставщика ресурсов или новая функция, требующая предоставления разрешения). Можно временно восстановить разрешения глобального администратора или использовать отдельную глобальную учетную запись администратора, которая является владельцем *подписки поставщика по умолчанию*.

Пользователь, регистрирующий Azure Stack Hub, является владельцем субъекта-службы в Azure AD. Только пользователь, зарегистрировавший Azure Stack Hub, может изменить регистрацию этой службы. Если пользователь без прав администратора, который не является владельцем службы регистрации, пытается зарегистрировать или изменить регистрацию Azure Stack Hub, может быть получен ответ с ошибкой 403. Он указывает, что у пользователя недостаточно разрешений для выполнения операции.

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack Hub в подписке Azure дополнительная плата не взимается.

> [!NOTE]
> Если у вас есть несколько экземпляров Azure Stack Hub, мы рекомендуем зарегистрировать каждый экземпляр в отдельную подписку. Это позволяет упростить отслеживание использования.

### <a name="powershell-language-mode"></a>Языковой режим PowerShell

Чтобы успешно зарегистрировать Azure Stack Hub, необходимо установить для языкового режима PowerShell значение **FullLanguageMode**.  Чтобы проверить, что текущий языковой режим имеет значение full, откройте окно PowerShell с повышенным уровнем разрешений и выполните следующие командлеты PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Убедитесь, что в выходных данных возвращено значение **FullLanguageMode**. Если вернулось другое значение языкового режима, необходимо выполнить регистрацию на другом компьютере или присвоить языковому режиму значение **FullLanguageMode** и продолжить регистрацию.

### <a name="install-powershell-for-azure-stack-hub"></a>Установка PowerShell для Azure Stack Hub

Чтобы зарегистрировать систему в Azure, требуется последняя версия PowerShell для Azure Stack Hub.

Если последняя версия еще не установлена, см. инструкции по [установке PowerShell для Azure Stack Hub](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-hub-tools"></a>Скачивание средств Azure Stack Hub

Репозиторий GitHub со средствами Azure Stack Hub содержит модули PowerShell, которые поддерживают функции Azure Stack Hub, в том числе функции регистрации. Для регистрации экземпляра Azure Stack Hub в Azure вам нужно импортировать и использовать модуль PowerShell **RegisterWithAzure.psm1**, который можно найти в репозитории средств Azure Stack Hub.

Перед регистрацией в Azure удалите все существующие версии средств Azure Stack Hub и [скачайте последнюю версию с сайта GitHub](azure-stack-powershell-download.md).

### <a name="determine-your-registration-scenario"></a>Определение своего сценария регистрации.

Развертывание Azure Stack Hub может выполняться в режиме *подключения* или *автономном* режиме.

- **Подключен**  
 Режим подключения означает, что развернутый экземпляр Azure Stack Hub может подключаться к Интернету и к Azure. Для хранилища удостоверений используется Azure AD или службы федерации Active Directory (AD FS). Для подключенных развертываний можно выбрать из двух моделей выставления счетов: оплата по мере использования или на основе емкости.
  - [Регистрация подключенного экземпляра Azure Stack Hub в Azure с использованием модели выставления счетов **с оплатой по мере использования**](#register-connected-with-pay-as-you-go-billing).
  - [Регистрация подключенного Azure Stack Hub в Azure с использованием модели выставления счетов **с оплатой на основе емкости**](#register-connected-with-capacity-billing).

- **Отключена**  
 Вариант автономного развертывания позволяет развернуть и использовать Azure Stack Hub без подключения к Интернету. Но при этом вы будете ограничены хранилищем удостоверений службы федерации Active Directory (AD FS) и моделью выставления счетов на основе емкости.
  - [Регистрация автономного экземпляра Azure Stack Hub с использованием модели выставления счетов **с оплатой на основе емкости**](#register-disconnected-with-capacity-billing).

### <a name="determine-a-unique-registration-name-to-use"></a>Определение необходимого уникального регистрационного имени

При регистрации Azure Stack Hub в Azure необходимо указать уникальное имя регистрации. Подписку Azure Stack Hub можно связать с регистрацией Azure с помощью идентификатора **CloudID** Azure Stack Hub.

> [!NOTE]
> Если для регистрации Azure Stack Hub используется модель выставления счетов на основе емкости, при повторной регистрации по истечении срока действия годовой подписки потребуется изменить уникальное имя, если только вы не [удалите истекшую регистрацию](azure-stack-registration.md#change-the-subscription-you-use) и повторно не зарегистрируетесь в Azure.

Чтобы определить идентификатор облака при развертывании Azure Stack Hub, откройте сеанс PowerShell от имени администратора на компьютере, с которого можно получить доступ к привилегированной конечной точке, выполните следующие команды и запишите значение **CloudID**.

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Регистрация подключенного развертывания с оплатой по мере использования

Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack Hub в Azure с моделью выставления счетов с оплатой по мере использования.

> [!Note]  
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке (PEP). См. сведения о PEP в руководстве по [использованию привилегированной конечной точки в Azure Stack Hub](azure-stack-privileged-endpoint.md).

Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack Hub, а затем настроить модель выставления счетов.

1. Чтобы зарегистрировать поставщик ресурсов Azure Stack Hub в Azure, запустите интегрированную среду скриптом PowerShell и выполните следующие командлеты PowerShell с параметром **EnvironmentName**, заданным для соответствующего типа подписки Azure (см. параметры ниже).

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack Hub. Чтобы добавить учетную запись, выполните командлет **Add-AzureRmAccount**. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную проверку подлинности в зависимости от конфигурации вашей учетной записи.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Параметр | Описание |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**.  |

   >[!Note]
   > Если истек срок сеанса, изменен пароль или вы хотите переключиться на другую учетную запись, перед входом с помощью Add-AzureRmAccount выполните следующий командлет: `Remove-AzureRmAccount-Scope Process`

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack Hub в своей подписке Azure.

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Запустите интегрированную среду скриптов PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack Hub. Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Затем в том же сеансе PowerShell убедитесь, что вы вошли в правильный контекст Azure PowerShell. Это будет учетная запись Azure, которая использовалась для регистрации указанного ранее поставщика ресурсов Azure Stack Hub. Выполните эти команды PowerShell:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Параметр | Описание |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**.  |

7. В этом же сеансе PowerShell выполните командлет **Set-AzsRegistration**. Выполните эти команды PowerShell:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Дополнительные сведения о командлете Set-AzsRegistration см. в разделе [Справка по регистрации](#registration-reference).

   Процесс займет от 10 до 15 минут. После выполнения команды отобразится сообщение **Your environment is now registered and activated using the provided parameters** (Ваша среда зарегистрирована и активирована с помощью предоставленных параметров).

## <a name="register-connected-with-capacity-billing"></a>Регистрация подключенного развертывания с оплатой на основе емкости

Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack Hub в Azure с моделью выставления счетов с оплатой по мере использования.

> [!Note]  
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке (PEP). См. сведения о PEP в руководстве по [использованию привилегированной конечной точки в Azure Stack Hub](azure-stack-privileged-endpoint.md).

Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack Hub, а затем настроить модель выставления счетов.

1. Чтобы зарегистрировать поставщик ресурсов Azure Stack Hub в Azure, запустите интегрированную среду скриптом PowerShell и выполните следующие командлеты PowerShell с параметром **EnvironmentName**, заданным для соответствующего типа подписки Azure (см. параметры ниже).

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack Hub. Чтобы добавить учетную запись, выполните командлет **Add-AzureRmAccount**. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную проверку подлинности в зависимости от конфигурации вашей учетной записи.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Параметр | Описание |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**.  |

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack Hub в своей подписке Azure.

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Запустите интегрированную среду скриптов PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack Hub. Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Отчеты о потреблении можно отключить, задав для параметра UsageReportingEnabled командлета **Set-AzsRegistration** значение false. 
   
   Дополнительные сведения о командлете Set-AzsRegistration см. в разделе [Справка по регистрации](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Регистрация отключенного развертывания с оплатой на основе емкости

Если вы регистрируете Azure Stack Hub в автономной среде (без подключения к Интернету), необходимо получить токен регистрации из среды Azure Stack Hub. Затем используйте этот токен на компьютере с PowerShell для Azure Stack Hub, который может подключаться к Azure.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Получение маркера регистрации из среды Azure Stack Hub

1. Запустите интегрированную среду скриптов PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack Hub. Импортируйте модуль **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Чтобы получить маркер регистрации, выполните следующие командлеты PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Дополнительные сведения о командлете Get-AzsRegistrationToken см. в разделе [Справка по регистрации](#registration-reference).

   > [!Tip]  
   > Маркер регистрации сохранен в файле, указанном для *$FilePathForRegistrationToken*. Путь к файлу или его имя можно изменить по своему усмотрению.

3. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure. Файл или текст можно скопировать из *$FilePathForRegistrationToken*.

### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация

На компьютере, подключенном к Интернету, выполните те же действия, чтобы импортировать модуль RegisterWithAzure.psm1 и войти в правильный контекст Azure Powershell. Затем вызовите командлет Register-AzsEnvironment. Укажите маркер регистрации для регистрации в Azure: При регистрации нескольких экземпляров Azure Stack Hub с одинаковым идентификатором подписки Azure следует задать уникальное имя регистрации.

Вам нужен маркер регистрации и уникальное имя токена.

1. Запустите интегрированную среду скриптов PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack Hub. Импортируйте модуль **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Выполните следующие командлеты PowerShell:  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

При необходимости вы можете использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации.

Вам нужен маркер регистрации и уникальное имя токена.

1. Запустите интегрированную среду скриптов PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack Hub. Импортируйте модуль **RegisterWithAzure.psm1**:  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. Выполните следующие командлеты PowerShell:  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > Сохраните имя ресурса регистрации или токен регистрации для дальнейшего использования.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Извлечение ключа активации из ресурса регистрации Azure

Затем необходимо извлечь ключ активации из ресурса регистрации, созданного в Azure во время выполнения Register-AzsEnvironment.

Чтобы получить ключ активации, выполните следующие командлеты PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Ключ активации сохраняется в файл, указанный для *$KeyOutputFilePath*. Путь к файлу или его имя можно изменить по своему усмотрению.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Создание ресурса активации в Azure Stack Hub

Вернитесь в среду Azure Stack Hub с файлом или текстом ключа активации, созданного с помощью Get-AzsActivationKey. Затем создайте ресурс активации в Azure Stack Hub с помощью этого ключа активации. Чтобы создать ресурс активации, выполните следующие командлеты PowerShell:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-hub-registration"></a>Проверка состояния регистрации Azure Stack Hub

Чтобы проверить состояние регистрации Azure Stack Hub, можно использовать плитку **Управление регионами**. На портале администрирования эта плитка размещается на панели мониторинга по умолчанию. Состояние может иметь значение "Зарегистрировано" или "Не зарегистрировано". Кроме того, после регистрации здесь отображаются идентификатор подписки, использовавшийся для регистрации Azure Stack Hub, а также группа ресурсов и имя ресурса регистрации.

1. Войдите на [портал администратора Azure Stack Hub](https://adminportal.local.azurestack.external).

2. На панели мониторинга выберите плитку **Управление регионами**.

3. Выберите **Свойства**. В этой колонке отображаются данные о состоянии и сведения о вашей среде. Состояние может иметь значение **Зарегистрировано**, **Не зарегистрировано** или **Просрочено**.

    [![Плитка управления регионами на портале администрирования Azure Stack Hub](media/azure-stack-registration/admin1sm.png "Плитка управления регионами")](media/azure-stack-registration/admin1.png#lightbox)

    Если зарегистрировано, свойства включают:
    
    - **Идентификатор подписки регистрации**. Идентификатор подписки Azure, зарегистрированный в Azure Stack Hub и связанный с ней.
    - **Группа ресурсов регистрации**. Группа ресурсов Azure в связанной подписке, которая содержит ресурсы Azure Stack Hub.

4. Вы можете просматривать ресурсы регистрации Azure Stack Hub и проверять состояние регистрации на портале Azure. Войдите на [портал Azure](https://portal.azure.com) с учетной записью, которая связана с подпиской, указанной при регистрации Azure Stack Hub. Щелкните **Все ресурсы**, установите флажок **Показать скрытые типы** и выберите имя регистрации.

5. Если регистрацию выполнить не удалось, [повторите попытку](#change-the-subscription-you-use), чтобы устранить проблему.  

Успешность регистрации можно также проверить с помощью функции управления Marketplace. Если в колонке управления Marketplace отображается список элементов Marketplace, это означает, что регистрация прошла успешно. При этом в отключенных средах элементы Marketplace в колонке управления Marketplace не отображаются.

> [!NOTE]
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться. В выпусках Azure Stack Hub до 1904 в автономном режиме в колонке управления Marketplace отображается запрос на регистрацию и активацию Azure Stack Hub, даже если регистрация уже выполнена. Это сообщение не отображается в выпусках после 1904.

## <a name="renew-or-change-registration"></a>Обновление или изменение регистрации

### <a name="renew-or-change-registration-in-connected-environments"></a>Обновление или изменение регистрации в подключенных средах

Регистрацию необходимо обновлять или возобновлять в следующих случаях:

- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

#### <a name="change-the-subscription-you-use"></a>Изменение подписки

Если вы хотите изменить используемую подписку, сначала выполните командлет **Remove-AzsRegistration**. Убедитесь, что вы вошли в правильном контексте выполнения Azure PowerShell. Затем выполните командлет **Set-AzsRegistration**, изменив любые параметры, в том числе `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Изменение компонентов модели выставления счетов или способов предложения функций

Если вам нужно изменить компоненты модели выставления счетов или способов предложения функций для установки, чтобы задать новые значения, можно применить функцию регистрации. Для этого не требуется удалять текущую регистрацию:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Обновление или изменение регистрации в отключенных средах

Регистрацию необходимо обновлять или возобновлять в следующих случаях:

- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

#### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Удаление ресурса активации из Azure Stack Hub

Сначала необходимо удалить ресурс активации из Azure Stack Hub, а затем — из Azure.  

Чтобы удалить ресурс активации из Azure Stack Hub, выполните следующие командлеты PowerShell в среде Azure Stack Hub.  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Затем, чтобы удалить ресурс регистрации в Azure, убедитесь, что используется компьютер, подключенный к Azure, войдите в правильный контекст Azure PowerShell и выполните соответствующие командлеты PowerShell, как описано ниже.

Вы можете использовать токен регистрации, с помощью которого создавался ресурс:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Или можно использовать имя регистрации:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Повторная регистрация c использованием шагов для отключенной среды

Итак, вы полностью отменили регистрацию в автономном режиме, и теперь нужно повторить процесс регистрации среды Azure Stack Hub в этом режиме.

### <a name="disable-or-enable-usage-reporting"></a>Отключение или включение отчетов о потреблении

Для сред Azure Stack Hub, в которых используется модель выставления счетов с оплатой на основе емкости, отключите отчеты о потреблении с помощью параметра **UsageReportingEnabled** или с помощью командлетов **Set-AzsRegistration** либо **Get-AzsRegistrationToken**. Azure Stack Hub передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении.

#### <a name="with-a-connected-azure-stack-hub"></a>Azure Stack Hub в режиме подключения

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack-hub"></a>Azure Stack Hub в автономном режиме

1. Чтобы изменить маркер регистрации, выполните следующие командлеты PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Маркер регистрации сохранен в файле, указанном для *$FilePathForRegistrationToken*. Путь к файлу или его имя можно изменить по своему усмотрению.

2. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure. Файл или текст можно скопировать из *$FilePathForRegistrationToken*.

## <a name="move-a-registration-resource"></a>Перемещение ресурса регистрации

Перемещение ресурса регистрации между группами ресурсов в одной подписке **поддерживается** для всех сред. Однако перемещение ресурса регистрации между подписками поддерживается только для CSP, когда обе подписки разрешают один и тот же идентификатор партнера. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Чтобы предотвратить случайное удаление ресурсов регистрации на портале, скрипт регистрации автоматически добавляет блокировку в ресурс. Эту блокировку необходимо удалить перед перемещением или удалением. Мы рекомендуем добавить блокировку в ресурс регистрации, чтобы предотвратить случайное удаление.

## <a name="registration-reference"></a>Справка по регистрации

### <a name="set-azsregistration"></a>Set-AzsRegistration

Командлет **Set-AzsRegistration** позволяет зарегистрировать Azure Stack Hub в Azure, а также включить или отключить предложение элементов в Marketplace и отчеты о потреблении.

Чтобы запустить командлет, вам потребуется:

- Глобальная подписке Azure любого типа.
- Необходимо войти в Azure PowerShell с помощью учетной записи владельца или участника этой подписки.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Параметр | Тип | Описание |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Учетные данные, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Модель выставления счетов, которая используется в подписке. Допустимые значения для этого параметра: Capacity, PayAsYouUse и Development. |
| MarketplaceSyndicationEnabled | True или False | Определяет, доступна ли функция управления Marketplace на портале. Имеет значение true, если регистрация осуществляется с подключением к Интернету. Имеет значение false, если регистрация осуществляется в среде без подключения к Интернету. При регистрации в среде без подключения к Интернету можно использовать [инструмент автономной синдикации](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) для скачивания элементов Marketplace. |
| UsageReportingEnabled | True или False | Azure Stack Hub передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении. Допустимые значения для этого параметра: True, False. |
| AgreementNumber | String |  |
| RegistrationName | String | Задайте уникальное имя регистрации, если сценарий регистрации выполняется в нескольких экземплярах Azure Stack Hub с использованием одного идентификатора подписки Azure. Этот параметр имеет значение по умолчанию **AzureStackRegistration**. Но если вы укажете одно имя для нескольких экземпляров Azure Stack Hub, выполнение скрипта завершится ошибкой. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Командлет Get-AzsRegistrationToken создает маркер регистрации из входных параметров.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Параметр | Тип | Описание |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Учетные данные, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Модель выставления счетов, которая используется в подписке. Допустимые значения для этого параметра: Capacity, PayAsYouUse и Development. |
| MarketplaceSyndicationEnabled | True или False |  |
| UsageReportingEnabled | True или False | Azure Stack Hub передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении. Допустимые значения для этого параметра: True, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Ошибки регистрации

При попытке регистрации Azure Stack Hub может появиться одна из указанных ниже ошибок.

- Не удалось получить обязательные сведения об оборудовании для $hostName. Проверьте физический узел и подключение, а потом попытайтесь повторить регистрацию.

- Не удается подключиться к $hostName для получения сведений об оборудовании. Проверьте физический узел и подключение, а затем попытайтесь повторно выполнить регистрацию.

> Причина. Обычно это происходит из-за того, что мы пытаемся получить сведения об оборудовании, такие как UUID, Bios и ЦП, с узлов, чтобы предпринять попытку активации, которая не удается из-за невозможности подключения к физическому узлу.

Когда вы пытаетесь получить доступ к управлению Marketplace, возникает ошибка при попытке синдицировать продукты. 
> Причина. Обычно это происходит, когда Azure Stack Hub не удается получить доступ к ресурсу регистрации. Одной из распространенных причин этого является сброс регистрации при смене клиента каталога подписки Azure. Вы не сможете получить доступ к Azure Stack Hub Marketplace или сведениям об использовании отчетов, если измените клиент каталога подписки. Необходимо повторно зарегистрироваться, чтобы устранить эту проблему.

Средство управления Marketplace предложит вам зарегистрировать и активировать Azure Stack Hub, даже если вы уже зарегистрировали свою метку в автономном режиме.
> Причина. Это известная проблема в автономных средах. Вы можете проверить состояние регистрации, выполнив [эти шаги](azure-stack-registration.md#verify-azure-stack-hub-registration). Чтобы использовать возможность управления Marketplace, необходимо [автономное средство](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario).

## <a name="next-steps"></a>Дальнейшие действия

[Скачивание элементов Marketplace из Azure](azure-stack-download-azure-marketplace-item.md)
