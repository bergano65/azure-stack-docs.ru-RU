---
title: Добавление новой учетной записи пользователя Azure Stack Hub в Azure Active Directory
description: Узнайте, как создать учетную запись пользователя в Azure Active Directory, чтобы изучить пользовательский портал.
author: JustinHall
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: b7bfcf97df22f5ca0d1dcaa7c9687079656af840
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890159"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Добавление новой учетной записи пользователя Azure Stack Hub в Azure Active Directory (Azure AD)

Перед тем, как протестировать предложения и планы, а также создать ресурсы, вам понадобится учетная запись пользователя. Создайте учетную запись пользователя в клиенте Azure AD с помощью портал Azure или PowerShell.

## <a name="create-user-account-using-the-azure-portal"></a>Создание учетной записи пользователя, используя портал Azure

Для использования портала Azure необходима подписка Azure.

1. Войдите в [Azure](https://portal.azure.com).
2. На левой панели навигации выберите **Active Directory** и переключитесь на каталог, который хотите использовать для Azure Stack Hub, или создайте новый.
3. Выберите **Azure Active Directory** > **Пользователи** > **Новый пользователь**.

    !["Пользователи" — страница всех пользователей с выделенным новым пользователем](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. На странице **Пользователь** укажите требуемые данные.

    ![Добавьте пользователя, страницу пользователя с информацией о нем](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Имя (обязательно)** . Имя и фамилия нового пользователя. Например, Мэри Паркер.
   - **Имя пользователя (обязательно)** . Имя нового пользователя. Например, mary@contoso.com.
       В доменной части имени пользователя должно использоваться либо начальное доменное имя по умолчанию, <_yourdomainname_>.onmicrosoft.com, либо имя личного домена, например contoso.com. Дополнительные сведения о создании имени личного домена см. в статье [Добавление имени личного домена с помощью портала Azure Active Directory](/azure/active-directory/fundamentals/add-custom-domain).
   - **Profile** (Профиль). При желании вы можете добавить дополнительную информацию о пользователе. Вы можете также добавить информацию о пользователе позже. Дополнительные сведения о добавлении информации о пользователе см. в разделе [Добавление или изменение данных профиля пользователя с помощью Azure Active Directory](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Роль каталога**. Выберите **Пользователь**.

5. Установите флажок **Показать пароль** и скопируйте автоматически созданный пароль, указанный в поле **Пароль**. Этот пароль понадобится для начального процесса входа в систему.

6. Нажмите кнопку **создания**.

    Пользователь создан и добавлен к вашему клиенту Azure AD.

7. Войдите на портал Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
8. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра пользовательского портала.

## <a name="create-a-user-account-using-powershell"></a>Создание учетной записи пользователя с помощью PowerShell

Если у вас нет подписки Azure, вы не сможете использовать портал Azure для добавления учетной записи клиента. В таком случае вы можете использовать модуль Azure AD для Windows PowerShell.

> [!NOTE]
> Если вы работаете с учетной записью Майкрософт для развертывания ASDK, вы не сможете использовать Azure AD PowerShell для создания учетной записи клиента.

1. Установите **64-разрядную версию**[помощника по входу в Microsoft Online Services для профессиональной версии RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Установите модуль Microsoft Azure AD для Windows PowerShell, сделав следующее.

    - Откройте командную строку Windows PowerShell с повышенными привилегиями (запустите Windows PowerShell как администратор).
    - Выполните команду **Install-Module MSOnline**.
    - Если появится запрос на установку поставщика NuGet, выберите **Y** (Да) и нажмите клавишу **ВВОД**.
    - Если появится запрос на установку модуля из PSGallery, выберите **Y** (Да) и нажмите клавишу **ВВОД**.

3. Выполните следующие командлеты.

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Войдите в Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
2. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра пользовательского портала.

## <a name="next-steps"></a>Дальнейшие действия

[Добавление пользователей Azure Stack Hub в AD FS](azure-stack-add-users-adfs.md)
