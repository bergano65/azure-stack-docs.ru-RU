---
title: Добавление новой учетной записи клиента Azure Stack в Azure Active Directory | Документация Майкрософт
description: Развернув Пакет средств разработки Azure Stack, создайте хотя бы одну учетную запись клиента, чтобы вы могли просматривать портал клиента.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 70151d7793ef1f58b544517cecb7aa53bf5b3041
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691395"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Добавление новой учетной записи клиента Azure Stack в Azure Active Directory

[Развернув Пакет средств разработки Azure Stack](../asdk/asdk-install.md), создайте учетную запись клиента, чтобы вы могли просматривать портал клиента и тестировать предложения и планы. Учетную запись можно создать с помощью [портала Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) или PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Создание учетной записи клиента Azure Stack с помощью портала Azure

Для использования портала Azure необходима подписка Azure.

1. Войдите в [Azure](https://portal.azure.com).
2. На левой панели навигации выберите **Active Directory** и переключитесь на каталог, который вы хотите использовать для Azure Stack, или создайте новый.
3. Выберите **Azure Active Directory** > **Пользователи** > **Новый пользователь**.

    !["Пользователи" — страница всех пользователей с выделенным новым пользователем](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. На странице **Пользователь** укажите требуемые данные.

    ![Добавьте пользователя, страницу пользователя с информацией о нем](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Имя (обязательно).** Имя и фамилия нового пользователя. Например, Мэри Паркер.
   - **Имя пользователя (обязательно).** Имя нового пользователя. Например, mary@contoso.com.
       В доменной части имени пользователя должно использоваться либо начальное доменное имя по умолчанию, <_yourdomainname_>.onmicrosoft.com, либо имя личного домена, например contoso.com. Дополнительные сведения о создании имени личного домена см. в статье [How to add a custom domain name to Azure Active Directory](/azure/active-directory/fundamentals/add-custom-domain) (Как добавить имя личного домена в Azure Active Directory).
   - **Профиль.** При желании вы можете добавить дополнительную информацию о пользователе. Вы также можете добавить информацию о пользователе позже. Дополнительные сведения о добавлении информации о пользователе см. в разделе [How to add or change user profile information](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal) (Как добавлять и изменять сведения в профиле пользователя).
   - **Роль каталога.**  выберите **Пользователь**.

5. Проверьте **Показать пароль** и скопируйте автоматически созданный пароль, указанный в поле **Пароль**. Этот пароль понадобится для начального процесса входа в систему.

6. Нажмите кнопку **Создать**.

    Пользователь создан и добавлен к вашему клиенту Azure AD.

7. Войдите на портал Microsoft Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
8. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра портала клиента.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Создание учетной записи пользователя Azure Stack с помощью PowerShell

Если у вас нет подписки Azure, вы не сможете использовать портал Azure для добавления учетной записи клиента. В таком случае вы можете использовать модуль Azure Active Directory для Windows PowerShell.

> [!NOTE]
> Если вы работаете с учетной записью Майкрософт для развертывания Пакета средств разработки Azure Stack, вы не сможете использовать Azure AD PowerShell для создания учетной записи клиента. 

1. Установите **64-разрядную версию** [помощника по входу в Microsoft Online Services для профессиональной версии RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Установите модуль Microsoft Azure Active Directory для Windows PowerShell, сделав следующее:

    - Откройте окно командной строки Windows PowerShell с повышенными привилегиями (запустите Windows PowerShell как администратор).
    - Выполните команду **Install-Module MSOnline**.
    - Если появится запрос на установку поставщика NuGet, выберите **Y** и **ВВОД**.
    - Если появится запрос на установку модуля из PSGallery, выберите **Y** и **ВВОД**.

3. Выполните следующие командлеты.

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Войдите в Microsoft Azure с помощью новой учетной записи. В ответ на запрос измените пароль.
2. Войдите в `https://portal.local.azurestack.external` с помощью новой учетной записи для просмотра портала клиента.

## <a name="next-steps"></a>Дополнительная информация

[Добавление пользователей Azure Stack в AD FS](azure-stack-add-users-adfs.md)
