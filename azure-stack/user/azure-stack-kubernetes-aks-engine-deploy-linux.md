---
title: Установка обработчика AKS в Azure Stack Hub в Linux
description: Из этой статьи вы узнаете, как разместить обработчик AKS на компьютере под управлением Linux в Azure Stack Hub для развертывания кластера Kubernetes и управления им.
author: mattbriggs
ms.topic: article
ms.date: 01/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 66e340df1d687e9a0c19f43c05c4fcb92e6940c2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883544"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Установка обработчика AKS в Azure Stack Hub в Linux

Вы можете разместить обработчик AKS на компьютере под управлением Linux в Azure Stack Hub для развертывания кластера Kubernetes и управления им. В этой статье мы опишем процессы подготовки клиентской виртуальной машины для управления кластером в подключенных и отключенных экземплярах Azure Stack Hub, а также проверки установки и настройки клиентской виртуальной машины на ASDK.

## <a name="prepare-the-client-vm"></a>Подготовка клиентской виртуальной машины

Обработчик AKS представляет собой средство командной строки, предназначенное для развертывания кластера Kubernetes и управления им. Вы можете запустить обработчик на компьютере в Azure Stack Hub. С этого компьютера через работающий обработчик AKS вы сможете развернуть ресурсы IaaS и программное обеспечение, необходимые для запуска кластера. Затем с того же компьютера, на котором выполняется обработчик, вы сможете выполнять задачи управления для этого кластера.

При выборе клиентского компьютера учитывайте следующее:

1. Нужна ли возможность восстанавливать клиентский компьютер в случае сбоев?
2. Каким способом вы будете подключаться к клиентскому компьютеру, и как он будет взаимодействовать с кластером?

## <a name="install-in-a-connected-environment"></a>Установка в подключенной среде

Вы можете установить клиентскую виртуальную машину для управления кластером Kubernetes в среде Azure Stack Hub, подключенной к Интернету.

1. Создайте виртуальную машину Linux в Azure Stack Hub. Инструкции см. в статье [Краткое руководство. Создание виртуальной машины с сервером Linux с помощью портала Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Подключитесь к виртуальной машине.
3. Найдите версию обработчика AKS в таблице [поддерживаемых версий Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Базовый образ AKS должен быть доступен в Azure Stack Hub Marketplace. При выполнении этой команды укажите версию `--version v0.43.0`. Если вы ее не укажете, команда попытается установить последнюю версию, для которой может понадобиться образ VHD, недоступный в вашем marketplace.
4. Выполните следующую команду:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.43.0
    ```

    > [!Note]  
    > Если этот метод установки не сработает, попробуйте выполнить инструкции для [отключенной среды](#install-in-a-disconnected-environment) или примените альтернативный диспетчер пакетов [GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish).

## <a name="install-in-a-disconnected-environment"></a>Установка в отключенной среде

Вы можете установить клиентскую виртуальную машину для управления кластером Kubernetes в среде Azure Stack Hub, не подключенной к Интернету.

1.  На компьютере с доступом к Интернету откройте репозиторий [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest) на сайте GitHub. Скачайте архив (*.tar.gz) для компьютера Linux, например `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Создайте учетную запись хранения в экземпляре Azure Stack Hub, чтобы передать в нее файл архива (*.tar.gz) с двоичным файлом обработчика AKS. Инструкции по использованию Обозревателя службы хранилища Azure см. в статье [Подключение обозревателя службы хранилища к подписке Azure Stack Hub или к учетной записи хранения](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Создайте виртуальную машину Linux в Azure Stack Hub. Инструкции см. в статье [Краткое руководство. Создание виртуальной машины с сервером Linux с помощью портала Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Используя URL-адрес большого двоичного объекта в учетной записи хранения Azure Stack Hub, на который вы отправили файл архива (*.tar.gz), скачайте этот файл на виртуальную машину управления. Извлеките архив в каталог `/usr/local/bin`.

4. Подключитесь к виртуальной машине.

5.  Выполните следующую команду:

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>Проверка установки

После настройки клиентской виртуальной машины убедитесь, что вы успешно установили обработчик AKS.

1. Подключитесь к клиентской виртуальной машине.
2. Выполните следующую команду:

   ```bash  
   aks-engine version
   ```

3. Если конечная точка Azure Resource Manager использует самозаверяющий сертификат, нужно явно добавить корневой сертификат в хранилище доверенных сертификатов компьютера. Корневой сертификат можно найти на виртуальной машине в этом каталоге: /var/lib/waagent/Certificates.pem. Скопируйте файл сертификата с помощью следующей команды: 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Если вам не удастся подтвердить установку обработчика AKS на клиентской виртуальной машине, воспользуйтесь инструкциями [по устранению проблем при установке обработчика AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Установка ASDK

Если для обработчика AKS в ASDK используется клиентская виртуальная машина, потребуется добавить сертификат.

При использовании ASDK конечная точка Azure Resource Manager использует самозаверяющий сертификат, и этот сертификат нужно явным образом добавить в хранилище доверенных сертификатов на клиентском компьютере. Корневой сертификат ASDK можно получить на любой виртуальной машине, развернутой в ASDK. Например, на виртуальной машине Ubuntu он расположен в каталоге `/var/lib/waagent/Certificates.pem`. 

Скопируйте файл сертификата с помощью следующей команды:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)