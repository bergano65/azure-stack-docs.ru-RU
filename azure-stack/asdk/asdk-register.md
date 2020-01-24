---
title: Регистрация ASDK в Azure | Документация Майкрософт
description: Узнайте, как зарегистрировать Пакет средств разработки Azure Stack (ASDK) в Azure, чтобы включить синдикацию marketplace и создание отчетов о потреблении.
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
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: c12882ea5f26589c18abaf016ba09b17d02bdcab
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022940"
---
# <a name="register-the-asdk-with-azure"></a>Регистрация ASDK в Azure

Вы можете зарегистрировать устанавливаемый Пакет средств разработки Azure Stack (ASDK) в Azure, чтобы скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. Регистрация требуется для поддержки полной функциональности Azure Stack, включая синдикацию marketplace. Выполните регистрацию, чтобы вы могли протестировать важные функции Azure Stack, например синдикацию Marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. Но с пользователей ASDK не взимается плата на основе отчетов о потреблении.

Если не зарегистрировать пакет ASDK, отобразится предупреждение **Требуется активация**. Это значит, что вам необходимо зарегистрировать ASDK. Это ожидаемое поведение.

## <a name="prerequisites"></a>предварительные требования

Перед выполнением инструкций по регистрации ASDK в Azure установите PowerShell для Azure Stack и скачайте инструменты Azure Stack, как описано в статье о [настройке, выполняемой после установки ASDK](asdk-post-deploy.md).

На компьютере, используемом для регистрации ASDK в Azure, необходимо установить параметр языкового режима PowerShell **FullLanguage**. Чтобы проверить, что текущий языковой режим имеет значение full, откройте окно PowerShell с повышенным уровнем разрешений и выполните следующие команды PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Убедитесь, что в выходных данных возвращено значение **FullLanguage**. Если возвращено другое значение языкового режима, необходимо выполнить регистрацию на другом компьютере или задать для языкового режима значение **FullLanguage** и продолжить регистрацию.

Учетная запись Azure AD, используемая для регистрации, должна обладать правами доступа к подписке Azure и разрешением на создание приложений с удостоверениями и субъектов-служб в каталоге, который связан с подпиской. Мы рекомендуем зарегистрировать Azure Stack в Azure, [создав учетную запись службы для регистрации](../operator/azure-stack-registration-role.md) вместо использования учетных данных глобального администратора.

## <a name="register-the-asdk"></a>Регистрация ASDK

Чтобы зарегистрировать ASDK в Azure, следуйте приведенным ниже инструкциям.

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для ASDK этот компьютер является главным компьютером ASDK.

1. Откройте консоль PowerShell с правами администратора.  

2. Выполните следующие команды PowerShell, чтобы зарегистрировать установку ASDK в Azure. Выполните вход в подписку для выставления счетов Azure и локальный экземпляр ASDK. Если у вас еще нет подписки Azure для выставления счетов, создайте [бесплатную учетную запись здесь](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.<br><br>Задайте уникальное имя для регистрации при выполнении командлета **Set-AzsRegistration**. Параметр **RegistrationName** имеет значение по умолчанию **AzureStackRegistration**. Тем не менее, если задать одно имя для нескольких экземпляров Azure Stack, сценарий завершится ошибкой.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
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
 > Перед выполнением инструкций по регистрации Azure Stack установите PowerShell для Azure Stack и скачайте инструменты Azure Stack, как описано в статье о [настройке, выполняемой после развертывания](asdk-post-deploy.md), как на главном компьютере ASDK, так и на компьютере с доступом к Интернету, используемом для подключения к Azure и регистрации.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение маркера регистрации из среды Azure Stack

На главном компьютере ASDK запустите PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время скачивания инструментов Azure Stack. Используйте следующие команды PowerShell, чтобы импортировать модуль **RegisterWithAzure.psm1**, а затем используйте командлет **Get-AzsRegistrationToken**, чтобы получить маркер регистрации:  

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

Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Интернету. Можно скопировать файл (или текст из файла), созданный с помощью параметра `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация

На подключенном к Интернету компьютеру используйте следующие команды PowerShell, чтобы импортировать модуль **RegisterWithAzure.psm1**, а затем используйте командлет **Register-AzsEnvironment**, чтобы выполнить регистрацию в Azure, предоставив только что созданный маркер регистрации и уникальное имя регистрации.  

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

После завершения регистрации вы увидите примерно следующее сообщение: **Your Azure Stack environment is now registered with Azure** (Среда Azure Stack зарегистрирована в Azure).

> [!IMPORTANT]
> **Не** закрывайте окно PowerShell.

Сохраните маркер регистрации и имя ресурса регистрации для дальнейшего использования.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Извлечение ключа активации из ресурса регистрации Azure

По-прежнему используя компьютер, который подключен к Интернету, **и то же окно консоли PowerShell**, извлеките ключ активации из ресурса регистрации, созданного при регистрации в Azure.

Чтобы получить ключ активации, выполните следующие команды PowerShell. Используйте то же значение уникального имени регистрации, которое вы указали при регистрации в Azure на предыдущем шаге.  

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

После завершения регистрации вы увидите примерно следующее сообщение: **Your environment has finished the registration and activation process** (Процесс регистрации и активации вашей среды завершен).

## <a name="verify-the-registration-was-successful"></a>Подтверждение успешного выполнения регистрации

Для проверки успешной регистрации Azure Stack можно использовать плитку **Управление регионами**. На портале администрирования эта плитка размещается на панели мониторинга по умолчанию.

1. Войдите на [портал администратора Azure Stack](https://adminportal.local.azurestack.external).

2. На панели мониторинга выберите плитку **Управление регионами**.

    [![Плитка Region Management (Управление регионами) на портале администрирования Azure Stack](media/asdk-register/admin1sm.png "Плитка управления регионами")](media/asdk-register/admin1.png#lightbox)

3. Выберите **Свойства**. В этой колонке отображаются данные о состоянии и сведения о вашей среде. Состояние может иметь значение **Зарегистрировано** или **Не зарегистрировано**. Если отображается состояние "Зарегистрировано", вы можете увидеть идентификатор подписки, использовавшийся для регистрации Azure Stack, а также группу и имя ресурса регистрации.

## <a name="move-a-registration-resource"></a>Перемещение ресурса регистрации
Перемещение ресурса регистрации между группами ресурсов в одной подписке **поддерживается**. Дополнительные сведения о перемещении ресурсов в новую группу ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Дальнейшие действия

- [Добавление элемента Marketplace для Azure Stack Hub](../operator/azure-stack-marketplace.md)
