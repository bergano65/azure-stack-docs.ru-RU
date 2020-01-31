---
title: Использование Azure Monitor для контейнеров в Azure Stack Hub
description: Узнайте, как использовать Azure Monitor для контейнеров в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 5b3172695fd6e0536360eed2dc4e370dd8bccecc
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885248"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Использование Azure Monitor для контейнеров в Azure Stack Hub

Вы можете использовать [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/), чтобы отслеживать контейнеры в кластере Kubernetes, развернутом в Azure Stack Hub. 

> [!IMPORTANT]
> Azure Monitor для контейнеров в Azure Stack Hub в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Чтобы проверить данные о работоспособности контейнера в Azure Monitor, собирайте данные метрик памяти и процессора от доступных в Kubernetes контроллеров, узлов и контейнеров, используя API метрик. Кроме того, служба собирает журналы контейнеров. Эти журналы можно использовать для диагностики проблем в локальном кластере из Azure. Эти метрики и журналы будут собираться автоматически, когда вы настроите мониторинг для кластеров Kubernetes. Журналы ведет контейнерная версия агента Log Analytics для Linux. Azure Monitor сохраняет метрики и журналы в рабочей области Log Analytics, доступной которая доступна в вашей подписке Azure.

Есть два способа включить Azure Monitor для кластера. Для обоих способов нужно настроить в Azure рабочую область Azure Monitor Log Analytics.

## <a name="prerequisites"></a>Предварительные требования

Для обоих методов нужно выполнить [предварительные требования](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites), которые перечислены в руководстве по [использованию контейнеров в Azure Monitor](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-one"></a>Первый метод

Вы можете применить схему [Helm](https://helm.sh/), чтобы установить агенты мониторинга в кластере. См. инструкции по [использованию контейнеров в Azure Monitor](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-two"></a>Второй метод

Вы можете указать **надстройку** в JSON-файле спецификации кластера для обработчика AKS. Этот файл также называется моделью API. В надстройке укажите версию **WorkspaceGUID** в кодировке Base64 и значение **WorkspaceKey** для рабочей области Azure Log Analytics, в которой будут храниться данные мониторинга.

Поддерживаемые определения API для кластера Azure Stack Hub можно найти в примере [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). В частности, найдите свойство **addons** в **kubernetesConfig**:

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Дальнейшие действия

- См. сведения об [обработчике AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).  
- См. сведения об [использовании службы Azure Monitor для контейнеров](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).
