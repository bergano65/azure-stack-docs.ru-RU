---
title: Использование модуля политики Azure Stack Hub
description: Сведения о том, как ограничить подписку Azure, чтобы ее поведение было аналогично подписке Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 7af2662c52de8085b6b77fa0c9a2b36f401168fc
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703831"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Управление политикой Azure с использованием модуля политики Azure Stack Hub

Модуль политики Azure Stack Hub позволяет настроить в подписке Azure такую же доступность служб и управления версиями, как и в Azure Stack Hub. Этот модуль использует командлет PowerShell [**New-AzureRmPolicyDefinition**](/powershell/module/azurerm.resources/new-azurermpolicydefinition) для создания политики Azure, которая ограничивает типы ресурсов и службы, доступные в подписке. Затем создайте назначение политики в соответствующей области с помощью командлета [**New-AzureRmPolicyAssignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment). После настройки политики подписку Azure можно использовать для разработки приложений, предназначенных для Azure Stack Hub.

## <a name="install-the-module"></a>Установка модуля

1. Установите необходимую версию модуля PowerShell AzureRM, как описано в шаге 1 руководства по [установке PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md).
2. [Скачайте средства Azure Stack Hub из GitHub](../operator/azure-stack-powershell-download.md).
3. [Настройте PowerShell для использования с Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Импортируйте модуль **AzureStack.Policy.psm1**.

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Применение политики к подписке Azure

Следующая команда позволяет применить политику Azure Stack Hub по умолчанию к подписке Azure. Перед выполнением этих команд замените `Azure subscription name` именем своей подписки Azure.

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Применение политики к группе ресурсов

Возможно, вам потребуется применить политики для выборочного управления. Например, у вас могут быть другие ресурсы, выполняющиеся в той же подписке. Областью действия приложения политики можно задать определенную группу ресурсов, которая позволит тестировать приложения для работы с по умолчанию с использованием ресурсов Azure. Перед выполнением следующих команд замените `Azure subscription name` именем своей подписки Azure.

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Политика в действии

После применения политики Azure при попытке развернуть ресурс, который запрещен этой политикой, возникает ошибка.

![Результат сбоя развертывания ресурса из-за ограничения политики](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
* [Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)
* [Deploy templates in Azure Stack using Visual Studio](azure-stack-deploy-template-visual-studio.md) (Развертывание шаблонов в Azure Stack с помощью Visual Studio)
