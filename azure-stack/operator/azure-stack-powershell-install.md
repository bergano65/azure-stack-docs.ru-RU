---
title: Установка модуля PowerShell AzureRM для Azure Stack Hub
description: Узнайте, как установить PowerShell для Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: b362ab1e4c555ae4de5be0feecd19d8cc8e6654a
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069440"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>Установка модуля PowerShell AzureRM для Azure Stack Hub

В Azure PowerShell AzureRM доступен набор командлетов, которые используют модель Azure Resource Manager для управления ресурсами Azure Stack Hub.

Кроме того, необходимо использовать *профили API*, чтобы указать совместимые конечные точки для поставщиков ресурсов Azure Stack Hub.

Профили API позволяют управлять различиями между версиями Azure и Azure Stack Hub. Профиль версии API — это набор модулей Azure Resource Manager PowerShell с определенными версиями API. Каждая облачная платформа имеет набор поддерживаемых профилей версий API. К примеру, Azure Stack Hub поддерживает определенную версию профиля, например **2019-03-01-hybrid**. При установке профиля устанавливается набор модулей Azure Resource Manager PowerShell, которые соответствуют выбранному профилю.

Вы можете установить совместимые модули PowerShell для Azure Stack Hub в сценарии с полноценным, частичным и отсутствующим подключением к Интернету. В этой статье рассматриваются подробные инструкции для этих сценариев.

## <a name="1-verify-your-prerequisites"></a>1. Проверка необходимых компонентов

Перед началом работы с Azure Stack Hub и модулем PowerShell AzureRM нужно проверить наличие таких необходимых компонентов:

