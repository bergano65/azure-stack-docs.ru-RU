---
title: Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack Hub
description: Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 49b2a950b523ca76143f50784efb048880d1ac93
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701944"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Краткое руководство. Создание виртуальной машины с сервером Linux с помощью Azure CLI в Azure Stack Hub

Вы можете создать виртуальную машину под управлением Ubuntu Server 16.04 LTS с помощью Azure CLI. В этой статье описано, как создать и использовать виртуальную машину. Здесь также объясняется, как выполнить следующие задачи:

* подключиться к виртуальной машине через удаленный клиент;
* установить веб-сервер NGINX и открыть его стандартную домашнюю страницу;
* очистить неиспользуемые ресурсы.

## <a name="prerequisites"></a>Предварительные требования

* Образ Linux в Azure Stack Hub Marketplace.

   По умолчанию Azure Stack Hub Marketplace не содержит образ Linux. Обратитесь к оператору Azure Stack Hub, чтобы он предоставил нужный образ Ubuntu Server 16.04 LTS. Для этого оператор может выполнить инструкции из статьи [Скачивание элементов Marketplace из Azure в Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md).

* Для создания ресурсов и управления ими Azure Stack Hub требуется определенная версия Azure CLI. Если вы не настраивали Azure CLI для Azure Stack Hub, войдите в среду [Пакет средств разработки Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (или внешний клиент на базе Windows, [если вы подключаетесь через VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) и выполните инструкции по [установке и настройке Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Открытый ключ SSH с именем *id_rsa.pub* сохраняется в каталоге (с расширением *.ssh*) вашего профиля пользователя Windows. Дополнительные сведения о создании ключей SSH см. в статье [Использование открытого ключа SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором вы можете развертывать ресурсы Azure Stack Hub и управлять ими. Из пакета средств разработки или интегрированной системы Azure Stack Hub выполните команду [az group create](/cli/azure/group#az-group-create), чтобы создать группу ресурсов.

> [!NOTE]
> В приведенных ниже примерах кода всем переменным уже присвоены значения. Но вы можете присвоить собственные значения.

В следующем примере создается группа ресурсов с именем myResourceGroup в локальном расположении. 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create). В следующем примере создаются виртуальная машина с именем myVM В этом примере используются следующие учетные данные администратора: имя *Demouser* и пароль *Demouser@123* . Укажите вместо них значения, подходящие для вашей среды.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Общедоступный IP-адрес возвращается в параметре **PublicIpAddress**. Запишите адрес для дальнейшего использования при работе с виртуальной машиной.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

Так как на этой виртуальной машине будет выполняться веб-сервер IIS, порт 80 должен быть доступным из Интернета. Чтобы открыть порт, используйте команду [az vm open-port](/cli/azure/vm). 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Используйте SSH для подключения к виртуальной машине.

Подключитесь к виртуальной машине с клиентского компьютера, на котором установлен протокол SSH. Если вы работаете в клиенте Windows, подключение можно создать с помощью [PuTTY](https://www.putty.org/). Чтобы подключиться к виртуальной машине, выполните следующую команду:

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

Теперь на виртуальной машине установлен веб-сервер NGINX и открыт порт 80, и вы можете обращаться к веб-серверу по общедоступному IP-адресу этой виртуальной машины. Для этого откройте браузер и перейдите на страницу ```http://<public IP address>```.

![Страница приветствия веб-сервера NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите ресурсы, которые вам больше не нужны. Их можно удалить с помощью команды [az group delete](/cli/azure/group#az-group-delete). Выполните следующую команду:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы развернули простую виртуальную машину с сервером Linux и веб-сервером. Дополнительные сведения о виртуальных машинах Azure Stack Hub см. в [этих рекомендациях](azure-stack-vm-considerations.md).
