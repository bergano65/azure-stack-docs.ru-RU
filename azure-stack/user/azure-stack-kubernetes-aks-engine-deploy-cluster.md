---
title: Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub | Документация Майкрософт
description: Узнайте, как развернуть кластер Kubernetes в Azure Stack Hub из клиентской виртуальной машины, на которой выполняется обработчик AKS.
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
ms.date: 01/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 34fc30c13cf365560fbd30234a60af4cc4f9a594
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883578"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub

Вы можете развернуть кластер Kubernetes в Azure Stack Hub из клиентской виртуальной машины, на которой выполняется обработчик AKS. В этой статье мы рассмотрим создание спецификации для кластера, развертывание кластера из файла `apimodel.json` и проверку кластера путем развертывания MySQL с помощью Helm.

## <a name="define-a-cluster-specification"></a>Определение спецификации кластера

Спецификацию кластера можно оформить в виде файла с документом в формате JSON, который называется [моделью API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). Обработчик AKS использует спецификацию кластера из модели API для создания кластера. 

### <a name="update-the-api-model"></a>Обновление модели API

В этом разделе рассматривается создание модели API для кластера.

1.  Для начала примените [пример](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) файла модели API для Azure Stack Hub и создайте локальную копию для своего развертывания. На виртуальной машине с установленным обработчиком AKS выполните следующую команду:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Если на нем нет подключения к Интернету, вы можете скачать файл и вручную скопировать его на отключенный от сети компьютер, где будете изменять его. Вы можете скопировать файл на компьютер Linux с помощью таких средств, как [PuTTY или WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html).

2.  Чтобы открыть файл, можно использовать редактор nano:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Если nano не установлен в вашей системе, установите его на Ubuntu с помощью команды `sudo apt-get install nano`.

