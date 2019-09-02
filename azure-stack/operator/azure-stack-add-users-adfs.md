---
title: Добавление пользователей Azure Stack в AD FS | Документация Майкрософт
description: Узнайте, как добавить пользователей Azure Stack для развертываний служб федерации Active Directory (AD FS).
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
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118634"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Добавление пользователей Azure Stack в AD FS
С помощью оснастки **Пользователи и компьютеры Active Directory** можно добавить дополнительных пользователей в среду Azure Stack, используя службы федерации Active Directory (AD FS) в качестве поставщика удостоверений.

## <a name="add-windows-server-active-directory-users"></a>Добавление пользователей Windows Server Active Directory
> [!TIP]
> В этом примере используется пакет по умолчанию azurestack.local ASDK для Active Directory. 

1. Войдите на компьютер с учетной записью, предоставляющей доступ к инструментам администрирования Windows, и откройте новую консоль управления (MMC).
2. Щелкните **Файл > Add or remove snap-in** (Добавить или удалить оснастку).
3. Выберите **Active Directory — пользователи и компьютеры** > **AzureStack.local** > **Пользователи**.
4. Выберите **Действия** > **Создать** > **Пользователь**.
5. В окне "Создать объект — пользователь" укажите сведения о пользователе. Щелкните **Далее**.
6. Укажите и подтвердите пароль.
7. Нажмите кнопку **Далее**, чтобы заполнить значения. Выберите **Готово**, чтобы создать пользователя.


## <a name="next-steps"></a>Дополнительная информация
[Создание субъектов-служб](azure-stack-create-service-principals.md)