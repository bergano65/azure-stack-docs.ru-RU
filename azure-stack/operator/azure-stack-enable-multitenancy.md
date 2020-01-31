---
title: Настройка мультитенантности в Azure Stack Hub
description: Узнайте, как включать и отключать несколько клиентов Azure Active Directory в Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dd91cfed0dd4f2444371de43e7da8a48b6339069
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890278"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Настройка мультитенантности в Azure Stack Hub

Вы можете настроить в Azure Stack Hub поддержку пользователей из нескольких клиентов Azure Active Directory (Azure AD), чтобы они использовали службы в Azure Stack Hub. Давайте рассмотрим следующий пример:

- Вы являетесь администратором служб для contoso.onmicrosoft.com, где установлен Azure Stack Hub.
- Мария является администратором каталога гостевых пользователей fabrikam.onmicrosoft.com.
- Компания, в которой работает Мария, использует службы IaaS и PaaS вашей компании. Вам нужно разрешить вход и использование ресурсов Azure Stack Hub в contoso.onmicrosoft.com для пользователей из гостевого каталога (fabrikam.onmicrosoft.com).

Далее описана процедура, которая позволяет настроить мультитенантность в Azure Stack Hub для описанного сценария. В нашем примере вам и Марии придется выполнить ряд действий, чтобы пользователи из компании Fabrikam могли выполнять вход в развертывание Azure Stack Hub компании Contoso и использовать ресурсы этого развертывания.

## <a name="enable-multi-tenancy"></a>Включение поддержки мультитенантности

Перед настройкой мультитенантности в Azure Stack Hub следует обратить внимание на ряд предварительных условий:
  
 - Вам необходимо согласовать с Марией административные действия в обоих каталогах: в Contoso, где установлена инфраструктура Azure Stack Hub, и в гостевом каталоге Fabrikam.
 - Убедитесь, что у вас [установлена](azure-stack-powershell-install.md) и [настроена](azure-stack-powershell-configure-admin.md) среда PowerShell для Azure Stack Hub.
 - [Скачайте средства для Azure Stack Hub](azure-stack-powershell-download.md) и импортируйте модули Connect и Identity:

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Настройка каталога Azure Stack Hub

В этом разделе вы настроите Azure Stack Hub, чтобы разрешить вход с учетными данными клиентов каталога Fabrikam Azure AD.

Подключите клиент гостевого каталога Fabrikam к Azure Stack Hub, настроив Azure Resource Manager для приема пользователей и субъектов-служб из клиента гостевого каталога.

Администратор служб contoso.onmicrosoft.com должен выполнить следующие команды:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group.
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>Настройка гостевого каталога

После того как оператор Azure Stack Hub разрешил использование каталога Fabrikam в Azure Stack Hub, Мария должна зарегистрировать Azure Stack Hub с клиентом каталога Fabrikam.

#### <a name="registering-azure-stack-hub-with-the-guest-directory"></a>Регистрация Azure Stack Hub в гостевом каталоге

Мария, которая является администратором каталога Fabrikam, использует следующие команды в гостевом каталоге fabrikam.onmicrosoft.com:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Если в дальнейшем администратор Azure Stack Hub будет устанавливать новые службы или обновления, возможно, потребуется снова запустить этот сценарий.
>
> Запускайте его в любое время, чтобы проверить состояние приложений Azure Stack Hub в вашем каталоге.
>
> Для решения проблем с созданием виртуальных машин в Управляемых дисках (служба, добавленная с обновлением 1808) был добавлен новый **поставщик дисковых ресурсов**, что требует перезапуска этого скрипта.

### <a name="direct-users-to-sign-in"></a>Информирование пользователей о возможности входа

Итак, вы с Марией завершили все действия по подключению ее каталога, и теперь она может сообщить пользователям Fabrikam сведения о процедуре входа в ваш каталог. Пользователи Fabrikam (с суффиксом fabrikam.onmicrosoft.com) могут использовать для входа URL-адрес https\://portal.local.azurestack.external.

Для всех [внешних субъектов](/azure/role-based-access-control/rbac-and-directory-admin-roles) в каталоге Fabrikam (без суффикса fabrikam.onmicrosoft.com, но включенных в каталог Fabrikam) Мария предоставит другой URL-адрес для входа: https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com. Если они не воспользуются этим URL-адресом, то они будут перенаправлены в каталог по умолчанию (Fabrikam) и получат сообщение об ошибке с указанием того, что администратор не дал согласия.

## <a name="disable-multi-tenancy"></a>Отключение поддержки мультитенантности

Если поддержка нескольких клиентов в Azure Stack Hub вам больше не требуется, можно отключить мультитенантность, выполнив перечисленные далее действия в указанном порядке:

1. Как администратор гостевого каталога (в этом сценарии — Мария) запустите командлет *Unregister-AzsWithMyDirectoryTenant*. Командлет удалит все приложения Azure Stack Hub из нового каталога.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Как администратор служб Azure Stack Hub (в этом сценарии — вы) запустите командлет *Unregister-AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Действия для отключения мультитенантности необходимо выполнить в указанном порядке. Шаг 1 не удастся выполнить, если перед ним выполнен шаг 2.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление делегированными поставщиками](azure-stack-delegated-provider.md).
- [Обзор Azure Stack Hub](azure-stack-overview.md)
- [Управление потреблением и оплатой для Azure Stack Hub в роли поставщика облачных решений](azure-stack-add-manage-billing-as-a-csp.md)
- [Добавление клиентов для контроля потребления и выставления счетов в Azure Stack Hub](azure-stack-csp-howto-register-tenants.md)
