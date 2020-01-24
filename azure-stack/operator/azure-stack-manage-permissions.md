---
title: Настройка разрешений на доступ с помощью управления доступом на основе ролей | Документация Майкрософт
description: Узнайте, как настроить права доступа с помощью управления доступом на основе ролей (RBAC) в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 7630579591b7d6e4c4179964d522dceb1023f55e
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882374"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Настройка разрешений на доступ с помощью управления доступом на основе ролей

Пользователь в Azure Stack Hub может быть читателем, владельцем или участником каждого экземпляра подписки, группы ресурсов или службы. Например, пользователь A может иметь разрешения на чтение подписки 1, но иметь права владельца виртуальной машины 7.

 - Читатель. Может просматривать все, но не может вносить изменения.
 - Участник. Может управлять всем, кроме доступа к ресурсам.
 - Владелец. Может управлять всем, включая доступ к ресурсам.
 - "Специальная": Пользователь имеет ограниченный доступ к конкретным ресурсам.

 Дополнительные сведения о создании настраиваемой роли см. в статье [Пользовательские роли в Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Настройка прав доступа для пользователя

1. Выполните вход с помощью с учетной записью с разрешениями владельца ресурса, которым вы хотите управлять.
2. В колонке ресурса щелкните значок **Доступ**![](media/azure-stack-manage-permissions/image1.png).
3. В колонке **Пользователи** щелкните **Роли**.
4. В колонке **Роли** выберите **Добавить**, чтобы добавить разрешения для пользователя.

## <a name="set-access-permissions-for-a-universal-group"></a>Настройка прав доступа для универсальной группы 

> [!Note]
> Применимо только к службам федерации Active Directory (AD FS).

1. Выполните вход с помощью с учетной записью с разрешениями владельца ресурса, которым вы хотите управлять.
2. В колонке ресурса щелкните значок **Доступ**![](media/azure-stack-manage-permissions/image1.png).
3. В колонке **Пользователи** щелкните **Роли**.
4. В колонке **Роли** выберите **Добавить**, чтобы добавить разрешения для универсальной группы Active Directory.

## <a name="next-steps"></a>Дальнейшие действия

[Добавление клиента Azure Stack Hub](azure-stack-add-new-user-aad.md)