---
title: Разрешение приложениям получать доступ к секретам из хранилища ключей Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как запустить пример приложения, которое извлекает ключи и секреты из хранилища ключей в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 97299ec47908325f7d3eddb7cf57ca891e145a8d
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718493"
---
# <a name="allow-apps-to-access-azure-stack-key-vault-secrets"></a>Разрешение приложениям получать доступ к секретам из хранилища ключей Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Выполните шаги в этой статье, чтобы запустить пример приложения **HelloKeyVault**, которое извлекает ключи и секреты из хранилища ключей в Azure Stack.

## <a name="prerequisites"></a>предварительные требования

Вы можете установить следующие обязательные компоненты из [Пакета средств разработки Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) или из внешнего клиента Windows, если вы [используете VPN-подключение](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Установите [совместимые с Azure Stack модули Azure PowerShell](../operator/azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Создание хранилища ключей и регистрация приложения

Чтобы подготовить пример приложения, выполните следующие действия:

* Создайте хранилище ключей в Azure Stack.
* Зарегистрируйте приложение в Azure Active Directory (Azure AD).

С помощью портала Azure или PowerShell выполните действия для подготовки к установке примера приложения.

> [!NOTE]
> По умолчанию скрипт PowerShell создает новое приложение в Active Directory. Но его можно использовать и для регистрации существующего приложения.

Прежде чем запускать приведенный ниже скрипт, не забудьте предоставить значения для переменных `aadTenantName` и `applicationPassword`. Если вы не укажете значение для переменной `applicationPassword`, скрипт создаст случайный пароль.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

На следующем изображении показаны выходные данные скрипта, используемого для создания хранилища ключей.

![Хранилище ключей с ключами доступа](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Запишите значения **VaultUrl**, **AuthClientId** и **AuthClientSecret**, возвращенные предыдущим скриптом. Используйте их для запуска приложения **HelloKeyVault**.

## <a name="download-and-configure-the-sample-application"></a>Скачивание и настройка примера приложения

Скачайте пример хранилища ключей со страницы [примеров клиентов хранилища ключей](https://www.microsoft.com/download/details.aspx?id=45343) Azure. Извлеките содержимое ZIP-файла на рабочей станции разработки. Папка примеров содержит два приложения. В этой статье используется **HelloKeyVault**.

Чтобы применить пример **HelloKeyVault**, выполните следующие действия.

1. Перейдите к папке **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault**.
2. Откройте приложение **HelloKeyVault** в Visual Studio.

### <a name="configure-the-sample-application"></a>Настройка примера приложения

В Visual Studio:

1. Откройте файл HelloKeyVault\App.config и найдите элемент `<appSettings>`.
2. Обновите ключи **VaultUrl**, **AuthClientId** и **AuthClientSecret** с помощью значений, которые были возвращены при создании хранилища ключей. По умолчанию файл App.config содержит заполнитель для `AuthCertThumbprint`. Замените его на `AuthClientSecret`.

   ![Параметры приложения](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Повторно создайте решение.

## <a name="run-the-app"></a>Запустите приложение

При запуске приложение **HelloKeyVault** входит в Azure AD и применяет маркер `AuthClientSecret` для аутентификации в хранилище ключей Azure Stack.

Пример **HelloKeyVault** позволяет выполнить следующие действия:

* запустить основные операции, например создание, шифрование, заключение в оболочку или удаление ключей и секретных данных;
* передать приложению **HelloKeyVault** параметры, например `encrypt` и `decrypt`, чтобы применить указанные изменения к хранилищу ключей.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание виртуальной машины с помощью пароля из хранилища ключей](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Create a virtual machine and include certificate retrieved from a key vault](azure-stack-key-vault-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)
