---
title: Как получить сведения о проверке подлинности для Azure Stack Hub
description: Узнайте, как получить сведения о проверке подлинности для Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 845be919279107e88a922dfd180e3fc8794e1a89
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79293897"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Получение сведений о проверке подлинности для Azure Stack Hub

Для проверки подлинности в Azure Stack Hub необходимо указать идентификатор подписки, идентификатор клиента и расположение, а также конечную точку Resource Manager для Azure Stack Hub. Эти значения можно получить из [конечной точки Resource Manager для Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint). Их также можно получить, выполнив действия, описанные в этой статье.

## <a name="values-needed-to-authenticate"></a>Значения, необходимые для проверки подлинности

Потребуются следующие сведения:

-   **Идентификатор подписки**  

    Идентификатор подписки используется для доступа к предложениям в Azure Stack Hub.

-   **Идентификатор клиента**

    Каталог — это контейнер, который содержит информацию о пользователях, приложениях, группах и субъектах-службах. Клиент каталога — это организация, например корпорация Майкрософт или ваша компания.

-   **Расположение**

    Расположение или регион — это набор центров обработки данных, развернутых в пределах периметра, определяемого задержкой, и соединенных между собой выделенной региональной сетью с низкой задержкой. При использовании Azure Stack Hub ваше расположение может включать в себя локальный центр обработки данных, а не регион Azure.

-   **Конечная точка Resource Manager для Azure Stack Hub**

    Microsoft Azure Resource Manager — это платформа управления, которая дает администраторам возможность развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

## <a name="get-the-subscription-id"></a>Получение идентификатора подписки

Чтобы получить идентификатор подписки, сделайте следующее:

1.  Войдите на портал пользователя Azure Stack Hub.

2.  Выбор пункта **Все службы**.

    > ![Сведения о проверке подлинности Azure Stack Hub: идентификатор клиента и подписки](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  Выберите **Подписки**.

4.  Выберите идентификатор подписки, которую вы хотите использовать.

5.  Скопируйте **идентификатор подписки** из раздела **Обзор**.

## <a name="get-the-tenant-id"></a>Получение идентификатора клиента

Чтобы получить идентификатор клиента, сделайте следующее:

1.  Войдите на портал пользователя Azure Stack Hub.

2.  Наведите указатель мыши на имя пользователя в правом верхнем углу колонки.

3.  **Идентификатор каталога** — это идентификатор клиента.

## <a name="get-the-azure-resource-manager-endpoint"></a>Получение конечной точки Azure Resource Manager

Конечная точка Azure Resource Manager — это конечная точка метаданных для службы развертывания и управления Azure Stack Hub. Она обеспечивает уровень управления, позволяющий создавать, обновлять и удалять ресурсы в подписке Azure.

Для интегрированной системы URL-адрес конечной точки Azure Resource Manager следующий:<br>`https://management.<location>.<fqdn>`

Для получения конечной точки метаданных, указывающей на такие свойства, как конечная точка коллекции, конечная точка графа, конечная точка портала, конечная точка входа и аудитории, URL-адрес будет следующим: `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше об использовании [Azure Stack Hub Resource Manager](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) в Azure Stack Hub.
