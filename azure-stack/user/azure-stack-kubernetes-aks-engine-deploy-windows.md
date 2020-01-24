---
title: Развертывание обработчика AKS на Windows в Azure Stack Hub | Документация Майкрософт
description: Из этой статьи вы узнаете, как разместить обработчик AKS на компьютере под управлением Windows в Azure Stack Hub для развертывания кластера Kubernetes и управления им.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 280ecbe7c02d3eb9bdc14ba29cfb0b6642a7144c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883530"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>Установка обработчика AKS в Windows в Azure Stack Hub

Вы можете разместить обработчик AKS на компьютере под управлением Windows в Azure Stack Hub для развертывания кластера Kubernetes и управления им. В этой статье мы опишем процессы подготовки клиентской виртуальной машины для управления кластером в подключенных и отключенных экземплярах Azure Stack Hub, а также проверки установки и настройки клиентской виртуальной машины на ASDK.

## <a name="prepare-the-client-vm"></a>Подготовка клиентской виртуальной машины

Обработчик AKS представляет собой средство командной строки, предназначенное для развертывания кластера Kubernetes и управления им. Вы можете запустить обработчик на компьютере в Azure Stack Hub. С этого компьютера через работающий обработчик AKS вы сможете развернуть ресурсы IaaS и программное обеспечение, необходимые для запуска кластера. Затем с того же компьютера, на котором выполняется обработчик, вы сможете выполнять задачи управления для этого кластера.

При выборе клиентского компьютера учитывайте следующее:

1. Нужна ли возможность восстанавливать клиентский компьютер в случае сбоев?
3. Каким способом вы будете подключаться к клиентскому компьютеру, и как он будет взаимодействовать с кластером?

## <a name="install-in-a-connected-environment"></a>Установка в подключенной среде

Вы можете установить клиентскую виртуальную машину для управления кластером Kubernetes в среде Azure Stack Hub, подключенной к Интернету.

1. Создайте виртуальную машину Windows в Azure Stack Hub. Инструкции см. в статье [Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).
2. Подключитесь к виртуальной машине.
3. [Установите Chocolatey по инструкциям для PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

    Веб-сайт Chocolaty предоставляет такую информацию: Chocolaty является диспетчером пакетов для Windows — почти как apt-get или yum, но для Windows. Он был разработан как децентрализованная платформа для быстрой установки нужных приложений и средств. Он основан на инфраструктуре NuGet и в настоящее время использует PowerShell для доставки пакетов из дистрибутивов прямо к вашей двери, ну то есть к компьютеру.
4. Найдите версию обработчика AKS в таблице [поддерживаемых версий Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Базовый обработчик AKS должен быть доступен в Azure Stack Hub Marketplace. При выполнении этой команды укажите версию `--version v0.43.0`. Если вы ее не укажете, команда попытается установить последнюю версию, для которой может понадобиться образ VHD, недоступный в вашем marketplace.
5. Выполните следующую команду в командной строке с повышенными привилегиями, добавив к ней номер версии:

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

> [!Note]  
> Если этот метод установки не сработает, попробуйте выполнить инструкции для [отключенной среды](#install-in-a-disconnected-environment) или воспользуйтесь альтернативным диспетчером пакетов [GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish).

## <a name="install-in-a-disconnected-environment"></a>Установка в отключенной среде

Вы можете установить клиентскую виртуальную машину для управления кластером Kubernetes в среде Azure Stack Hub, не подключенной к Интернету.

1.  На компьютере с доступом к Интернету откройте репозиторий [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest) на сайте GitHub. Скачайте архив (*.tar.gz) для компьютера Windows, например `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Создайте учетную запись хранения в экземпляре Azure Stack Hub, чтобы передать в нее файл архива (*.tar.gz) с двоичным файлом обработчика AKS. Инструкции по использованию Обозревателя службы хранилища Azure см. в статье [Подключение обозревателя службы хранилища к подписке Azure Stack Hub или к учетной записи хранения](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Создайте виртуальную машину Windows в Azure Stack Hub. Инструкции см. в статье [Краткое руководство. Создание виртуальной машины Windows Server с помощью портала Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)

4.  Используя URL-адрес большого двоичного объекта в учетной записи хранения Azure Stack Hub, на который вы отправили файл архива (*.tar.gz), скачайте этот файл на виртуальную машину управления. Извлеките архив в каталог, к которому есть доступ из командной строки.

5. Подключитесь к виртуальной машине.

6. [Установите Chocolatey по инструкциям для PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

7.  Выполните следующую команду в командной строке с повышенными привилегиями: Укажите правильный номер версии:

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

## <a name="verify-the-installation"></a>Проверка установки

После настройки клиентской виртуальной машины убедитесь, что вы успешно установили обработчик AKS.

1. Подключитесь к клиентской виртуальной машине.
2. Выполните следующую команду:

    ```PowerShell  
    aks-engine version
    ```

Если вам не удастся подтвердить установку обработчика AKS на клиентской виртуальной машине, воспользуйтесь инструкциями [по устранению проблем при установке обработчика AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Установка ASDK

Если для обработчика AKS в ASDK используется клиентская виртуальная машина, размещенная на компьютере за пределами ASDK, потребуется добавить сертификат. Если вы используете виртуальную машину Windows в самой среде ASDK, этот компьютер автоматически доверяет сертификату ASDK. Если клиентский компьютер находится за пределами ASDK, необходимо извлечь сертификат из ASDK и добавить его на компьютер под управлением Windows.

При использовании ASDK конечная точка Azure Resource Manager использует самозаверяющий сертификат, и этот сертификат нужно явным образом добавить в хранилище доверенных сертификатов на клиентском компьютере. Корневой сертификат ASDK можно получить на любой виртуальной машине, развернутой в ASDK.

1. Экспортируйте корневой сертификат ЦС. Инструкции см. в разделе [Экспорт корневого сертификата ЦС Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-hub-ca-root-certificate).
2. Доверьте корневой сертификат ЦС Azure Stack Hub. Инструкции см. в разделе [Доверие для корневого сертификата ЦС Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-hub-ca-root-certificate).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
