---
title: Что собой представляет обработчик AKS в Azure Stack? | Документация Майкрософт
description: Узнайте, как использовать программу командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure и Azure Stack.
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
ms.openlocfilehash: 22779072b2dfed018a2ff6d5eac5bf2c294ccd31
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595122"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Что собой представляет обработчик AKS в Azure Stack?

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете использовать программу командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure и Azure Stack. Обработчик AKS позволяет создавать, обновлять и масштабировать собственные кластеры Azure Resource Manager. С помощью обработчика можно развернуть кластер как в подключенных, так и в отключенных средах. В этой статье содержатся общие сведения об обработчике AKS, поддерживаемых сценариях его использования в Azure Stack, а также таких операциях, как развертывание, обновление и масштабирование.

> [!IMPORTANT]
> Обработчик AKS сейчас предоставляется на условиях общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Общие сведения об обработчике AKS

[Обработчик AKS](https://github.com/Azure/aks-engine) предоставляет программу командной строки для развертывания кластера Kubernetes и управления им в Azure и Azure Stack. Благодаря применению Azure Resource Manager обработчик AKS помогает создавать и обслуживать кластеры на основе виртуальных машин, виртуальных сетей и других ресурсов IaaS (инфраструктуры как услуги) в Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>Рекомендации по использованию обработчика AKS в Azure Stack

Прежде чем начинать работу с обработчиком AKS в Azure Stack, нужно определить отличия между Azure Stack и Azure. В этом разделе описаны возможности и особенности, которые нужно учитывать при использовании Azure Stack с обработчиком AKS для управления кластером Kubernetes.

См. сведения о [характеристиках и особенностях использования обработчика AKS в Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Поддерживаемые сценарии для обработчика AKS

Служба поддержки Azure Stack поддерживает следующие сценарии работы:

1.  Обработчик AKS развертывает все артефакты кластера согласно инструкциям из этой документации на основе [следующего шаблона](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  Обработчик AKS развертывает кластер в существующей виртуальной сети. См. сведения об [использовании пользовательской виртуальной сети с обработчиком AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Операции [обновления](azure-stack-kubernetes-aks-engine-upgrade.md) и [масштабирования](azure-stack-kubernetes-aks-engine-scale.md).

См. сведения о [политиках поддержки для обработчика AKS в Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Установка обработчика AKS и развертывание кластера Kubernetes

Чтобы развернуть кластер Kubernetes с обработчиком AKS в Azure Stack, сделайте следующее:

1. [Установка необходимых компонентов для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Установите обработчик AKS на виртуальной машине с доступом к среде Azure Stack.
     - [Установка обработчика AKS в Windows в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Установка обработчика AKS в Azure Stack в Linux](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Разверните кластер Kubernetes с обработчиком AKS в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Установка необходимых компонентов для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md)