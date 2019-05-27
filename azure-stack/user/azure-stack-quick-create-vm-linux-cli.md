---
title: Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack | Документация Майкрософт
description: Создайте виртуальную машину Linux с помощью CLI в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 19c856bdf981775b0b3a8ee923046b51e6ef79ca
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782777"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-azure-cli-in-azure-stack"></a>Краткое руководство. Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете создать виртуальную машину под управлением Ubuntu Server 16.04 LTS с помощью Azure CLI. Чтобы создать и использовать виртуальную машину, выполните описанные в этой статье действия. В этой статье приведены инструкции, которые помогут вам:

* подключиться к виртуальной машине через удаленный клиент;
* установить веб-сервер NGINX и открыть его стандартную домашнюю страницу;
* очистить неиспользуемые ресурсы.

## <a name="prerequisites"></a>Предварительные требования

* **Образ Linux в Azure Stack Marketplace**.

   По умолчанию Azure Stack Marketplace не содержит образ Linux. Обратитесь к оператору Azure Stack, чтобы он предоставил нужный образ **Ubuntu Server 16.04 LTS**. Оператор может добавить его, выполнив инструкции из статьи [Download marketplace items from Azure to Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) (Скачивание элементов Marketplace из Azure в Azure Stack).

* Для создания ресурсов и управления ими в Azure CLI требуется определенная версия Azure Stack. Если вы не настраивали Azure CLI для Azure Stack, войдите в [пакет средств разработки](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (или внешний клиент на базе Windows, [если вы подключаетесь через VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) и выполните действия по [установке и настройке Azure CLI](azure-stack-version-profiles-azurecli2.md).

* В каталоге .ssh вашего профиля пользователя Windows сохраняется открытый ключ SSH с именем id_rsa.pub. Дополнительные сведения создании открытого ключа SSH см. в [этой статье](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором вы можете развертывать ресурсы Azure Stack и управлять ими. Из пакета средств разработки или интегрированной системы Azure Stack выполните команду [az group create](/cli/azure/group#az-group-create), чтобы создать группу ресурсов.

> [!NOTE]
> В примерах кода всем переменным уже присвоены значения. Но вы можете изменить эти значения, если потребуется.

В следующем примере создается группа ресурсов с именем myResourceGroup в локальном расположении. 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create). В следующем примере создаются виртуальная машина с именем myVM В этом примере используются следующие учетные данные администратора: имя Demouser и пароль Demouser@123. Укажите вместо них значения, подходящие для вашей среды.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Общедоступный IP-адрес возвращается в параметре **PublicIpAddress**. Запишите этот адрес, так как он понадобится позже для доступа к виртуальной машине.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

Так как на этой виртуальной машине будет выполняться веб-сервер IIS, порт 80 должен быть доступным из Интернета. Выполните команду [az vm open-port](/cli/azure/vm), чтобы открыть нужный порт. 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Используйте SSH для подключения к виртуальной машине.

Подключитесь к виртуальной машине с клиентского компьютера, на котором установлен протокол SSH. Если вы работаете в клиенте Windows, для создания подключения можно использовать [Putty](https://www.putty.org/). Чтобы подключиться к виртуальной машине, выполните следующую команду:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Установка веб-сервера NGINX

Чтобы обновить источники пакетов и установить последнюю версию пакета NGINX, выполните следующий скрипт:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Просмотр страницы приветствия nginx

Теперь на виртуальной машине установлен сервер NGINX и открыт порт 80, и вы можете обращаться к веб-серверу через общедоступный IP-адрес этой виртуальной машины. Откройте браузер и перейдите на страницу ```http://<public IP address>```.

![Страница приветствия веб-сервера NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите ресурсы, которые вам больше не нужны. Для удаления этих ресурсов можно использовать команду [az group delete](/cli/azure/group#az-group-delete). Чтобы удалить группу ресурсов и все ресурсы в ней, выполните следующую команду:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы развернули простую виртуальную машину с сервером Linux и веб-сервером. Дополнительные сведения о виртуальных машинах Azure Stack см. в [рекомендациях по работе с виртуальными машинами в Azure Stack](azure-stack-vm-considerations.md).
