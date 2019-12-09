---
title: Добавление Kubernetes в Azure Stack Marketplace
titleSuffix: Azure Stack
description: Узнайте, как добавить Kubernetes в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 985d0e33fd5a15329a1a47bd2d6b11e50cd82a1c
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780819"
---
# <a name="add-kubernetes-to-azure-stack-marketplace"></a>Добавление Kubernetes в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!note]  
> Используйте элемент Kubernetes Azure Stack Marketplace для развертывания кластеров в качестве проверки концепции. Для поддерживаемых кластеров Kubernetes в Azure Stack используйте[обработчик AKS](azure-stack-aks-engine.md).

Вы можете обеспечить своим пользователям доступ к Kubernetes из Marketplace. Затем развертывание Kubernetes выполняется за одну согласованную операцию.

В этой статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager. Прежде чем начать, проверьте настройки Azure Stack и глобальные параметры клиента Azure. Соберите необходимые сведения об Azure Stack. Добавьте необходимые ресурсы в клиент и Azure Stack Marketplace. Кластер зависит от сервера Ubuntu, настраиваемого скрипта и элемента кластера Kubernetes из Azure Stack Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Создание плана, предложения и подписки

Создайте план, предложение и подписку для Kubernetes (элемент Marketplace). Можно также использовать имеющийся план и предложение.

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Создайте базовый план. Инструкции см. в статье [Создание плана в Azure Stack](azure-stack-create-plan.md).

1. Создайте предложение. Инструкции см. в статье [Создание предложения в Azure Stack](azure-stack-create-offer.md).

1. Выберите **Предложения** и найдите созданное предложение.

1. В колонке "Предложение" выберите **Обзор**.

1. Щелкните **Изменить состояние**. Щелкните **Общедоступный**.

1. Чтобы создать подписку, последовательно выберите **+ Create a resource**(+ Создать ресурс) > **Offers and Plans**(Предложения и планы) > **Подписка**.

    a. Введите значение в поле **Отображаемое имя**.

    b. Введите значение в поле **Пользователь**. Используйте учетную запись Azure AD, связанную с вашим клиентом.

    c. **Описание поставщика**

    d. В поле **Клиент каталога** укажите клиент Azure AD для Azure Stack. 

    д. Выберите **Предложение**. Выберите имя созданного предложения. Запишите идентификатор подписки.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Создание субъекта-службы и учетных данных в AD FS

Если вы используете службы федерации Active Directory (AD FS) для службы управления удостоверениями, необходимо будет создать субъект-службу для пользователей, развертывающих кластер Kubernetes. Создайте субъект-службу, используя секрет клиента. Инструкции см. в разделе [Create a service principal that uses client secret credentials](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials) (Создание субъекта-службы, использующего секрет клиента).

## <a name="add-an-ubuntu-server-image"></a>Добавление образа сервера Ubuntu

Добавьте в Azure Stack Marketplace следующий образ сервера Ubuntu:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Ubuntu Server`.

1. Выберите последнюю версию сервера. Проверьте полный номер версии и убедитесь, что вы используете последнюю версию:
    - **Издатель**: Canonical
    - **Предложение**: UbuntuServer
    - **Версия.** 16.04.201806120 (или последняя версия)
    - **SKU.** 16.04-LTS

1. Выберите **Скачать**.

## <a name="add-a-custom-script-for-linux"></a>Добавление настраиваемого скрипта для Linux

Добавьте Kubernetes в Azure Stack Marketplace, выполнив следующие действия:

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Custom Script for Linux`.

1. Выберите скрипт со следующим профилем:
   - **Предложение**: Настраиваемый скрипт для Linux 2.0
   - **Версия.** 2.0.6 (или последняя версия)
   - **Издатель**: Корпорация Майкрософт

     > [!Note]  
     > Могут отображаться несколько версий настраиваемого скрипта для Linux. Необходимо добавить последнюю версию элемента.

1. Выберите **Скачать**.

## <a name="add-kubernetes-to-the-marketplace"></a>Добавление Kubernetes в Marketplace

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Kubernetes`.

1. Выберите `Kubernetes Cluster`.

1. Выберите **Скачать**.

    > [!note]  
    > Для отображения элемента в Azure Stack Marketplace может потребоваться пять минут.

    ![Элемент Kubernetes в Azure Stack Marketplace](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Обновление или удаление Kubernetes

При обновлении Kubernetes удалите предыдущий элемент в Azure Stack Marketplace. Выполните инструкции из этой статьи по добавлению обновления Kubernetes в Azure Stack Marketplace.

Чтобы удалить Kubernetes, выполните такие действия:

1. Подключитесь к Azure Stack с помощью PowerShell в роли оператора. Инструкции см. в статье [Настройка среды PowerShell в Azure Stack](azure-stack-powershell-configure-admin.md).

2. Найдите текущий элемент кластера Kubernetes в коллекции.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Запомните имя текущего элемента, например `Microsoft.AzureStackKubernetesCluster.0.3.0`.

4. Удалите элемент, выполнив следующий командлет PowerShell:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Дополнительная информация

[Развертывание Kubernetes в Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Общие сведения о предложении служб в Azure Stack](service-plan-offer-subscription-overview.md)
