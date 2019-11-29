---
title: Добавление необходимых компонентов для обработчика Службы Azure Kubernetes (AKS) в Azure Stack Marketplace | Документация Майкрософт
description: Узнайте, как добавить необходимые компоненты обработчика AKS в Azure Stack Marketplace.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ee19c6ee32960c52bcf7a4918c3d1e48406129c3
ms.sourcegitcommit: 31e04af4d405215ef200aba0b40d601fc5ca7662
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74391542"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Добавление необходимых компонентов для обработчика Службы Azure Kubernetes (AKS) в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете разрешить пользователям настраивать обработчик Службы Azure Kubernetes (AKS), добавив в Azure Stack элементы, описанные в этой статье. Затем пользователи смогут развернуть кластер Kubernetes с помощью одной согласованной операции. В этой статье описано, как обеспечить доступность обработчика AKS для пользователей в подключенных и отключенных средах. Обработчик AKS зависит от удостоверения субъекта-службы, а в Marketplace — от пользовательского расширения скриптов и базового образа AKS.

## <a name="check-your-users-service-offering"></a>Проверка предложения службы для пользователей

Пользователям потребуется план, предложение и подписка на Azure Stack с достаточным объемом свободного пространства. Пользователям часто требуется развертывать кластеры, содержащие максимум шесть виртуальных машин и состоящие из трех главных и трех рабочих узлов. Необходимо убедиться, что у них достаточный объем квоты.

Если вам нужны дополнительные сведения о планировании и настройке предложения служб, обратитесь к статье [Общие сведения о предложении служб в Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="create-a-service-principal-and-credentials"></a>Создание субъекта-службы и учетных данных

Кластеру Kubernetes потребуется субъект-служба (SPN) и разрешения на основе ролей в Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Создание имени субъекта-службы в Azure AD

Если вы используете службу Azure Active Directory (Azure AD) в качестве службы управления удостоверениями, необходимо будет создать субъект-службу для пользователей, развертывающих кластер Kubernetes. Создайте субъект-службу с использованием секрета клиента. Дополнительные сведения см. в разделе [Создание субъекта-службы, который использует учетные данные в виде секрета клиент](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Создание имени субъекта-службы в AD FS

Если вы используете службы федерации Active Directory (AD FS) для службы управления удостоверениями, необходимо будет создать субъект-службу для пользователей, развертывающих кластер Kubernetes. Создайте субъект-службу с использованием секрета клиента. Инструкции см. в разделе [Create a service principal that uses client secret credentials](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials) (Создание субъекта-службы, использующего секрет клиента).

## <a name="add-the-aks-base-image"></a>Добавление базового образа AKS

Вы можете добавить базовый образ AKS в Marketplace, получив элемент из Azure. Однако если служба Azure Stack отключена, чтобы добавить элемент, используйте инструкции, указанные в разделе [Сценарий отключенного или частично подключенного режима](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario). Добавьте элемент, указанный на пятом шаге.

Добавьте следующий элемент в Marketplace:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `AKS Base`.

1. Выберите версию образа, соответствующую версии обработчика AKS. См. список базовых образов AKS, соответствующих версии обработчика AKS и [поддерживаемым версиям Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). 

    В списке выберите:
    - **Издатель**: Служба Azure Kubernetes
    - **Предложение**: aks.
    - **Версия.** Дистрибутив с базовым образом AKS 16.04-LTS за сентябрь 2019 г. (2019.10.24 или версия, которая соответствует версии обработчика AKS)

1. Выберите **Скачать**.

## <a name="add-a-custom-script-extension"></a>Добавление расширения пользовательских скриптов

Вы можете добавить пользовательский скрипт в Marketplace, получив элемент из Azure. Однако если служба Azure Stack отключена, чтобы добавить элемент, используйте инструкции, указанные в разделе [Сценарий отключенного или частично подключенного режима](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario).  Добавьте элемент, указанный на пятом шаге.

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Custom Script for Linux`.

1. Выберите скрипт со следующим профилем:
   - **Предложение**: Настраиваемый скрипт для Linux 2.0
   - **Версия.** 2.0.6 (или последняя версия)
   - **Издатель**: Корпорация Майкрософт

     > [!Note]  
     > Могут отображаться несколько версий пользовательского скрипта для Linux. Необходимо добавить последнюю версию элемента.

1. Выберите **Скачать**.

## <a name="next-steps"></a>Дополнительная информация

[Что собой представляет обработчик AKS в Azure Stack?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Общие сведения о предложении служб в Azure Stack](service-plan-offer-subscription-overview.md)
