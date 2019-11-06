---
title: Обновление кластера Kubernetes в Azure Stack | Документация Майкрософт
description: Сведения о том, как обновить кластер Kubernetes в Azure Stack.
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
ms.date: 10/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 3720781dc2545fefaff0b2cd703d7c3880c4b97b
ms.sourcegitcommit: 83cef2c4ec6e1b2fd3f997c91675c1058a850e2f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "72999882"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Обновление кластера Kubernetes в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="upgrade-a-cluster"></a>Обновление кластера

Обработчик AKS позволяет обновить кластер, который изначально был развернут с помощью этого средства. Кластеры можно обслуживать с помощью обработчика AKS. Задачи обслуживания выполняются так же, как для любой системы IaaS. Вы должны следить за доступностью новых обновлений и применять их, используя обработчик AKS.

Команда "Upgrade" (Обновление) обновляет версию Kubernetes и базовый образ ОС. При каждом запуске команды обновления для каждого узла кластера обработчик AKS создает новую виртуальную машину с помощью базового образа AKS, связанного с используемой версией **aks-engine**. Вы можете использовать команду `aks-engine upgrade`, чтобы сохранить валюту всех мастеров и агентов узлов в кластере. 

Корпорация Майкрософт не управляет вашим кластером. Но корпорация Майкрософт предоставляет средство и образ виртуальной машины, которые можно использовать для управления кластером. 

Для развернутого кластера обновляются следующие компоненты:

-   Kubernetes
-   поставщик Kubernetes в Azure Stack;
-   базовая операционная система.

При обновлении рабочего кластера учитывайте следующее:

-   Используете ли вы правильную спецификацию кластера (`apimodel.json`) и группу ресурсов для целевого кластера?
-   Надежен ли компьютер, который вы используете в качестве клиентского, то есть на котором выполняется обработчик AKS и операции обновления?
-   Убедитесь, что у вас есть и нормально работает резервный кластер.
-   Если это возможно, выполняйте команду на виртуальной машине в среде Azure Stack, чтобы снизить число сетевых прыжков и риск сбоев подключения.
-   Убедитесь, что ваша подписка имеет достаточно пространства для завершения процесса. Этот процесс связан с выделением новых виртуальных машин.
-   Не запланировано обновлений системы или других задач.
-   Настройте промежуточное обновление в кластере, настройки которого точно совпадают с параметрами рабочего кластера, и проверьте в нем обновление перед тем, как запускать его в рабочем кластере.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Шаги для обновления к более новой версии Kubernetes

> [!Note]  
> Базовый образ AKS также будет обновлен, если вы используете более новую версию обработчика AKS, а образ доступен в Marketplace.

В приведенных ниже инструкциях используются минимальные шаги для выполнения обновления. Дополнительные сведения см. в статье [Обновление кластеров Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md).

1. Сначала вам нужно определить версии, которые можно выбрать для обновления. Возможная версия зависит от текущей версии. Используйте полученное значение версии для выполнения обновления.

    Выполните следующие команды:

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Например, команда `get-versions` для текущей версии Kubernetes 1.13.5 сообщает, что можно выполнить обновление до версий 1.13.7, 1.14.1, 1.14.3.

2. Соберите сведения, которые понадобятся для выполнения команды `upgrade`. Обновление использует следующие параметры.

    | Параметр | Пример | ОПИСАНИЕ |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Чтобы сообщить обработчику AKS, что целевой платформой является Azure Stack, укажите `AzureStackCloud`. |
    | location | local | Название региона для Azure Stack. Для ASDK параметр региона нужно настроить как `local`. |
    | resource-group | kube-rg | Введите имя новой группы ресурсов или выберите существующую. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите идентификатор подписки. Дополнительные сведения см. в разделе [Подписка на предложение](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |
    | api-model | ./kubernetes-azurestack.json | Путь к файлу конфигурации кластера или модели API. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите GUID субъекта-службы. Идентификатор клиента определяется как идентификатор приложения, когда администратор Azure Stack создает субъект-службу. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите секрет субъекта-службы. Это секрет клиента, который вы настроили при создании службы. |
    | identity-system | adfs | Необязательный элемент. Укажите решение по управлению удостоверениями, если используются службы федерации Active Directory (AD FS). |

3. Указав все значения, выполните такую команду:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Если по какой-либо причине операция обновления завершится сбоем, можно повторно запустить команду обновления после устранения проблемы. Обработчик AKS возобновит операцию, на которой случился сбой в предыдущий раз.

## <a name="steps-to-only-upgrade-the-os-image"></a>Этапы только для обновления образа ОС

1. Просмотрите [таблицу "supported-kubernetes-versions"](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) (поддерживаемых версий kubernetes) и определите, есть ли у вас версия aks-engine и базовый образ AKS, которые нужно обновить. Чтобы просмотреть версию aks-engine, запустите: `aks-engine version`.
2. Обновите обработчик AKS соответствующим образом. На компьютере с установленным aks-engine запустите `./get-akse.sh --version vx.xx.x` заменив **x.xx.x** целевой версией.
3. Попросите оператора Azure Stack добавить версию базового образа AKS, необходимого для Azure Stack Marketplace, который нужно использовать.
4. Выполните команду `aks-engine upgrade`, используя ту же используемую версию Kubernetes, но добавьте `--force`. Пример можно увидеть в статье [Принудительное выполнение обновления](#forcing-an-upgrade).


## <a name="forcing-an-upgrade"></a>Принудительное выполнение обновления

В некоторых случаях может потребоваться принудительно обновить кластер. Например, в первый день вы развертываете кластер в отключенной среде, используя последнюю версию Kubernetes. На следующий день Ubuntu выпускает исправление уязвимости, для которой корпорация Майкрософт создает новый **базовый образ AKS**. Вы можете установить новый образ, выполнив обновление до той же версии Kubernetes, которая уже развернута.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Инструкции см. в статье [о принудительном обновлении](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Дополнительная информация

- Сведения [об обработчике AKS в Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).
- [Масштабирование кластера Kubernetes в Azure Stack](azure-stack-kubernetes-aks-engine-scale.md).