---
title: Мультитенантность в Azure Stack
description: Узнайте, как организовать поддержку нескольких каталогов Azure Active Directory в Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 3b06945eefcdb8cb51ffea2e9c4d626d538f3202
ms.sourcegitcommit: 94669fe8a55fadd3103e80be307e9e8c823bf746
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68940242"
---
# <a name="multi-tenancy-in-azure-stack"></a>Мультитенантность в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете настроить в Azure Stack поддержку пользователей из нескольких клиентов Azure Active Directory (Azure AD), чтобы они использовали службы в Azure Stack. Давайте рассмотрим следующий пример:

- Вы являетесь администратором служб для contoso.onmicrosoft.com, где установлен Azure Stack.
- Мария является администратором каталога гостевых пользователей fabrikam.onmicrosoft.com.
- Компания, в которой работает Мария, использует службы IaaS и PaaS вашей компании. Вам нужно разрешить вход и использование ресурсов Azure Stack в contoso.onmicrosoft.com для пользователей из гостевого каталога (fabrikam.onmicrosoft.com).

Далее описана процедура, которая позволяет настроить мультитенантность в Azure Stack для описанного сценария. В нашем примере вам и Марии придется выполнить ряд действий, чтобы пользователи из компании Fabrikam могли выполнять вход в развертывание Azure Stack компании Contoso и использовать ресурсы этого развертывания.  

## <a name="enable-multi-tenancy"></a>Включение поддержки мультитенантности

Перед настройкой мультитенантности в Azure Stack следует обратить внимание на ряд предварительных условий.
  
 - Вам необходимо согласовать с Марией административные действия в обоих каталогах: в Contoso, где установлена инфраструктура Azure Stack, и в гостевом каталоге Fabrikam.  
 - Убедитесь, что у вас [установлена](azure-stack-powershell-install.md) и [настроена](azure-stack-powershell-configure-admin.md) среда PowerShell для Azure Stack.
 - [Скачайте средства для Azure Stack](azure-stack-powershell-download.md) и импортируйте модули Connect и Identity.

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Настройка каталога Azure Stack

В этом разделе вы настроите Azure Stack, чтобы разрешить вход с учетными данными клиентов каталога Fabrikam Azure AD.

Подключите клиент гостевого каталога Fabrikam к Azure Stack, настроив Azure Resource Manager для приема пользователей и субъектов-служб из клиента гостевого каталога.

Администратор служб contoso.onmicrosoft.com должен выполнить следующие команды.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
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

После того как администратор или оператор Azure Stack разрешил использование каталога Fabrikam в Azure Stack, Мария должна зарегистрировать Azure Stack с клиентом каталога Fabrikam.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Регистрация Azure Stack в гостевом каталоге

Мария, которая является администратором каталога Fabrikam, использует следующие команды в гостевом каталоге fabrikam.onmicrosoft.com.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Если в дальнейшем администратор Azure Stack будет устанавливать новые службы или обновления, возможно, потребуется снова запустить этот сценарий.
>
> Запускайте его в любое время, чтобы проверить состояние приложений Azure Stack в вашем каталоге.
>
> Для решения проблем с созданием виртуальных машин в Управляемых дисках (служба, добавленная с обновлением 1808) был добавлен новый **Поставщик дисковых ресурсов**, что требует перезапуска этого скрипта.

### <a name="direct-users-to-sign-in"></a>Информирование пользователей о возможности входа

Итак, вы с Марией завершили все действия по подключению ее каталога, и теперь она может сообщить пользователям Fabrikam сведения о процедуре входа в ваш каталог.  Пользователи Fabrikam (с суффиксом fabrikam.onmicrosoft.com) могут использовать для входа URL-адрес https\://portal.local.azurestack.external.  

Для всех [внешних субъектов](/azure/role-based-access-control/rbac-and-directory-admin-roles) в каталоге Fabrikam (без суффикса fabrikam.onmicrosoft.com, но включенных в каталог Fabrikam) Мария предоставит другой URL-адрес для входа: https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Если они не воспользуются этим URL-адресом, то они будут перенаправлены в каталог по умолчанию (Fabrikam) и получат сообщение об ошибке с указанием того, что администратор не дал согласия.

## <a name="disable-multi-tenancy"></a>Отключение поддержки мультитенантности

Если поддержка нескольких клиентов в Azure Stack вам больше не требуется, можно отключить мультитенантность, выполнив перечисленные далее действия в указанном порядке.

1. Как администратор гостевого каталога (в этом сценарии — Мария) запустите командлет *Unregister-AzsWithMyDirectoryTenant*. Командлет удалит все приложения Azure Stack из нового каталога.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Как администратор служб Azure Stack (в этом сценарии — вы), запустите командлет *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
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

## <a name="next-steps"></a>Дополнительная информация

- [Управление делегированными поставщиками](azure-stack-delegated-provider.md).
- [Основные понятия Azure Stack](azure-stack-overview.md).
- [Управление потреблением и оплатой для Azure Stack в роли поставщика облачных служб](azure-stack-add-manage-billing-as-a-csp.md)
- [Добавление клиентов для контроля потребления и выставления счетов в Azure Stack](azure-stack-csp-howto-register-tenants.md)
