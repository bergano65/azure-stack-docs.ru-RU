---
title: Настройка ASDK после развертывания
description: Узнайте, какие изменения мы рекомендуем внести в конфигурацию после установки Пакета средств разработки Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: f9a1a6779a0864f49197536c2fe0758f3c59d599
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873589"
---
# <a name="post-deployment-configurations-for-asdk"></a>Настройка ASDK после его развертывания

После [ установки Пакета средств разработки Azure Stack (ASDK)](asdk-install.md) вам необходимо внести в конфигурацию несколько изменений. Для этого на главном компьютере ASDK нужно выполнить вход с правами пользователя AzureStack\AzureStackAdmin.

## <a name="install-azure-stack-powershell"></a>Установка PowerShell для Azure Stack

Для работы с Azure Stack необходимы модули Azure PowerShell, совместимые с Azure Stack.

Команды PowerShell для Azure Stack устанавливаются с использованием коллекции PowerShell. Чтобы зарегистрировать репозиторий PSGallery, откройте сеанс Windows PowerShell с повышенными привилегиями и выполните в нем следующие команды.

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Для указания совместимых с Azure Stack модулей AzureRM рекомендуется использовать профили версии API.  Профили версий API позволяют управлять различиями между версиями Azure и Azure Stack. Профиль версии API — это набор модулей AzureRM PowerShell с определенными версиями API. Модуль **AzureRM.Bootstrapper**, доступный в коллекции PowerShell, предоставляет командлеты PowerShell, необходимые для работы с профилями версий API.

Последнюю версию модуля PowerShell для Azure Stack можно установить в двух режимах: с подключением главного компьютера ASDK к Интернету или без него.

> [!IMPORTANT]
> Прежде чем устанавливать нужную версию, обязательно [удалите все установленные модули Azure PowerShell](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **При наличии подключения к Интернету** с главного компьютера ASDK. Выполните следующий сценарий PowerShell, чтобы установить эти модули в экземпляр ASDK.


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.0
  ```

  Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

- **Без подключения к Интернету** с главного компьютера ASDK. При установке в автономном режиме следует заранее скачать модули PowerShell на компьютер, подключенный к Интернету, с помощью следующих команд PowerShell:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  После этого перенесите скачанные пакеты на компьютер ASDK и зарегистрируйте это расположение в качестве репозитория по умолчанию, а затем установите модули AzureRM и AzureStack из этого репозитория:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Скачивание средств Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) — это репозиторий GitHub, в котором размещены модули PowerShell для администрирования и развертывания ресурсов в Azure Stack. Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку AzureStack-Tools, выполнив следующий скрипт:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Проверка установки ASDK

Чтобы убедиться, что развертывание ASDK прошло успешно, выполните командлет Test-AzureStack следующим образом.

1. Войдите на главный компьютер ASDK с учетной записью AzureStack\AzureStackAdmin.
2. Откройте PowerShell с правами администратора (не путайте с интегрированной средой сценариев Windows PowerShell).
3. Выполните `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`.
4. Выполните `Test-AzureStack`.

Выполнение тестов может занять несколько минут. Если установка выполнена успешно, выходные данные выглядят примерно так:

![Тестирование Azure Stack: установка выполнена успешно](media/asdk-post-deploy/test-azurestack.png)

Если операция закончится сбоем, выполните рекомендации из раздела об устранении неполадок.

## <a name="enable-multi-tenancy"></a>Включение поддержки мультитенантности

Для развертываний с помощью Azure AD необходимо [включить поддержку мультитенантности](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) для установки ASDK.

> [!NOTE]
> Если для входа на портал Azure Stack используются учетные записи администратора или пользователя из доменов, отличных от того, который использовался для регистрации Azure Stack, то доменное имя, использованное для регистрации Azure Stack, необходимо добавить в URL-адрес портала. Например, если вы зарегистрировали Azure Stack с использованием fabrikam.onmicrosoft.com, а учетная запись для входа является admin@contoso.com, URL-адрес для входа на портал пользователей будет следующим: https\:/portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Дальнейшие действия

[Регистрация ASDK в Azure AD](asdk-register.md)
