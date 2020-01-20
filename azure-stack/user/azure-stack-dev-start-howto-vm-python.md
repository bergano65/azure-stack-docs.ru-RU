---
title: Развертывание веб-приложения Python на виртуальной машине в Azure Stack Hub | Документация Майкрософт
description: Разверните веб-приложение Python на виртуальной машине в Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6beefec2f912d69e6772980f54c84e93f615de02
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820565"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>Развертывание веб-приложения Python на виртуальной машине в Azure Stack Hub

Вы можете создать виртуальную машину для размещения веб-приложения Python в Azure Stack Hub. В этой статье объясняется, как настроить сервер для размещения веб-приложения Python, которое затем будет развернуто в Azure Stack Hub.

В рамках этой статьи для запуска Flask в виртуальной среде на сервере Nginx используется Python 3.x.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Настройте виртуальную машину в Azure Stack Hub, следуя инструкциям из статьи о [развертывании виртуальной машины Linux для размещения веб-приложения в Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. В области сети виртуальных машин разрешите доступ к следующим портам:

    | Порт | Протокол | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP — это протокол, который используется для доставки веб-страниц с серверов. Клиенты подключаются по протоколу HTTP, используя DNS-имя или IP-адрес. |
    | 443 | HTTPS | HTTPS — это безопасная версия протокола HTTP, которая использует сертификат безопасности и обеспечивает передачу данных в зашифрованном виде. |
    | 22 | SSH | Secure Shell (SSH) — это сетевой протокол с применением шифрования для безопасного обмена данными. Такое соединение используется клиентом SSH для настройки виртуальной машины и развертывания приложений. |
    | 3389 | RDP | Необязательный параметр. Протокол RDP позволяет подключаться к удаленному рабочему столу, чтобы использовать графический пользовательский интерфейс на вашем компьютере.   |
    | 5000, 8000 | Другой | Порты, которые используются веб-платформой Flask при разработке. Для рабочего сервера разработки вам нужно перенаправить трафик через порты 80 и 443. |

## <a name="install-python"></a>Установка Python

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. В командной строке bash на виртуальной машине введите следующую команду:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Проверьте установку. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующую команду:

    ```bash  
        python -version
    ```

3. [Установите Nginx](https://www.nginx.com/resources/wiki/), упрощенную версию веб-сервера. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующую команду:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Установите Git](https://git-scm.com) — широко распространенную систему управления версиями и исходным кодом. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующую команду:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Развертывание и запуск приложения

1. Настройте репозиторий Git на виртуальной машине. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Создайте виртуальную среду и установите в ней все зависимости пакета. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Перейдите на новый сервер. Вы увидите запущенное веб-приложение.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Обновление сервера

1. Подключитесь к своей виртуальной машине в сеансе SSH. Остановите работу сервера, нажав комбинацию клавиш CTRL+C.

2. Введите следующие команды:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Активируйте виртуальную среду и запустите приложение.

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [разработке для Azure Stack Hub](azure-stack-dev-start.md).
- Дополнительные сведения о распространенных сценариях развертывания IaaS для Azure Stack Hub см. [здесь](azure-stack-dev-start-deploy-app.md).
- Дополнительные сведения о языке программирования Python и дополнительные ресурсы см. на сайте [Python.org](https://www.python.org).
