---
title: Развертывание приложения Ruby на виртуальной машине в Azure Stack | Документация Майкрософт
description: Разверните приложение Ruby на виртуальной машине в Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f7867e963551d04336ac4092e8b1b2f033c29e94
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838331"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Развертывание веб-приложения Ruby на виртуальной машине в Azure Stack

Вы можете создать виртуальную машину для размещения веб-приложения Ruby в Azure Stack. В этой статье объясняется, как настроить сервер для размещения веб-приложения Ruby, которое затем будет развернуто в Azure Stack.

В рамках этой статьи используются Ruby и веб-платформа Ruby on Rails.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Настройте виртуальную машину в Azure Stack. Выполните действия, описанные в статье [Развертывание виртуальной машины Linux для размещения веб-приложения в Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. В колонке сети виртуальных машин разрешите доступ к следующим портам:

    | Порт | Протокол | ОПИСАНИЕ |
    | --- | --- | --- |
    | 80 | HTTP | HTTP — это протокол, который используется для доставки веб-страниц с серверов. Клиенты подключаются по протоколу HTTP, используя DNS-имя или IP-адрес. |
    | 443 | HTTPS | HTTPS — это безопасная версия протокола HTTP, которая использует сертификат безопасности и обеспечивает передачу данных в зашифрованном виде. |
    | 22 | SSH | Secure Shell (SSH) — это сетевой протокол с применением шифрования для безопасного обмена данными. Такое соединение используется клиентом SSH для настройки виртуальной машины и развертывания приложений. |
    | 3389 | RDP | Необязательный элемент. Протокол RDP позволяет подключаться к удаленному рабочему столу, чтобы использовать графический пользовательский интерфейс на вашем компьютере.   |
    | 3000 | Пользовательская | Этот порт используется при разработке на Ruby on Rails. Для рабочего сервера разработки вам нужно перенаправить трафик через порты 80 и 443. |

## <a name="install-ruby"></a>Установка Ruby

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Установите репозиторий PPA. В командной строке bash на виртуальной машине введите следующие команды:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Установите Ruby и Ruby on Rails на виртуальную машину. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Установите зависимости Ruby on Rails. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Во время установки зависимостей Ruby on Rails, возможно, потребуется повторно выполнить `sudo gem install bundler`. Если установка завершается сбоем, просмотрите журналы ошибок и устраните проблемы.

4. Проверьте установку. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующую команду:

    ```bash  
        ruby -v
    ```

3. [Установите Git](https://git-scm.com) — широко распространенную систему управления версиями и исходным кодом. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующую команду:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Создание и запуск приложения

1. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Перейдите на новый сервер. Вы увидите запущенное веб-приложение.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [разработке для Azure Stack](azure-stack-dev-start.md).
- Дополнительные сведения о распространенных сценариях развертывания IaaS для Azure Stack см. [здесь](azure-stack-dev-start-deploy-app.md).
- Дополнительные сведения о языке программирования Ruby и дополнительные ресурсы для Python см. на сайте [Ruby-lang.org](https://www.ruby-lang.org).