- **PowerShell версии 5.1**. <br>
Чтобы проверить используемую версию, выполните команду **$PSVersionTable.PSVersion** и сравните **номер основной версии**. Если у вас нет PowerShell 5.1, следуйте инструкциям из раздела [Обновление существующей версии Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

  > [!Note]
  > Для PowerShell 5.1 требуется компьютер с ОС Windows.

- **Откройте командную строку PowerShell с повышенными правами**.

- **Доступ к коллекции PowerShell** <br>
  Необходим доступ к [коллекции PowerShell](https://www.powershellgallery.com). Коллекция является центральным репозиторием содержимого PowerShell. Модуль **PowerShellGet** содержит командлеты для обнаружения, установки, обновления и публикации артефактов PowerShell. Примерами таких артефактов являются модули, ресурсы DSC, возможности ролей и сценарии из коллекция PowerShell и других частных репозиториев. Если PowerShell используется в ситуации, когда подключение к Интернету отсутствует, необходимо извлечь ресурсы с компьютера с подключением к Интернету и сохранить их в доступном расположении на компьютере, не подключенном к Интернету.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Проверка доступности коллекции PowerShell

Проверьте, зарегистрирована ли коллекция PSGallery в качестве репозитория.

> [!Note]  
> На этом шаге необходим доступ к Интернету.

Откройте командную строку PowerShell с повышенными привилегиями и выполните следующие командлеты:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Если репозиторий не зарегистрирован, откройте сеанс Windows PowerShell с повышенными привилегиями и выполните в нем следующую команду.

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Удаление существующих версий модулей PowerShell для Azure Stack Hub

Прежде чем устанавливать нужную версию, обязательно удалите все установленные ранее модули PowerShell AzureRM для Azure Stack Hub. Удалите модули с помощью одного из следующих двух способов.

1. Чтобы удалить имеющиеся модули PowerShell AzureRM, закройте все активные сеансы PowerShell и выполните следующие командлеты:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Если возникает ошибка, например The module is already in use (Модуль уже используется), закройте сеансы PowerShell, которые используют модули, и повторно запустите приведенный выше скрипт.

2. Удалите все папки, имена которых начинаются с `Azure` или `Azs.`, из папок `C:\Program Files\WindowsPowerShell\Modules` и `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. При удалении этих папок удаляются все установленные модули PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. С подключением. Установка PowerShell для Azure Stack Hub с подключением к Интернету

Требуемый профиль версии API и модули PowerShell Azure Stack Hub будут зависеть от выполняемой версии Azure Stack Hub.

### <a name="install-azure-stack-hub-powershell"></a>Установка PowerShell для Azure Stack Hub

Выполните следующий скрипт PowerShell, чтобы установить эти модули на рабочей станции разработки:

::: moniker range=">=azs-2002"
Для Azure Stack Hub 2002 и более поздних версий:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.1
```

::: moniker-end
::: moniker range="azs-1910"
Для Azure Stack Hub версии 1910:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - Модуль Azure Stack Hub версии 1.8.0 является выпуском с критическим изменением. Дополнительные сведения см. в [заметках о выпуске](release-notes.md).

::: moniker-end
::: moniker range="<=azs-1908"
Для Azure Stack Hub 1908 и более ранних версий:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> Модуль Azure Stack Hub версии 1.7.2 является выпуском с критическим изменением. Чтобы выполнить миграцию с Azure Stack Hub 1.6.0, обратитесь к [руководству по миграции](https://aka.ms/azspshmigration171).

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>Подтверждение установки PowerShell

Подтвердите установку, выполнив следующую команду:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Если установка выполнена успешно, в выходных данных указываются модули `AzureAz` и `AzureStack`.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Без подключения. Установка PowerShell без подключения к Интернету

При установке без подключения к Интернету следует сначала скачать модули PowerShell на компьютер, подключенный к Интернету. Затем их нужно перенести в Пакет средств разработки Azure Stack (ASDK) для установки.

Войдите на компьютер с подключением к Интернету и, в зависимости от используемой версии Azure Stack Hub, выполните следующие сценарии для скачивания пакетов Azure Resource Manager и Azure Stack Hub.

Установка состоит из пяти шагов:

1. Установка PowerShell для Azure Stack Hub на компьютер с подключением к Интернету.
2. Включение дополнительных возможностей хранилища.
3. Передача пакетов PowerShell на рабочую станцию без подключения к Интернету.
4. Начальная загрузка поставщика NuGet, выполняемая вручную на отключенной рабочей станции
5. Подтверждение установки PowerShell.

### <a name="install-azure-stack-hub-powershell"></a>Установка PowerShell для Azure Stack Hub

::: moniker range=">=azs-2002"
Azure Stack Hub 2002 и более поздних версий.

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.1
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack Hub версии 1910.

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> Модуль Azure Stack Hub версии 1.8.0 является выпуском с критическим изменением. Дополнительные сведения см. в [заметках о выпуске](release-notes.md).

::: moniker-end
::: moniker range="<=azs-1908"
Для Azure Stack Hub 1908 и более ранних версий:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> Модуль Azure Stack Hub версии 1.7.1 является критическим изменением. Чтобы выполнить миграцию с Azure Stack Hub 1.6.0, обратитесь к [руководству по миграции](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

::: moniker-end

> [!NOTE]  
> На компьютерах без подключения к Интернету советуем выполнить приведенный ниже командлет для отключения сбора данных телеметрии. Без выключения сбора данных телеметрии может произойти снижение производительности командлетов. Это относится только к компьютерам без подключения к Интернету.
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Добавление пакетов на рабочую станцию

1. Скопируйте скачанные пакеты на USB-устройство.

2. Войдите на отключенную рабочую станцию и скопируйте пакеты с USB-устройства в нужное расположение на ней.

3. Начальная загрузка поставщика NuGet, выполняемая вручную на отключенной рабочей станции Инструкции см. в разделе [Ручной режим начальной загрузки поставщика NuGet на автономный компьютер](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Зарегистрируйте это расположение в качестве репозитория по умолчанию и установите из этого репозитория модули AzureRM и `AzureStack`:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Подтверждение установки PowerShell

Подтвердите установку, выполнив следующую команду:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Настройка PowerShell для использования прокси-сервера

Если для доступа к Интернету требуется прокси-сервер, необходимо сначала настроить PowerShell для использования имеющегося прокси-сервера.

1. Откройте командную строку PowerShell с повышенными привилегиями.
2. Выполните следующие команды:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Дальнейшие действия

- [Скачивание средств Azure Stack Hub из GitHub](azure-stack-powershell-download.md)
- [Подключение к Azure Stack Hub в роли пользователя с помощью PowerShell](../user/azure-stack-powershell-configure-user.md)
- [Подключение к Azure Stack Hub с помощью PowerShell](azure-stack-powershell-configure-admin.md)
- [Управление профилями версий API в Azure Stack Hub](../user/azure-stack-version-profiles.md)
