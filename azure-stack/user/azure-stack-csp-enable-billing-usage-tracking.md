---
title: Предоставление поставщику облачных служб возможности управлять подпиской Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как предоставить поставщику облачных служб возможность управлять подпиской Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691980"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Предоставление поставщику облачных служб возможности управлять подпиской Azure Stack

*Область применения: интегрированные системы Azure Stack*

Если вы работаете с Azure Stack с помощью поставщика облачных служб (CSP), то можете самостоятельно управлять своей подпиской для доступа к ресурсам в Azure и Azure Stack. Можно также разрешить поставщику управлять подпиской для вас. В этой статье описаны следующие процессы.

* Предоставьте поставщику служб доступ к своей подписке.
* Убедитесь, что поставщик служб может управлять вашей службой.

> [!NOTE]
> Если поставщик облачных служб не управляет вашей учетной записью и вы пропустите следующие шаги, то поставщик облачных служб не сможет управлять вашей подпиской Azure Stack.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Управление подпиской с помощью поставщика облачных служб

Добавьте поставщик облачных служб в качестве **пользователя** вашей подписки.

1. Добавьте выбранного поставщика облачных служб в качестве гостевого пользователя с ролью **пользователя** в вашем каталоге клиента. Инструкции по добавлению пользователя см. в статье [Add or delete users using Azure Active Directory](/azure/active-directory/add-users-azure-active-directory) (Добавление или удаление пользователей с помощью Azure Active Directory).

2. Поставщик облачных служб создает для вас локальную подписку Azure Stack. Теперь вы можете использовать Azure Stack.
3. Поставщик облачных служб должен создать ресурс в вашей подписке, чтобы убедиться, что он также может управлять ресурсами. Например, можно [создать виртуальную машину Windows на портале Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Предоставление поставщику облачных служб возможности управлять подпиской с помощью прав управления доступом на основе ролей

Добавьте поставщик облачных служб в качестве **владельца** вашей подписки.

1. Добавьте поставщик облачных служб в качестве гостевого пользователя в вашем каталоге клиента. Инструкции по добавлению пользователя см. в статье [Add or delete users using Azure Active Directory](/azure/active-directory/add-users-azure-active-directory) (Добавление или удаление пользователей с помощью Azure Active Directory).

2. Назначьте гостевому пользователю поставщика облачных служб роль **владельца**. Подробные сведения о добавлении поставщика облачных служб в подписку см. в статье [Manage access to Azure resources using RBAC and the Azure portal](/azure/role-based-access-control/role-assignments-portal) (Управление доступом к ресурсам Azure использованием управления доступом на основе ролей и портала Azure). Поставщик облачных служб создает для вас локальную подписку Azure Stack. Теперь вы можете использовать Azure Stack.
3. Поставщик облачных служб должен создать ресурс в вашей подписке, чтобы убедиться, что он может управлять ресурсами.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в статье о [потреблении ресурсов и выставлении счетов в Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
