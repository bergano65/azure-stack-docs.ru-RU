---
title: Изменение владельца выставления счетов для пользовательских подписок Azure Stack Hub
description: Сведения о том, как изменить владельца выставления счетов для пользовательских подписок Azure Stack Hub.
author: justinha
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: justinha
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: e6404ad425f75dfdbf714e9b4efbf81f4bfdddcb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878957"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Изменение владельца выставления счетов для пользовательских подписок Azure Stack Hub

Операторы Azure Stack Hub могут изменить владельца выставления счетов для пользовательской подписки используя PowerShell. Одна из причин для изменения владельца заключается в замене пользователя, который увольняется из организации.

Существует два вида *владельцев*, назначенных к подписке.

- **Владелец выставления счетов.** По умолчанию это пользователь учетной записи, который получает подписку от предложения, а затем является владельцем взаимоотношений выставления счетов для этой подписки. Эта учетная запись является администратором подписки. Только одна учетная запись пользователя может иметь это обозначение в подписке. Владелец выставления счетов — это, как правило, ведущий специалист организации или руководитель команды.

  Для изменения владельца выставления счетов можно использовать командлет PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription).  

- **Владельцы, добавляемые с помощью ролей RBAC**. Это дополнительные пользователи, которым может быть предоставлена роль **владельца** с помощью системы [управления доступом на основе ролей](azure-stack-manage-permissions.md) (RBAC). В качестве владельцев выставления счетов можно добавить любое количество дополнительных пользовательских учетных записей. Дополнительные владельцы также являются администраторами подписки и имеют все привилегии для подписки, за исключением разрешения на удаление владельца выставления счетов.

  Управлять дополнительными владельцами можно с помощью PowerShell. Дополнительные сведения см. в [этой статье](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Изменение владельца выставления счетов

Чтобы изменить владельца выставления счетов пользовательской подписки, выполните следующий скрипт. Компьютер, используемый для выполнения сценария, необходимо подключить к Azure Stack Hub и запустить модуль Azure Stack Hub PowerShell версии 1.3.0 или более поздней. Дополнительные сведения см. в статье [Установка PowerShell для Azure Stack Hub](azure-stack-powershell-install.md).

>[!NOTE]
>В многопользовательской службе Azure Stack Hub новый владелец должен находиться в том же каталоге, что и существующий. Прежде чем предоставить права владения подпиской пользователю, который находится в другом каталоге, необходимо сначала [пригласить этого пользователя в свой каталог в качестве гостя](/azure/active-directory/b2b/add-users-administrator).

Замените в сценарии следующие значения перед выполнением.

- **$ArmEndpoint:** конечная точка Resource Manager для вашей среды.
- **$TenantId:** идентификатор вашего клиента.
- **$SubscriptionId:** Идентификатор подписки.
- **$OwnerUpn:** учетная запись, например **имя_пользователя\@example.com**, для добавления в качестве нового владельца выставления счетов.

```powershell
# Set up Azure Stack Hub admin environment
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

## <a name="next-steps"></a>Дальнейшие действия

- [Управление доступом на основе ролей](azure-stack-manage-permissions.md)
