---
title: Что собой представляет обработчик AKS в Azure Stack? | Документация Майкрософт
description: Сведения о том, как использовать программу командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure и Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 93a835b6d3eff233ccbd421930f9618325126ea4
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73057772"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Что собой представляет обработчик AKS в Azure Stack?

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете использовать программу командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure и Azure Stack. Обработчик AKS позволяет создавать, обновлять и масштабировать собственные кластеры Azure Resource Manager. С помощью обработчика можно развернуть кластер как в подключенных, так и в отключенных средах. В этой статье содержатся общие сведения об обработчике AKS, поддерживаемых сценариях его использования в Azure Stack, а также о таких операциях, как развертывание, обновление и масштабирование.

> [!IMPORTANT]
> Обработчик AKS сейчас предоставляется на условиях общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Обзор обработчика AKS

[Обработчик AKS](https://github.com/Azure/aks-engine) предоставляет программу командной строки для развертывания кластера Kubernetes и управления им в Azure и Azure Stack. Благодаря применению Azure Resource Manager обработчик AKS помогает создавать и обслуживать кластеры на основе виртуальных машин, виртуальных сетей и других ресурсов IaaS (инфраструктуры как услуги) в Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>Рекомендации по использованию обработчика AKS в Azure Stack

Прежде чем начинать работу с обработчиком AKS в Azure Stack, важно хорошо разобраться в различиях между Azure Stack и Azure. В этом разделе описаны важнейшие возможности и особенности, которые нужно учитывать при выборе Azure Stack с обработчиком AKS для управления кластером Kubernetes.

Дополнительные сведения о характеристиках обработчика AKS в Azure Stack и отличиях его использования в среде Azure см. в [этой статье](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Поддерживаемые сценарии для обработчика AKS

Служба поддержки Azure Stack поддерживает следующие сценарии работы:

1.  Обработчик AKS развертывает все артефакты кластера по рекомендациям из этой документации на основе [следующего шаблона](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  Обработчик AKS развертывает кластер в существующей виртуальной сети. Дополнительные сведения см. в статье [об использовании пользовательской виртуальной сети с обработчиком AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Операции [обновления](azure-stack-kubernetes-aks-engine-upgrade.md) и [масштабирования](azure-stack-kubernetes-aks-engine-scale.md).

Дополнительные сведения об обработчике AKS и Azure Stack см. в статье о [политиках поддержки для обработчика AKS в Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Установка обработчика AKS и развертывание кластера Kubernetes

Чтобы развернуть кластер Kubernetes с обработчиком AKS в Azure Stack, выполните следующее:

1. [Установите необходимые компоненты для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md).
2. Установите обработчик AKS на компьютер с доступом к среде Azure Stack.
     - [Установка обработчика AKS в Windows в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Установка обработчика AKS в Linux в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Разверните кластер Kubernetes с обработчиком AKS в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Установка необходимых компонентов для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md)