---
title: Изменение владельца выставления счетов для пользовательских подписок Azure Stack | Документация Майкрософт
description: Сведения о том, как изменить владельца выставления счетов для пользовательских подписок Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: justinha
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 3c4453974092fd1873e5f77b1074c82851fc1be1
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094377"
---
# <a name="change-the-billing-owner-for-an-azure-stack-user-subscription"></a>Изменение владельца выставления счетов для пользовательских подписок Azure Stack

Операторы Azure Stack могут с помощью PowerShell изменить владельца выставления счетов для пользовательской подписки. Одна из причин для изменения владельца заключается в замене пользователя, который увольняется из организации.

Существует два вида *владельцев*, назначенных к подписке.

- **Владелец выставления счетов.** По умолчанию это пользователь учетной записи, который получает подписку от предложения, а затем является владельцем взаимоотношений выставления счетов для этой подписки. Эта учетная запись является администратором подписки. Только одна учетная запись пользователя может иметь это обозначение в подписке. Владелец выставления счетов — это, как правило, ведущий специалист организации или руководитель команды.

  Для изменения владельца выставления счетов можно использовать командлет PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription).  

- **Владельцы, добавляемые с помощью ролей RBAC**. Это дополнительные пользователи, которым может быть предоставлена роль **владельца** с помощью системы [управления доступом на основе ролей](azure-stack-manage-permissions.md) (RBAC). В качестве владельцев выставления счетов можно добавить любое количество дополнительных пользовательских учетных записей. Дополнительные владельцы также являются администраторами подписки и имеют все привилегии для подписки, за исключением разрешения на удаление владельца выставления счетов.

  Управлять дополнительными владельцами можно с помощью PowerShell. Дополнительные сведения см. в [этой статье](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Изменение владельца выставления счетов

Чтобы изменить владельца выставления счетов пользовательской подписки, выполните следующий скрипт. Компьютер, используемый для выполнения сценария, необходимо подключить к Azure Stack и запустить модуль Azure Stack PowerShell версии 1.3.0 или более поздней. Дополнительные сведения см. в статье [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md).

>[!NOTE]
>В многопользовательской службе Azure Stack новый владелец должен находиться в том же каталоге, что и существующий. Прежде чем предоставить права владения подпиской пользователю, который находится в другом каталоге, необходимо сначала [пригласить этого пользователя в свой каталог в качестве гостя](/azure/active-directory/b2b/add-users-administrator).

Замените в сценарии следующие значения перед выполнением.

- **$ArmEndpoint:** конечная точка Resource Manager для вашей среды.
- **$TenantId:** идентификатор вашего клиента.
- **$SubscriptionId:** Идентификатор подписки.
- **$OwnerUpn:** учетная запись, например **имя_пользователя\@example.com**, для добавления в качестве нового владельца выставления счетов.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../../includes/remove-account.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Управление доступом на основе ролей](azure-stack-manage-permissions.md)
