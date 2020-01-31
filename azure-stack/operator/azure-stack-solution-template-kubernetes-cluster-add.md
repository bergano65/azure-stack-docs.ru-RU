---
title: Добавление Kubernetes в Azure Stack Hub Marketplace
titleSuffix: Azure Stack Hub
description: Узнайте, как добавить Kubernetes в Azure Stack Hub Marketplace.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: bbd99b7085f96e24f2d1b2a74795be2a1584b656
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881290"
---
# <a name="add-kubernetes-to-azure-stack-hub-marketplace"></a>Добавление Kubernetes в Azure Stack Hub Marketplace

> [!note]  
> Используйте элемент Kubernetes Azure Stack Hub Marketplace для развертывания кластеров в качестве проверки концепции. Для поддерживаемых кластеров Kubernetes в Azure Stack Hub используйте [обработчик AKS](azure-stack-aks-engine.md).

Вы можете обеспечить своим пользователям доступ к Kubernetes из Marketplace. Затем развертывание Kubernetes выполняется за одну согласованную операцию.

В этой статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager. Прежде чем начать, проверьте настройки Azure Stack Hub и глобальные параметры клиента Azure. Соберите необходимые сведения об Azure Stack Hub. Добавьте необходимые ресурсы в клиент и Azure Stack Hub Marketplace. Кластер зависит от сервера Ubuntu, настраиваемого скрипта и элемента кластера Kubernetes из Azure Stack Hub Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Создание плана, предложения и подписки

Создайте план, предложение и подписку для Kubernetes (элемент Marketplace). Можно также использовать имеющийся план и предложение.

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Создайте базовый план. Инструкции см. в статье [Создание плана в Azure Stack Hub](azure-stack-create-plan.md).

1. Создайте предложение. Инструкции см. в статье [Создание предложения в Azure Stack Hub](azure-stack-create-offer.md).

1. Выберите **Предложения** и найдите созданное предложение.

1. В колонке "Предложение" выберите **Обзор**.

1. Щелкните **Изменить состояние**. Щелкните **Общедоступный**.

1. Чтобы создать подписку, последовательно выберите **+ Create a resource**(+ Создать ресурс) > **Offers and Plans**(Предложения и планы) > **Подписка**.

    а. Введите значение в поле **Отображаемое имя**.

    b. Введите значение в поле **Пользователь**. Используйте учетную запись Azure AD, связанную с вашим клиентом.

    c. **Описание поставщика**

    d. В поле **Клиент каталога** укажите клиент Azure AD для Azure Stack Hub. 

    д) Выберите **Предложение**. Выберите имя созданного предложения. Запишите идентификатор подписки.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Создание субъекта-службы и учетных данных в AD FS

Если вы используете службы федерации Active Directory (AD FS) для службы управления удостоверениями, необходимо будет создать субъект-службу для пользователей, развертывающих кластер Kubernetes. Создайте субъект-службу, используя секрет клиента. Инструкции см. в разделе [Create a service principal that uses client secret credentials](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials) (Создание субъекта-службы, использующего секрет клиента).

## <a name="add-an-ubuntu-server-image"></a>Добавление образа сервера Ubuntu

Добавьте в Azure Stack Hub Marketplace следующий образ сервера Ubuntu:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Введите `Ubuntu Server`.

1. Выберите последнюю версию сервера. Проверьте полный номер версии и убедитесь, что вы используете последнюю версию:
    - **Издатель**: Canonical
    - **Предложение**: UbuntuServer
    - **Версия.** 16.04.201806120 (или последняя версия)
    - **SKU**: 16.04-LTS

1. Выберите **Скачать**.

## <a name="add-a-custom-script-for-linux"></a>Добавление настраиваемого скрипта для Linux

Добавьте Kubernetes из Azure Stack Hub Marketplace, выполнив приведенные ниже действия.

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Введите `Custom Script for Linux`.

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

1. Введите `Kubernetes`.

1. Выберите `Kubernetes Cluster`.

1. Выберите **Скачать**.

    > [!note]  
    > Для отображения элемента в Azure Stack Hub Marketplace может потребоваться пять минут.

    ![Элемент Kubernetes в Azure Stack Hub Marketplace](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Обновление или удаление Kubernetes

При обновлении элемента Kubernetes удалите предыдущий элемент в Azure Stack Hub Marketplace. Выполните инструкции из этой статьи по добавлению обновления Kubernetes в Azure Stack Hub Marketplace.

Чтобы удалить Kubernetes, выполните такие действия:

1. Подключитесь к Azure Stack Hub с помощью PowerShell в роли оператора. Инструкции см. в статье [Подключение к Azure Stack Hub с помощью PowerShell](azure-stack-powershell-configure-admin.md).

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

## <a name="next-steps"></a>Дальнейшие действия

[Статья о развертывании Kubernetes в Azure Stack Hub](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Общие сведения о предложении служб в Azure Stack Hub](service-plan-offer-subscription-overview.md)
