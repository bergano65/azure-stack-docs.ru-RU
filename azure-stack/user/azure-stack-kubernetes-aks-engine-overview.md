---
title: Что собой представляет обработчик AKS в Azure Stack Hub?
description: Сведения об использовании программы командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure и Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: e1cd3e10e8805ff286afd3d919892fcdc054d208
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702641"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Что собой представляет обработчик AKS в Azure Stack Hub?

Вы можете использовать программу командной строки обработчика AKS для развертывания кластера Kubernetes и управления им в Azure Stack Hub. Обработчик AKS позволяет создавать, обновлять и масштабировать собственные кластеры Azure Resource Manager. С помощью обработчика можно развернуть кластер как в подключенных, так и в отключенных средах. В этой статье содержатся общие сведения об обработчике AKS, поддерживаемых сценариях его использования в Azure Stack Hub, а также таких операциях, как развертывание, обновление и масштабирование.

## <a name="overview-of-the-aks-engine"></a>Общие сведения об обработчике AKS

[Обработчик AKS](https://github.com/Azure/aks-engine) предоставляет программу командной строки для развертывания кластера Kubernetes и управления им в Azure и Azure Stack Hub. Благодаря применению Azure Resource Manager обработчик AKS помогает создавать и обслуживать кластеры на основе виртуальных машин, виртуальных сетей и других ресурсов IaaS (инфраструктуры как услуги) в Azure Stack Hub.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>Рекомендации по использованию обработчика AKS в Azure Stack Hub

Прежде чем начинать работу с обработчиком AKS в Azure Stack Hub, нужно определить отличия между Azure Stack Hub и Azure. В этом разделе описаны возможности и особенности, которые нужно учитывать при использовании Azure Stack Hub с обработчиком AKS для управления кластером Kubernetes.

Ознакомьтесь со сведениями о [характеристиках и особенностях использования обработчика AKS в Azure Stack Hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Поддерживаемые сценарии для обработчика AKS

Служба поддержки Azure Stack Hub поддерживает следующие сценарии работы:

1.  Обработчик AKS развертывает все артефакты кластера согласно инструкциям из этой документации на основе [следующего шаблона](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  Обработчик AKS развертывает кластер в существующей виртуальной сети. См. сведения об [использовании пользовательской виртуальной сети с обработчиком AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Операции [обновления](azure-stack-kubernetes-aks-engine-upgrade.md) и [масштабирования](azure-stack-kubernetes-aks-engine-scale.md).

Ознакомьтесь со сведениями о [политиках поддержки для обработчика AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Установка обработчика AKS и развертывание кластера Kubernetes

Чтобы развернуть кластер Kubernetes с обработчиком AKS в Azure Stack Hub, необходимо выполнить следующие задачи:

1. [Установка необходимых компонентов для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Установка обработчика AKS на виртуальной машине с доступом к среде Azure Stack Hub.
     - [Установка обработчика AKS в Windows в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Установка обработчика AKS в Azure Stack Hub в Linux](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Установка необходимых компонентов для обработчика AKS](azure-stack-kubernetes-aks-engine-set-up.md)