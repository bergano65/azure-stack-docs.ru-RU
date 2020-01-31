---
title: Создание виртуальной машины Windows с помощью портала Azure Stack Hub
description: Узнайте, как создать виртуальную машину Windows Server 2016 с помощью портала Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 0792d0dffa8a61194b0f6725aba9222d69c8634e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883293"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack Hub

Узнайте, как создать виртуальную машину Windows Server 2016 с помощью портала Azure Stack Hub.

> [!NOTE]  
> Снимки экрана в этой статье обновлены в соответствии с пользовательским интерфейсом, представленным в Azure Stack Hub версии 1808. В версии 1808 добавлена поддержка *управляемых дисков* помимо неуправляемых дисков. При использовании более ранней версии некоторые экраны (например, при выборе диска) будут отличаться от изображений в этой статье.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Войдите на портал Azure Stack Hub.

Войдите на портал Azure Stack Hub. Адрес портала Azure Stack Hub зависит от того, к какому продукту Azure Stack Hub вы подключаетесь.

* Если вам нужен Пакет средств разработки Azure Stack (ASDK), перейдите по адресу https://portal.local.azurestack.external.
* При работе с интегрированной системой Azure Stack Hub используйте URL-адрес, предоставленный оператором Azure Stack Hub.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Щелкните **Создать ресурс** > **Вычисление**. Найдите элемент ` Windows Server 2016 Datacenter – Pay as you use`.
    Если вы не видите вариант **Windows Server 2016 Datacenter (оплата по мере использования)** , обратитесь к оператору Azure Stack и попросите его добавить этот образ в Azure Stack Hub Marketplace. Инструкции для оператора облака представлены в статье [Создание и публикация пользовательского элемента Azure Stack Hub Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter (оплата по мере использования)](./media/azure-stack-quick-windows-portal/image1.png)

1. Нажмите кнопку **создания**.

    ![Создание ресурса](./media/azure-stack-quick-windows-portal/image2.png)

1. Введите **Имя**, **Тип диска**, **Имя пользователя** и **Пароль** в разделе **Основные сведения**. Выберите **подписку**. Создайте **группу ресурсов** или выберите существующую, укажите **Расположение** и щелкните **ОК**.

    ![Создание виртуальной машины — основные сведения](./media/azure-stack-quick-windows-portal/image3.png)

1. В разделе **Размер** выберите **D1_v2**, а затем щелкните **Выбрать**.

    ![Создание виртуальной машины — размер](./media/azure-stack-quick-windows-portal/image4.png)

1. На странице **Параметры** внесите необходимые изменения в значения по умолчанию. Необходимо выбрать общедоступные входящие порты из соответствующего раскрывающегося списка. По завершении нажмите кнопку **ОК**.

    ![Создание виртуальной машины — параметры](./media/azure-stack-quick-windows-portal/image5.png)

1. В разделе **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.

    ![Создание виртуальной машины — сводка](./media/azure-stack-quick-windows-portal/image6.png)

1. Чтобы просмотреть новую виртуальную машину, выберите **Виртуальные машины**. Выполните поиск по имени виртуальной машины и выберите ее в результатах поиска.

![Создание виртуальной машины — поиск виртуальной машины](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив использование виртуальной машины, удалите ее и вместе со связанными ресурсами. Для этого выберите группу ресурсов на странице виртуальной машины и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы развернули простую виртуальную машину Windows Server. Дополнительные сведения о виртуальных машинах Azure Stack Hub см. в статье [Рекомендации по использованию виртуальных машин в Azure Stack Hub](azure-stack-vm-considerations.md).
