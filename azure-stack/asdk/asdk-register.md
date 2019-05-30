---
title: Регистрация ASDK в Azure | Документация Майкрософт
description: В этой статье объясняется, как зарегистрировать Azure Stack в Azure, чтобы включить синдикацию Marketplace и создание отчетов о потреблении.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 10fd52a85dd46002e40061c197641a716afa3230
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267688"
---
# <a name="azure-stack-registration"></a>Регистрация Azure Stack
Вы можете зарегистрировать устанавливаемый Пакет средств разработки Azure Stack (ASDK) в Azure, чтобы скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. Регистрация требуется для поддержки полной функциональности Azure Stack, включая синдикацию marketplace. Выполните регистрацию, чтобы вы могли протестировать важные функции Azure Stack, например синдикацию Marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. Но с пользователей ASDK не взимается плата на основе отчетов о потреблении.

Если не зарегистрировать пакет ASDK, отобразится предупреждение **Требуется активация**. Это значит, что вам необходимо зарегистрировать Пакет средств разработки Azure Stack. Это ожидаемое поведение.

## <a name="prerequisites"></a>Предварительные требования
Перед выполнением инструкций по регистрации ASDK в Azure установите PowerShell для Azure Stack и скачайте средства Azure Stack, как описано в статье о [настройке, выполняемой после установки ASDK](asdk-post-deploy.md).

Кроме того, на компьютере, используемом для регистрации ASDK в Azure, необходимо установить параметр языкового режима PowerShell **FullLanguageMode**. Чтобы проверить, что текущий языковой режим имеет значение full, откройте окно PowerShell с повышенным уровнем разрешений и выполните следующие команды PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Убедитесь, что в выходных данных возвращено значение **FullLanguageMode**. Если вернулось другое значение языкового режима, необходимо выполнить регистрацию на другом компьютере или задать для языкового режима значение **FullLanguageMode** и продолжить регистрацию.

Учетная запись Azure AD, используемая для регистрации, должна обладать правами доступа к подписке Azure и разрешением на создание приложений удостоверений и субъектов-служб в каталоге, который связан с подпиской. Мы рекомендуем зарегистрировать Azure Stack в Azure с помощью администрирования наименьших привилегий, [создав учетную запись службы для регистрации](../operator/azure-stack-registration-role.md) вместо использования учетных данных глобального администратора.

## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Чтобы зарегистрировать ASDK в Azure, следуйте приведенным ниже инструкциям.

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для ASDK этот компьютер является главным компьютером, на котором размещается пакет средств разработки.

1. Откройте консоль PowerShell от имени администратора.  

2. Выполните следующие команды PowerShell, чтобы зарегистрировать установку ASDK в Azure. Вам понадобится войти в подписку для выставления счетов Azure и локальную установку ASDK. Если у вас еще нет подписки Azure для выставления счетов, создайте [бесплатную учетную запись здесь](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.<br><br>Задайте уникальное имя для регистрации при выполнении командлета **Set-AzsRegistration**. Параметр **RegistrationName** имеет значение по умолчанию **AzureStackRegistration**. Тем не менее, если задать одно имя для нескольких экземпляров Azure Stack, сценарий завершится ошибкой.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. После выполнения скрипта появятся сообщение, информирующее о том, что **среда зарегистрирована и активирована с помощью предоставленных параметров**.

    ![Среда зарегистрирована](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Регистрация в отключенных средах
При регистрации Azure Stack в отключенной среде (без подключения к Интернету) необходимо получить маркер регистрации из среды Azure Stack, а затем использовать этот маркер на компьютере, который можно подключить к Azure, чтобы зарегистрировать или создать ресурс активации для среды ASDK.
 
 > [!IMPORTANT]
 > Перед выполнением инструкций по регистрации Azure Stack установите PowerShell для Azure Stack и скачайте средства Azure Stack, как описано в статье о [настройке после развертывания](asdk-post-deploy.md), как на главном компьютере ASDK, так и на компьютере с доступом к Интернету, используемом для подключения к Azure и регистрации.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение маркера регистрации из среды Azure Stack
На главном компьютере ASDK запустите PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания средств Azure Stack. Используйте следующие команды PowerShell, чтобы импортировать модуль **RegisterWithAzure.psm1**, а затем используйте командлет **Get-AzsRegistrationToken**, чтобы получить маркер регистрации:  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Интернету. Файл или текст можно скопировать из файла, созданного с помощью параметра $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация
На подключенном к Интернету компьютеру используйте следующие команды PowerShell, чтобы импортировать модуль **RegisterWithAzure.psm1**, а затем используйте командлет **Register-AzsEnvironment**, чтобы зарегистрироваться в Azure, предоставив только что созданный маркер регистрации и уникальное имя регистрации:  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Кроме того, можно использовать командлет **Get-Content**, чтобы указать файл, содержащий маркер регистрации:

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

После завершения регистрации вы увидите сообщение, информирующее о том, что **среда Azure Stack зарегистрирована в Azure**.

> [!IMPORTANT]
> Не закрывайте окно PowerShell. 

Сохраните маркер регистрации и имя ресурса регистрации для дальнейшего использования.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Извлечение ключа активации из ресурса регистрации Azure

По-прежнему используя компьютер, который подключен к Интернету, **и то же окно консоли PowerShell**, извлеките ключ активации из ресурса регистрации, созданного при регистрации в Azure.

Чтобы получить ключ активации, выполните приведенные ниже команды PowerShell. Используйте то же значение уникального регистрационного имени, которое вы указали при регистрации в Azure на предыдущем шаге.  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Создание ресурса активации в Azure Stack

Вернитесь в среду Azure Stack с файлом или текстом из ключа активации, созданного из **Get-AzsActivationKey**. Выполните приведенные ниже команды PowerShell, чтобы создать ресурс активации в Azure Stack с помощью этого ключа активации.   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Кроме того, можно использовать командлет **Get-Content**, чтобы указать файл, содержащий маркер регистрации:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

После завершения активации вы увидите сообщение, информирующее о том, что **среда зарегистрирована и активирована**.

## <a name="verify-the-registration-was-successful"></a>Подтверждение успешного выполнения регистрации

Для проверки успешной регистрации Azure Stack можно использовать плитку **Управление регионами**. На портале администрирования эта плитка размещается на панели мониторинга по умолчанию.

1. Войдите на [портал администрирования Azure Stack](https://adminportal.local.azurestack.external).

2. На панели мониторинга выберите плитку **Управление регионами**.

    [![Плитка "Управление регионами"](media/asdk-register/admin1sm.png "Region management tile")](media/asdk-register/admin1.png#lightbox)

3. Выберите **Свойства**. В этой колонке отображаются данные о состоянии и сведения о вашей среде. Состояние может иметь значение **Зарегистрировано** или **Не зарегистрировано**. Если отображается состояние "Зарегистрировано", вы можете увидеть идентификатор подписки, использовавшийся для регистрации Azure Stack, а также группу и имя ресурса регистрации.

## <a name="move-a-registration-resource"></a>Перемещение ресурса регистрации
Перемещение ресурса регистрации между группами ресурсов в одной подписке **поддерживается**. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Дополнительная информация

- [Добавление элемента Marketplace Azure Stack](../operator/azure-stack-marketplace.md)
