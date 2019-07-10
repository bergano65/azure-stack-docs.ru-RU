---
title: Развертывание Kubernetes для работы с контейнерами Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть Kubernetes для работы с контейнерами Azure Stack.
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 16bbd9b7554eb9e42bac2b0f5694f148589b94ed
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308699"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Узнайте, как развернуть Kubernetes для работы с контейнерами Azure Stack.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!Note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии. Сейчас в предварительной версии не поддерживаются сценарии работы с Azure Stack в автономном режиме. Для сценариев разработки и тестирования используйте только элемент из marketplace.

Вы можете следовать инструкциям, описанным в этой статье, чтобы развернуть и настроить ресурсы для Kubernetes за одну согласованную операцию. В этих инструкциях используется шаблон решения Azure Resource Manager. Вам потребуется собрать все необходимые сведения об установке Azure Stack, создать шаблон и выполнить развертывание в облаке. Шаблон Azure Stack не использует управляемую службу AKS, представляемую в глобальной среде Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes и контейнеры

Вы можете установить Kubernetes с помощью шаблонов Azure Resource Manager, созданных обработчиком AKS в Azure Stack. [Kubernetes](https://kubernetes.io) — это система с открытым кодом для автоматизации развертывания, масштабирования и управления приложениями в контейнерах. [Контейнер](https://www.docker.com/what-container) — это образ. Образ контейнера похож на виртуальную машину, но в отличие от нее в контейнер помещаются ресурсы, необходимые только для запуска приложения, например код, среда выполнения для этого кода, определенные библиотеки и параметры.

Kubernetes можно использовать для следующих целей:

- Разработка обновляемых приложений с высокой масштабируемостью, которые можно развернуть в считаные секунды. 
- Упрощение структуры приложения и повышение его надежности с помощью различных приложений Helm. [Helm](https://github.com/kubernetes/helm) — это средство упаковки с открытым исходным кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом.
- Простой мониторинг и диагностика работоспособности приложений.

Вам будут выставляться счета только за использование вычислительных ресурсов, необходимых для узлов, поддерживающих кластер. Дополнительные сведения см. в статье [Потребление ресурсов и выставление счетов в Azure Stack](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Развертывание Kubernetes для работы с контейнерами

Инструкции по развертыванию кластера Kubernetes в Azure Stack будут зависеть от вашей службы управления удостоверениями. Проверьте решение по управлению удостоверениями, используемое при установке Azure Stack. Обратитесь к администратору Azure Stack, чтобы проверить службу управления удостоверениями.

- **Azure Active Directory (Azure AD).**  
Инструкции по установке кластера с использованием Azure AD см. в статье [Deploy Kubernetes to Azure Stack using Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md) (Развертывание Kubernetes в Azure Stack с помощью Azure Active Directory).

- **Службы федерации Active Directory (AD FS)**  
Инструкции по установке кластера с использованием AD FS см. в статье [Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Подключение к кластеру

Теперь все готово для подключения к кластеру. Главный кластер можно найти в группе кластерных ресурсов. Он называется `k8s-master-<sequence-of-numbers>`. Подключитесь к главному кластеру, используя клиент SSH. Управлять кластером можно с помощью **kubectl**, клиента командной строки Kubernetes. Инструкции см. на сайте [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Можно также использовать диспетчер пакетов **Helm** для установки и развертывания приложений в кластер. Инструкции по установке и использованию Helm с кластером см. на сайте [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Дополнительная информация

[Получение доступа к панели мониторинга Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-dashboard.md)

[Добавление Kubernetes в Marketplace (для оператора Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Deploy Kubernetes to Azure Stack using Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md) (Развертывание Kubernetes в Azure Stack с помощью Azure Active Directory)

[Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes в Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
