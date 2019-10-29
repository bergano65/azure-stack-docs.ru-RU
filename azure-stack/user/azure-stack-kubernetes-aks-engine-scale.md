---
title: Масштабирование кластера Kubernetes в Azure Stack | Документация Майкрософт
description: Сведения о том, как масштабировать кластер Kubernetes в Azure Stack.
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
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: b7d20327410ace5b5ad12d34a24ff474c1a384b0
ms.sourcegitcommit: 4a2318ad395b2a931833ccba4430d8d04cdd8819
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780477"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>Масштабирование кластера Kubernetes в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете вручную масштабировать кластер с помощью команды **scale** в обработчике AKS. Команда **scale** использует тот же файл конфигурации кластера (`apimodel.json`) в выходном каталоге, который применялся для исходного развертывания Azure Resource Manager. Обработчик выполняет операцию масштабирования для указанного пула агентов. По завершении операции масштабирования обработчик обновляет определение кластера в том же файле `apimodel.json`, чтобы отобразить новое число узлов в текущей обновленной конфигурации кластера.

## <a name="scale-a-cluster"></a>Масштабировать кластер

Команда `aks-engine scale` может увеличить или уменьшить количество узлов в существующем пуле агентов в кластере Kubernetes `aks-engine`. Узлы всегда добавляются и удаляются в конце пула агентов. Перед удалением узлы блокируются и очищаются.

### <a name="values-for-the-scale-command"></a>Значения для команды scale

Команда scale использует следующие параметры, чтобы найти файл определения кластера и обновить этот кластер.

| Параметр | Пример | ОПИСАНИЕ |
| --- | --- | --- | 
| azure-env | AzureStackCloud | При использовании Azure Stack имена сред должны иметь значение `AzureStackCloud`. | 
| location | local | Это регион для экземпляра Azure Stack. Для ASDK параметр региона нужно настроить как `local`.  | 
| resource-group | kube-rg | Имя группы ресурсов, которая содержит кластер. | 
| subscription-id |  | Идентификатор GUID подписки, которая содержит используемые кластером ресурсы. Убедитесь, что в подписке есть достаточная квота для масштабирования. | 
| client-id |  | Идентификатор клиента субъекта-службы, который был указан при создании кластера из обработчика AKS. | 
| client-secret |  | Секрет субъекта-службы, который был указан при создании кластера. | 
| api-model | kube-rg/apimodel.json | Путь к файлу определения кластера (apimodel.json). Возможный вариант:  _output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | Требуемое число узлов. | 
| -master-FQDN |  | Полное доменное имя главного узла. Обязательный параметр при уменьшении масштаба. |
| identity-system | adfs | Необязательный элемент. Укажите решение по управлению удостоверениями, если используются службы федерации Active Directory (AD FS). |

При масштабировании кластера в Azure Stack необходимо указать параметр **--azure-env**. Дополнительные сведения о параметрах и значениях для команды **scale** обработчика AKS см. в [этой статье](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

### <a name="command-to-scale-your-cluster"></a>Команда для масштабирования кластера

Чтобы масштабировать кластер, выполните следующую команду:

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>Дополнительная информация

- Сведения [об обработчике AKS в Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
- [Обновление кластера Kubernetes в Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)