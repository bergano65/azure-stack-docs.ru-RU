---
title: Настройка разрешений на доступ с помощью управления доступом на основе ролей | Документация Майкрософт
description: Узнайте, как настроить разрешения на доступ с помощью управления доступом на основе ролей (RBAC) в Azure Stack.
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
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: fa4e836a2c7cd5b59a6234a05efcc1cface12620
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277050"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Настройка разрешений на доступ с помощью управления доступом на основе ролей

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пользователь в Azure Stack может быть читателем, владельцем или участником каждого экземпляра подписки, группы ресурсов или службы. Например, пользователь A может иметь разрешения на чтение подписки 1, но иметь права владельца виртуальной машины 7.

 - Читатель. Может просматривать все, но не может вносить изменения.
 - Участник. Может управлять всем, кроме доступа к ресурсам.
 - Владелец. Может управлять всем, включая доступ к ресурсам.

## <a name="set-access-permissions-for-a-user"></a>Настройка прав доступа для пользователя

1. Выполните вход с помощью с учетной записью с разрешениями владельца ресурса, которым вы хотите управлять.
2. В колонке ресурса щелкните значок **Доступ** ![](media/azure-stack-manage-permissions/image1.png).
3. В колонке **Пользователи** щелкните **Роли**.
4. В колонке **Роли** выберите **Добавить**, чтобы добавить разрешения для пользователя.

## <a name="set-access-permissions-for-a-universal-group"></a>Настройка прав доступа для универсальной группы 

> [!Note]
> Применимо только к службам федерации Active Directory (AD FS).

1. Выполните вход с помощью с учетной записью с разрешениями владельца ресурса, которым вы хотите управлять.
2. В колонке ресурса щелкните значок **Доступ** ![](media/azure-stack-manage-permissions/image1.png).
3. В колонке **Пользователи** щелкните **Роли**.
4. В колонке **Роли** выберите **Добавить**, чтобы добавить разрешения для универсальной группы Active Directory.

## <a name="next-steps"></a>Дополнительная информация

[Добавление клиента Azure Stack](azure-stack-add-new-user-aad.md)