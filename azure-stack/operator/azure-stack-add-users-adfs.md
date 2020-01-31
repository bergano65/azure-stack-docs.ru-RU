---
title: Добавление пользователей Azure Stack Hub в AD FS
description: Узнайте, как добавить пользователей Azure Stack Hub для развертываний служб федерации Active Directory (AD FS).
author: ihenkel
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 7d11ae849a5ecc51c1506e8d978510884b752a9a
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890380"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Добавление новой учетной записи пользователя Azure Stack Hub в службы федерации Active Directory (AD FS)

Оснастку **Пользователи и компьютеры Active Directory** можно использовать для добавления дополнительных пользователей в среду Azure Stack Hub, используя AD FS в качестве поставщика удостоверений.

## <a name="add-windows-server-active-directory-users"></a>Добавление пользователей Windows Server Active Directory

1. Войдите на компьютер с учетной записью, предоставляющей доступ к инструментам администрирования Windows, и откройте новую консоль управления (MMC).
2. Щелкните **Файл > Add or remove snap-in** (Добавить или удалить оснастку).

   > [!TIP]
   > Замените *directory-domain* (домен directory) доменом, который соответствует вашему каталогу. 

3. Выберите **Active Directory — пользователи и компьютеры** > *directory-domain* > **Пользователи**.
4. Выберите **Действия** > **Создать** > **Пользователь**.
5. В окне "Создать объект — пользователь" укажите сведения о пользователе. Выберите **Далее**.
6. Укажите и подтвердите пароль.
7. Нажмите кнопку **Далее**, чтобы заполнить значения. Выберите **Готово**, чтобы создать пользователя.


## <a name="next-steps"></a>Дальнейшие действия

[Использование удостоверения приложения для доступа к ресурсам Azure Stack Hub](azure-stack-create-service-principals.md)