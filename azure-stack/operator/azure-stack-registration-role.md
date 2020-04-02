---
title: Создание пользовательской роли для регистрации Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Сведения о том, как создать пользовательскую роль, чтобы избежать использования глобального администратора для регистрации Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/27/2020
ms.author: inhenkel
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 599191a33334e8d38989abb4e293c7361855acfa
ms.sourcegitcommit: da91962d8133b985169b236fb4c84f4ef564efc8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367783"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>Создание пользовательской роли для регистрации Azure Stack Hub

> [!WARNING]
> Это не функция безопасности. Прибегайте к этому в тех случаях, когда вы хотите, чтобы ограничения предотвращали случайные изменения в подписке Azure. Если пользователь делегирует права настраиваемой роли, он получает права на изменение разрешений и на повышение уровня прав. Назначайте настраиваемую роль только тем пользователям, которым вы доверяете.

Во время регистрации Azure Stack Hub вам необходимо выполнить вход с помощью учетной записи Azure Active Directory (Azure AD). Для учетной записи требуются следующие разрешения Azure AD и подписки Azure:

* **Разрешения на регистрацию приложений в клиенте Azure AD.** У администраторов есть разрешения на регистрацию приложений. Разрешение для пользователя — это глобальный параметр для всех пользователей в клиенте. Чтобы просмотреть или изменить параметр, ознакомьтесь с разделом [создание приложения Azure AD и субъекта-службы с доступом к ресурсам](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    *Пользователь может регистрировать приложения*. Параметру должно быть присвоено значение **Да**, чтобы разрешить учетной записи пользователя регистрировать Azure Stack Hub. Если для параметра регистрации приложения установлено значение **Нет**, вы не сможете использовать учетную запись пользователя для регистрации Azure Stack Hub. Понадобится использовать учетную запись глобального администратора.

* **Набор достаточных разрешений подписки Azure.** Пользователи, которым назначена роль "Владелец", имеют все необходимые разрешения. Для других учетных записей вы можете присвоить набор разрешений, назначив настраиваемую роль, как описано в следующем разделе.

Вы можете создать пользовательскую роль (вместо использования учетной записи с правами владельца в подписке Azure) и с ее помощью назначить разрешения учетной записи пользователя с меньшими полномочиями. Эту учетную запись можно затем использовать для регистрации Azure Stack Hub.

## <a name="create-a-custom-role-using-powershell"></a>Создание пользовательской роли с помощью PowerShell

Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](/azure/role-based-access-control/built-in-roles#owner) или [Администратор доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator). Используйте следующий шаблон JSON, чтобы упростить создание пользовательской роли. Этот шаблон создает пользовательскую роль, которая дает требуемый доступ на чтение и запись для регистрации Azure Stack Hub.

1. Создайте файл JSON. Например, `C:\CustomRoles\registrationrole.json`.
2. Добавьте в файл следующий код JSON. Замените `<SubscriptionID>` идентификатором своей подписки Azure.

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read",
        "Microsoft.Authorization/locks/read",
        "Microsoft.Authorization/locks/write
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

4. Чтобы создать пользовательскую роль, используйте командлет **New-AzureRmRoleDefinition**, который указывает файл шаблона в формате JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Назначение пользователя роли для регистрации

После создания пользовательской роли для регистрации назначьте ее учетной записи пользователя, которая будет использоваться для регистрации Azure Stack Hub.

1. Войдите в учетную запись с достаточными разрешениями в подписке Azure, чтобы делегировать права, например, [владельца](/azure/role-based-access-control/built-in-roles#owner) или [администратора доступа пользователей](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. В разделе **Подписки** выберите **Управление доступом (IAM) > Добавить назначение ролей**.
3. В поле **Роль** выберите созданную пользовательскую роль: *роль для регистрации Azure Stack Hub*.
4. Выберите пользователей, которых хотите назначить роли.
5. Щелкните **Сохранить**, чтобы назначить выбранных пользователей роли.

    ![Выбор пользователей для назначения им пользовательской роли на портале Azure](media/azure-stack-registration-role/assign-role.png)

Дополнительные сведения об использовании настраиваемых ролей см. в статье [Управление доступом с помощью RBAC и портала Azure](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Дальнейшие действия

[Регистрация Azure Stack Hub в Azure](azure-stack-registration.md)
