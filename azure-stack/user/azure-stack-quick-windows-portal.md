---
title: Создание виртуальной машины Windows с помощью портала Azure Stack | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Windows Server 2016 с помощью портала Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823924"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Узнайте, как создать виртуальную машину Windows Server 2016 с помощью портала Azure Stack.

> [!NOTE]  
> Снимки экрана в этой статье обновлены в соответствии с пользовательским интерфейсом, представленным в Azure Stack версии 1808. В версии 1808 добавлена поддержка *управляемых дисков* помимо неуправляемых дисков. При использовании более ранней версии некоторые экраны (например, при выборе диска) будут отличаться от изображений в этой статье.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Вход на портал Azure Stack

Войдите на портал Azure Stack. Адрес портала Azure Stack зависит от того, к какому продукту Azure Stack вы подключаетесь:

* Если вам нужен Пакет средств разработки Azure Stack (ASDK), перейдите по адресу https://portal.local.azurestack.external.
* При работе с интегрированной системой Azure Stack используйте URL-адрес, предоставленный оператором Azure Stack.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Щелкните **+ Создать ресурс** > **Вычисление** > **Windows Server 2016 Datacente – Pay-as-you-use** (Windows Server 2016 Datacenter (оплата по мере использования))  > **Создать**. <br> Если вы не видите вариант **Windows Server 2016 Datacenter – Pay-as-you-use** (Windows Server 2016 Datacenter (оплата по мере использования)), обратитесь к оператору Azure Stack и попросите его добавить этот образ в marketplace, как описано в статье [Добавление образа виртуальной машины Windows Server 2016 в Azure Stack Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Создание виртуальной машины Windows на портале](media/azure-stack-quick-windows-portal/image01.png)

2. В колонке **Основные сведения** введите **имя**, **имя пользователя** и **пароль**. Выберите **подписку**. Создайте **группу ресурсов** или выберите существующую, укажите **расположение**, а затем нажмите **ОК**.

    ![Настройка основных параметров.](media/azure-stack-quick-windows-portal/image02.png)

3. В разделе **Размер** выберите **D1 Standard**, а затем щелкните **Выбрать**.  

    ![Выбор размера виртуальной машины](media/azure-stack-quick-windows-portal/image03.png)

4. На странице **Параметры** внесите необходимые изменения в значения по умолчанию.
   - Начиная с Azure Stack версии 1808, можно настроить **хранилище**, в котором можно использовать *управляемые диски*. В прежних версиях были доступны только неуправляемые диски.  

   ![Настройка параметров виртуальной машины](media/azure-stack-quick-windows-portal/image04.png)  

   Завершив настройку параметров, щелкните **ОК**, чтобы продолжить.

5. В разделе **Сводка** нажмите кнопку **ОК**, чтобы создать виртуальную машину.
    ![Просмотр сводки и создание виртуальной машины](media/azure-stack-quick-windows-portal/image05.png)

6. Чтобы увидеть новую виртуальную машину, щелкните **Все ресурсы**, выполните поиск виртуальной машины по имени, а затем выберите его в результатах поиска.

    ![Просмотр виртуальной машины](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив использование виртуальной машины, удалите ее и вместе со связанными ресурсами. Для этого выберите группу ресурсов на странице виртуальной машины и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы развернули простую виртуальную машину Windows Server. Дополнительные сведения о виртуальных машинах Azure Stack см. в статье [Рекомендации по использованию виртуальных машин в Azure Stack](azure-stack-vm-considerations.md).
