---
title: Создание роли для регистрации для Azure Stack
description: Сведения о том, как создать настраиваемую роль, чтобы избежать использования учетной записи глобального администратора для регистрации.
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
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 09a75b7aad3d0a9a919883641d8dc901353a5048
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251915"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Создание роли для регистрации для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В сценариях, когда вы не хотите предоставлять разрешения владельца в подписке Azure, можно создать настраиваемую роль для назначения разрешений учетной записи пользователя для регистрации Azure Stack.

> [!WARNING]
> Это не функция безопасности. Прибегайте к этому в тех случаях, когда вы хотите, чтобы ограничения предотвращали случайные изменения в подписке Azure. Если пользователь делегирует права настраиваемой роли, он получает права на изменение разрешений и на повышение уровня прав. Назначайте настраиваемую роль только тем пользователям, которым вы доверяете.

При регистрации Azure Stack учетная запись регистрации требует следующие разрешения Azure Active Directory и подписки Azure.

* **Разрешения для регистрации приложения в клиенте Azure Active Directory.** Разрешения для регистрации приложения есть у администраторов. Разрешение для пользователя — это глобальный параметр для всех пользователей в клиенте. Чтобы просмотреть или изменить параметры, ознакомьтесь с разделом [Необходимые разрешения](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    *Пользователь может регистрировать приложения*. Параметру должно быть присвоено значение **Да**, чтобы разрешить учетной записи пользователя регистрировать Azure Stack. Если для параметра регистрации приложения установлено значение **Нет**, вы не сможете использовать учетную запись пользователя для регистрации Azure Stack и должны использовать учетную запись глобального администратора.

* **Набор достаточных разрешений подписки Azure.** Пользователи в группе владельцев имеют такие разрешения. Для других учетных записей вы можете присвоить набор разрешений, назначив настраиваемую роль, как описано в следующем разделе.

## <a name="create-a-custom-role-using-powershell"></a>Создание пользовательской роли с помощью PowerShell

Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](/azure/role-based-access-control/built-in-roles#owner) или [Администратор доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator). Используйте следующий шаблон JSON, чтобы упростить определение настраиваемой роли. Этот шаблон создает пользовательскую роль, которая дает требуемый доступ на чтение и запись для регистрации Azure Stack.

1. Создайте файл JSON. Например, `C:\CustomRoles\registrationrole.json`
2. Добавьте в файл следующий код JSON. Замените `<SubscriptionID>` идентификатором своей подписки Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. В PowerShell подключитесь к Azure, чтобы использовать Azure Resource Manager. При поступлении запроса войдите в учетную запись с достаточными разрешениями, например [владельца](/azure/role-based-access-control/built-in-roles#owner) или [администратора доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Чтобы добавить роль к подписке, используйте файл шаблона **New-AzureRmRoleDefinition** в формате JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Назначение пользователя роли для регистрации

После создания настраиваемой роли регистрации назначьте пользователей роли, регистрирующих Azure Stack.

1. Войдите в учетную запись с достаточными разрешениями в подписке Azure, чтобы делегировать права, например [владельца](/azure/role-based-access-control/built-in-roles#owner) или [администратора доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. В разделе **Подписки** выберите **Управление доступом (IAM) > Добавить назначение ролей**.
3. В поле **Роль** выберите настраиваемую роль, для которой вы создали *роль для регистрации Azure Stack*.
4. Выберите пользователей, которых хотите назначить роли.
5. Щелкните **Сохранить**, чтобы назначить выбранных пользователей роли.

    ![Выбор пользователей для назначения роли](media/azure-stack-registration-role/assign-role.png)

Дополнительные сведения об использовании настраиваемых ролей см. в статье [Управление доступом с помощью RBAC и портала Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Дополнительная информация

[Регистрация Azure Stack в Azure](azure-stack-registration.md)
