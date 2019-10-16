---
title: Установка необходимых компонентов для обработчика AKS в Azure Stack | Документация Майкрософт
description: Предварительные требования для запуска обработчика ASK в Azure Stack.
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
ms.openlocfilehash: b56c5a2be45e9f92630283a2b702f37471e80290
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173097"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Установка необходимых компонентов для обработчика AKS в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Обработчик AKS можно установить на виртуальной машине в вашей среде или на любом клиентском компьютере с доступом к конечной точке Azure Stack Resource Manager. Перед запуском обработчика необходимо подготовить следующие компоненты: сервер Ubuntu с базовым обработчиком AKS и расширение пользовательских сценариев Linux, доступные в вашей подписке, удостоверение субъекта-службы с назначенной ролью участника и пару закрытого и открытого ключей для доступа по протоколу SSH к серверу Ubuntu. Кроме того, если вы используете Пакет средств разработки Azure Stack, на компьютере разработки нужно настроить доверие нужным сертификатам.

Если все необходимые компоненты готовы, приступайте к [определению кластера](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Если вы являетесь оператором облака для Azure Stack и хотите предложить подсистему AKS, следуйте инструкциям в статье [Add the Azure Kubernetes Services (AKS) Engine prerequisites to the Azure Stack Marketplace](../operator/azure-stack-aks-engine.md) (Добавление предварительных требований обработчика Службы Azure Kubernetes (AKS) в Azure Stack Marketplace).

## <a name="prerequisites-for-the-aks-engine"></a>Необходимые компоненты для обработчика AKS

Для работы с обработчиком AKS должны быть доступны следующие ресурсы. Учитывайте, что обработчик AKS рассчитан на использование клиентами Azure Stack для развертывания кластеров Kubernetes в клиентских подписках. Единственный процесс, который может предполагать взаимодействие с оператором Azure Stack, это скачивание элементов Marketplace и создание удостоверения субъекта-службы. Подробные сведения собраны в следующей таблице.

| Предварительные требования | ОПИСАНИЕ | Обязательно | Указания |
| --- | --- | --- | --- |
| Расширение пользовательских скриптов Linux | Расширение пользовательских скриптов Linux 2.0<br>Предложение: Настраиваемый скрипт для Linux 2.0<br>Версия: 2.0.6 (или последняя версия)<br>Издатель: Корпорация Майкрософт | Обязательно | Если у вас в подписке нет этого элемента, обратитесь к оператору облака. |
| Базовый образ AKS для Ubuntu | Базовый образ AKS<br>Предложение: aks<br>Версия: 2019.09.19 (или более новая)<br>Издатель: microsoft-aks<br>SKU: aks-ubuntu-1604-201909 | Обязательно | Если у вас в подписке нет этого элемента, обратитесь к оператору облака. Дополнительные сведения о зависимости версий см. в разделе [Соответствие версий обработчика и базового образа](#matching-engine-to-base-image-version).<br> Если вы являетесь оператором облака для Azure Stack и хотите предложить подсистему AKS, следуйте инструкциям в статье [Add the Azure Kubernetes Services (AKS) Engine prerequisites to the Azure Stack Marketplace](../operator/azure-stack-aks-engine.md) (Добавление предварительных требований обработчика Службы Azure Kubernetes (AKS) в Azure Stack Marketplace). |
| Подписка Azure Stack | Доступ к предложениям в Azure Stack осуществляется через подписки. Предложение содержит доступные вам службы. | Обязательно | Чтобы иметь возможность развертывать рабочие нагрузки клиента в Azure Stack, прежде всего нужно получить [подписку Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Имя субъекта-службы |  Приложение, необходимое для развертывания или настройки ресурсов через Azure Resource Manager, должно быть представлено субъектом-службой. | Обязательно | Для получения этого элемента может потребоваться взаимодействие с оператором Azure Stack.  Инструкции см. в статье [Использование удостоверения приложения для доступа к ресурсам](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| Имя субъекта-службы с назначенной ролью **Участник** | Чтобы разрешить приложению доступ к ресурсам в подписке с помощью субъекта-службы, необходимо назначить этому субъекту-службе роль для конкретного ресурса. | Обязательно | Инструкции см. в разделе [Назначение роли](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |
| группа ресурсов. | Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Если вы не укажете существующую группу ресурсов, средство создаст новую. | Необязательно | [Управление группами ресурсов Azure Resource Manager с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal). |
| Закрытый и открытый ключи | Чтобы использовать открытое SSH-подключение на компьютере разработки и сервере виртуальной машины в Azure Stack, на котором размещено ваше веб-приложение, вам потребуется создать пару из открытого и закрытого ключей SSH. | Обязательно | Инструкции по создания ключа см. в разделе [SSH Key Generation](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key) (Создание ключа SSH).|

> [!Note]  
> Вы также можете создать необходимые компоненты для обработчика AKS с помощью [Azure CLI для Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) или [Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Соответствие версий обработчика и базового образа

Обработчик AKS использует специально созданный образ, который именуется **базовым образом AKS**. Версия обработчика AKS определяется версией образа, который предоставляет для Azure Stack ваш оператор Azure Stack. Таблицу, в которой перечислены версии обработчика AKS и соответствующие поддерживаемые версии Kubernetes, можно найти [здесь](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Например, для обработчика AKS версии `v0.41.2` необходим базовый образ AKS версии `2019.09.19`. Попросите оператора Azure Stack скачать нужную версию образа из Azure Marketplace в Azure Stack Marketplace.

Если образ недоступен в Azure Stack Marketplace, будет происходить ошибка. Например, если вы используете обработчик AKS версии 0.41.2, и в системе недоступна версия `2019.09.19` базового образа AKS, при запуске обработчика AKS вы увидите следующую ошибку: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Вы можете проверить текущую версию обработчика AKS, выполнив следующую команду:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Развертывание обработчика AKS в Windows в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Развертывание обработчика AKS в Linux в Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)