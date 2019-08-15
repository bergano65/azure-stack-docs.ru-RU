---
title: Установка Visual Studio и подключение к Azure Stack | Документация Майкрософт
description: Узнайте, как установить Visual Studio и подключиться к Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 19d532cd85244f69d1a969e92ca302822abf0133
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991680"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Установка Visual Studio и подключение к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью Visual Studio можно записывать и развертывать [шаблоны](azure-stack-arm-templates.md) Azure Resource Manager в Azure Stack. В этой статье описано, как установить Visual Studio в [Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) или на внешнем компьютере, если вы планируете использовать Azure Stack через [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>Установка Visual Studio

1. Скачайте и запустите [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Откройте **установщик веб-платформы Майкрософт**.

3. Найдите **Visual Studio Community 2015 с пакетом Microsoft Azure SDK — 2.9.6**. Щелкните **Добавить**, а затем — **Установить**.

4. Удалите компонент **Microsoft Azure PowerShell**, который установлен как часть пакета SDK для Azure.

    ![Снимок экрана: шаги по установке установщика веб-платформы](./media/azure-stack-install-visual-studio/image1.png)

5. [Установите PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md).

6. Перезагрузите компьютер после завершения установки.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Подключение к Azure Stack с помощью Azure AD

1. Запустите Visual Studio.

2. В меню **Представление** выберите **Cloud Explorer**.

3. На новой панели выберите **Добавить учетную запись** и выполните вход с помощью учетных данных Azure Active Directory (Azure AD).  

    ![Снимок экрана. Cloud Explorer после входа и подключения к Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

После входа вы можете [развертывать шаблоны](azure-stack-deploy-template-visual-studio.md) или просматривать доступные типы и группы ресурсов для создания собственных шаблонов.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Подключение к Azure Stack с помощью служб федерации Active Directory (AD FS)

1. Запустите Visual Studio.

2. В разделе **Инструменты** выберите **Параметры**.

3. Разверните раскрывающееся меню **Среда** в **области навигации** и выберите **Учетные записи**.

4. Выберите **Добавить** и введите конечную точку Azure Resource Manager пользователя. Если вам нужен Пакет средств разработки Azure Stack (ASDK), перейдите по следующему адресу: `https://management.local.azurestack/external`.  Если вам нужны интегрированные системы Azure Stack, используйте адрес: `https://management.[Region}.[External FQDN]`.

    ![Добавление новой конечной точки обнаружения облака Azure](./media/azure-stack-install-visual-studio/image5.png)

5. Выберите **Добавить**.  

    Visual Studio вызывает Azure Resource Manager и обнаруживает конечные точки, включая конечную точку аутентификации для служб федерации Azure Directory (AD FS).

    ![Снимок экрана. Cloud Explorer после входа и подключения к Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. В меню **Представление** выберите **Cloud Explorer**.

7. Выберите **Добавить ученую запись** и выполните вход с помощью учетных данных служб федерации Active Directory.  

    ![Вход в Visual Studio в Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer запрашивает доступные подписки. Вы можете выбрать доступную подписку для управления.

    ![Выбор подписок Azure для управления в Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Просмотрите имеющиеся ресурсы, группы ресурсов или шаблоны развертывания.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в рекомендациях по [параллельной](/visualstudio/install/install-visual-studio-versions-side-by-side) установке разных версий Visual Studio.
- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md).
