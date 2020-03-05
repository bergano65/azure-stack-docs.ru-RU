---
title: Развертывание Kubernetes для использования контейнеров Azure Stack Hub
description: Узнайте, как развернуть Kubernetes для работы с контейнерами Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5fa9c506b2e030adbf521191a623579f56f1ae0f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701774"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>Развертывание Kubernetes для работы с контейнерами Azure Stack Hub

> [!Note]  
> Используйте элемент Kubernetes Azure Stack Marketplace для развертывания кластеров в качестве проверки концепции. Для поддерживаемых кластеров Kubernetes в Azure Stack используйте[обработчик AKS](azure-stack-kubernetes-aks-engine-overview.md).

Вы можете следовать инструкциям, описанным в этой статье, чтобы развернуть и настроить ресурсы для Kubernetes за одну согласованную операцию. В этих инструкциях используется шаблон решения Azure Resource Manager. Вам потребуется собрать все необходимые сведения об установке Azure Stack Hub, создать шаблон и выполнить развертывание в облаке. Шаблон Azure Stack Hub не использует управляемую службу AKS, представляемую в глобальной среде Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes и контейнеры

Вы можете установить Kubernetes с помощью шаблонов Azure Resource Manager, созданных обработчиком AKS в Azure Stack Hub. [Kubernetes](https://kubernetes.io) — это система с открытым кодом для автоматизации развертывания, масштабирования и управления приложениями в контейнерах. [Контейнер](https://www.docker.com/what-container) — это образ. Образ контейнера похож на виртуальную машину, но в отличие от нее в контейнер помещаются ресурсы, необходимые только для запуска приложения, например код, среда выполнения для этого кода, определенные библиотеки и параметры.

Kubernetes можно использовать для следующих целей:

- Разработка обновляемых приложений с высокой масштабируемостью, которые можно развернуть в считаные секунды. 
- Упрощение структуры приложения и повышение его надежности с помощью различных приложений Helm. [Helm](https://github.com/kubernetes/helm) — это средство упаковки с открытым исходным кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом.
- Простой мониторинг и диагностика работоспособности приложений.

Вам будут выставляться счета только за использование вычислительных ресурсов, необходимых для узлов, поддерживающих кластер. Дополнительные сведения см. в статье [Потребление ресурсов и выставление счетов в Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Развертывание Kubernetes для работы с контейнерами

Инструкции по развертыванию кластера Kubernetes в Azure Stack Hub будут зависеть от вашей службы управления удостоверениями. Проверьте решение по управлению удостоверениями, используемое при установке Azure Stack Hub. Обратитесь к администратору Azure Stack Hub, чтобы проверить службу управления удостоверениями.

- **Azure Active Directory (Azure AD).**  
Инструкции по установке кластера с использованием Azure AD см. в статье [Развертывание Kubernetes в Azure Stack Hub с помощью Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md).

- **Службы федерации Active Directory (AD FS)**  
Инструкции по установке кластера с использованием AD FS см. в статье [Развертывание Kubernetes в Azure Stack Hub с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Подключение к кластеру

Теперь все готово для подключения к кластеру. Главный кластер можно найти в группе кластерных ресурсов. Он называется `k8s-master-<sequence-of-numbers>`. Подключитесь к главному кластеру, используя клиент SSH. Управлять кластером можно с помощью **kubectl**, клиента командной строки Kubernetes. Инструкции см. на сайте [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Можно также использовать диспетчер пакетов **Helm** для установки и развертывания приложений в кластер. Инструкции по установке и использованию Helm с кластером см. на сайте [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Дальнейшие действия

[Получение доступа к панели мониторинга Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-dashboard.md)

[Добавление Kubernetes в Azure Stack Hub Marketplace](../operator/azure-stack-solution-template-kubernetes-cluster-add.md) (для оператора Azure Stack Hub)

[Развертывание Kubernetes в Azure Stack Hub с помощью Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md)

[Развертывание Kubernetes в Azure Stack Hub с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes в Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
