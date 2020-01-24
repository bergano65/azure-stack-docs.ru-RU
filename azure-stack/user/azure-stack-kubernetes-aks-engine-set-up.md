---
title: Установка необходимых компонентов для обработчика AKS в Azure Stack Hub | Документация Майкрософт
description: Предварительные требования для запуска обработчика ASK в Azure Stack Hub.
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
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 1516b07cc491ca365f5bc87ad584960818bb537d
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879620"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Установка необходимых компонентов для обработчика AKS в Azure Stack Hub

Обработчик AKS можно установить на виртуальной машине в вашей среде или на любом клиентском компьютере с доступом к конечной точке Azure Stack Hub Resource Manager. Перед запуском обработчика необходимо подготовить следующие компоненты: сервер Ubuntu с базовым обработчиком AKS и расширение пользовательских сценариев Linux, доступные в вашей подписке, удостоверение субъекта-службы с назначенной ролью участника и пару закрытого и открытого ключей для доступа по протоколу SSH к серверу Ubuntu. Кроме того, если вы используете Пакет средств разработки Azure Stack, на компьютере разработки нужно настроить доверие нужным сертификатам.

Если все необходимые компоненты готовы, приступайте к [определению кластера](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Если вы являетесь оператором облака для Azure Stack Hub и хотите предложить обработчик AKS, следуйте инструкциям по [добавлению предварительных требований обработчика AKS в Azure Stack Hub Marketplace](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Необходимые компоненты для обработчика AKS

Для использования обработчика AKS должны быть доступны следующие ресурсы. Учитывайте, что обработчик AKS рассчитан на использование клиентами Azure Stack Hub для развертывания кластеров Kubernetes в клиентских подписках. Единственный процесс, который может предполагать взаимодействие с оператором Azure Stack Hub, это скачивание элементов Marketplace и создание удостоверения субъекта-службы. Подробные сведения собраны в следующей таблице.

Для оператора облака требуются следующие элементы.

| Предварительные требования | Description | Обязательно | Instructions |
| --- | --- | --- | --- | --- |
| Расширение пользовательских скриптов Linux | Расширение пользовательских скриптов Linux 2.0<br>Предложение: Настраиваемый скрипт для Linux 2.0<br>Версия: 2.0.6 (или последняя версия)<br>Издатель: Корпорация Майкрософт | Обязательно | Если у вас в подписке нет этого элемента, обратитесь к оператору облака. |
| Базовый образ AKS для Ubuntu | Базовый образ AKS<br>Предложение: aks<br> 2019.10.24 (или более новая версия)<br>Издатель: microsoft-aks<br>SKU: aks-ubuntu-1604-201910 | Обязательно | Если у вас в подписке нет этого элемента, обратитесь к оператору облака. Дополнительные сведения о зависимости версий см. в разделе [Соответствие версий обработчика и базового образа](#matching-engine-to-base-image-version).<br> Если вы являетесь оператором облака для Azure Stack Hub и хотите предложить обработчик AKS, следуйте инструкциям по [добавлению предварительных требований обработчика AKS в Azure Stack Hub Marketplace](../operator/azure-stack-aks-engine.md). |
| Имя субъекта-службы |  Приложение, необходимое для развертывания или настройки ресурсов через Azure Resource Manager, должно быть представлено субъектом-службой. | Обязательно | Для получения этого элемента может потребоваться взаимодействие с оператором Azure Stack Hub.  Инструкции см. в статье [Использование удостоверения приложения для доступа к ресурсам](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| Имя субъекта-службы с назначенной ролью **Участник** | Чтобы разрешить приложению доступ к ресурсам в подписке с помощью субъекта-службы, необходимо назначить этому субъекту-службе роль для конкретного ресурса. | Обязательно | Инструкции см. в разделе [Назначение роли](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |

Вы можете задать следующие элементы.

| Предварительные требования | Description | Обязательно | Instructions |
| --- | --- | --- | --- |
| Подписка Azure Stack Hub | Доступ к предложениям в Azure Stack Hub осуществляется через подписки. Предложение содержит доступные вам службы. | Обязательно | Чтобы иметь возможность развертывать рабочие нагрузки клиента в Azure Stack Hub, прежде всего нужно получить [подписку Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| группа ресурсов. | Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Если вы не укажете существующую группу ресурсов, средство создаст новую. | Необязательно | [Управление группами ресурсов Azure Resource Manager с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal). |
| Закрытый и открытый ключи | Чтобы использовать открытое SSH-подключение на компьютере разработки и сервере виртуальной машины в Azure Stack Hub, на котором размещено ваше веб-приложение, вам потребуется создать пару из открытого и закрытого ключей SSH. | Обязательно | Инструкции по создания ключа см. в разделе [SSH Key Generation](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key) (Создание ключа SSH).|


> [!Note]  
> Вы также можете создать необходимые компоненты для обработчика AKS с помощью [Azure CLI для Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) или [Azure Stack Hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Соответствие версий обработчика и базового образа

Обработчик AKS использует специально созданный образ — **базовый образ AKS**. Версия обработчика AKS определяется версией образа, который предоставляет для Azure Stack Hub ваш оператор Azure Stack Hub. См. таблицу, в которой перечислены [версии обработчика AKS и соответствующие поддерживаемые версии Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Например, для использования обработчика AKS версии `v0.43.0` необходим базовый образ AKS версии `2019.10.24`. Попросите оператора Azure Stack Hub скачать нужную версию образа из Azure Marketplace в Azure Stack Hub Marketplace.

Если образ недоступен в Azure Stack Hub Marketplace, будет происходить ошибка. Например, если вы используете обработчик AKS версии 0.43.0, и в системе недоступна версия `2019.10.24` базового образа AKS, при запуске обработчика AKS вы увидите следующую ошибку: 

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Установка обработчика AKS в Windows в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Установка обработчика AKS в Azure Stack Hub в Linux](azure-stack-kubernetes-aks-engine-deploy-linux.md)