3.  В файле kubernetes-azurestack.json найдите `orchestratorRelease`. Выберите одну из поддерживаемых версий Kubernetes, Например, 1.14, 1.15. Эти версии часто являются обновлениями. Лучше указывать версию в формате x.xx, а не x.xx.x. Список актуальных версий см. в разделе о [поддерживаемых версиях Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Поддерживаемую версию можно узнать, выполнив следующую команду обработчика AKS:

    ```bash
    aks-engine get-versions
    ```

4.  Найдите `customCloudProfile` и укажите URL-адрес для портала клиента. Например, `https://portal.local.azurestack.external`. 

5. Если вы используете AD FS, добавьте `"identitySystem":"adfs"`. Например,

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Если вы используете Azure AD для системы удостоверений, добавлять поле **identitySystem** не нужно.

6. Найдите `portalURL` и укажите URL-адрес для портала клиента. Например, `https://portal.local.azurestack.external`.

7.  В массиве `masterProfile` заполните следующие поля:

    | Поле | Description |
    | --- | --- |
    | dnsPrefix | Введите уникальную строку, которая будет определять имя узла для виртуальных машин. Например, можно создать имя на основе имени группы ресурсов. |
    | count |  Введите число главных серверов для развертывания. Минимальное значение для развертывания высокого уровня доступности — 3, а для развертываний без высокого уровня доступности — 1. |
    | vmSize |  Введите [размер, поддерживаемый Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), например `Standard_D2_v2`. |
    | distro | Введите `aks-ubuntu-16.04`. |

8.  В массиве `agentPoolProfiles` измените следующие данные:

    | Поле | Description |
    | --- | --- |
    | count | Введите число агентов для развертывания. |
    | vmSize | Введите [размер, поддерживаемый Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), например `Standard_D2_v2`. |
    | distro | Введите `aks-ubuntu-16.04`. |

9.  В массиве `linuxProfile` измените следующие данные:

    | Поле | Description |
    | --- | --- |
    | adminUsername | Имя пользователя для администратора виртуальной машины. |
    | ssh | Введите открытый ключ, который будет использоваться для аутентификации SSH-соединений с виртуальными машинами. При использовании Putty откройте генератор ключей PuTTY, чтобы загрузить закрытый ключ Putty и открытый ключ, который начинается с ssh-rsa, как показано в следующем примере. Вы можете использовать ключ, сгенерированный при создании клиента Linux, но **вам необходимо скопировать открытый ключ, чтобы он представлял собой однострочный текст, как показано в примере**.|

    ![Генератор ключей PuTTY](media/azure-stack-kubernetes-aks-engine-deploy-cluster/putty-key-generator.png)

### <a name="more-information-about-the-api-model"></a>Дополнительные сведения о модели API

- Полный справочник по всем параметрам, доступным в модели API, см. в описании [определений кластера](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Основные сведения о параметрах, имеющих прямое отношение к Azure Stack Hub, вы найдете в [этой статье](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Развертывание кластера Kubernetes

Собрав в модели API все необходимые значения, переходите к созданию кластера. На этом этапе нужно выполнить следующие действия.

Попросите оператора Azure Stack Hub:

- Проверить работоспособность системы, например с помощью `Test-AzureStack` и средства мониторинга оборудования вашего поставщика вычислительной техники.
- Убедиться в наличии надлежащего объема ресурсов в системе, в том числе памяти, хранилища и общедоступных IP-адресов.
- Предоставить сведения о квоте, связанной с вашей подпиской, чтобы вы были уверены в наличии достаточного места для размещения запланированного количества виртуальных машин.

Переходите к развертыванию кластера:

1.  Проверьте доступные параметры для [флагов CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags) обработчика AKS в Azure Stack Hub.

    | Параметр | Пример | Description |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Используйте `AzureStackCloud`, чтобы сообщить обработчику AKS, что целевой платформой является Azure Stack Hub. |
    | identity-system | adfs | Необязательный параметр. Укажите решение по управлению удостоверениями, если используются службы федерации Active Directory (AD FS). |
    | location | local | Название региона для Azure Stack Hub. Для ASDK параметр региона нужно настроить как `local`. |
    | resource-group | kube-rg | Введите имя новой группы ресурсов или выберите существующую. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре. |
    | api-model | ./kubernetes-azurestack.json | Путь к файлу конфигурации кластера или модели API. |
    | output-directory | kube-rg | Введите имя каталога, в котором будет содержаться выходной файл `apimodel.json` и другие созданные файлы. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите GUID субъекта-службы. Идентификатор клиента определяется как идентификатор приложения, когда администратор Azure Stack Hub создает субъект-службу. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите секрет субъекта-службы. Это секрет клиента, который вы настроили при создании службы. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Введите идентификатор подписки. Дополнительные сведения см. в разделе [Подписка на предложение](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |

    Например:

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Если выполнение по любой причине завершится ошибкой после создания выходного каталога, устраните проблему и выполните команду повторно. Если вы повторно запускаете развертывание, выходной каталог которого уже использовался ранее, обработчик AKS вернет ошибку с сообщением о том, что каталог уже существует. Существующий каталог можно перезаписать, указав флаг `--force-overwrite`.

3.  Сохраните конфигурацию кластера для обработчика AKS в безопасном зашифрованном расположении.

    Найдите файл `apimodel.json`. Сохраните его в надежном месте. Этот файл будет использоваться как источник входных данных в других операциях с обработчиком AKS.

    Созданный файл `apimodel.json` содержит субъект-службу, секрет и открытый ключ SSH для вашей входной модели API. Он также включает все остальные метаданные, которые нужны обработчику AKS для выполнения других операций. Если вы утратите его, обработчик AKS не сможет настроить кластер.

    Секреты **не шифруются**. Храните этот файл в надежном зашифрованном расположении. 

## <a name="verify-your-cluster"></a>Проверка кластера

Чтобы проверить кластер, разверните в нем MySQL с помощью Helm.

1. Получите общедоступный IP-адрес одного из главных узлов с помощью портала Azure Stack Hub.

2. С любого компьютера, который имеет доступ к экземпляру Azure Stack Hub, подключитесь по протоколу SSH к новому главному узлу из любого клиента, например PuTTY или MobaXterm. 

3. В качестве имени пользователя SSH укажите "azureuser" и предоставьте файл закрытого ключа из той пары ключей, которую вы указали для развертывания кластера.

4.  Выполните следующие команды:

    ```bash
    sudo snap install helm --classic
    kubectl -n kube-system create serviceaccount tiller
    kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
    helm init --service-account=tiller
    helm repo update
    helm install stable/mysql
    ```

5.  Чтобы очистить тест, найдите имя для развертывания MySQL. В следующем примере используется имя `wintering-rodent`. Удалите его. 

    Выполните следующие команды:

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    Интерфейс командной строки отобразит следующее:
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Устранение неполадок с обработчиком AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-troubleshoot.md)
