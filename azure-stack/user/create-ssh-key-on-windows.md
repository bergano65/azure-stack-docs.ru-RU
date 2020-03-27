---
title: Развертывание кластера Kubernetes в пользовательской виртуальной сети Azure Stack Hub
description: Сведения о том, как развернуть кластер Kubernetes в пользовательской виртуальной сети Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: e82ddb48b3858acdf25163976854f538400da54b
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069197"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>Создание ключа SSH для Linux в Azure Stack Hub

Вы можете создать ключ SSH (Secure Shell) для компьютера Linux на компьютере с Windows. Открытый ключ, созданный по инструкциям из этой статьи, можно применять для аутентификации на основе SSH на виртуальных машинах. Если вы используете компьютер под управлением Windows, установите Ubuntu в Windows, чтобы использовать терминал с такими служебными программами, как bash, ssh и git. Выполните команду **ssh-keygen**, чтобы создать ключ.

## <a name="open-bash-on-windows"></a>Запуск Bash в Windows

1. Если на компьютере не установлена подсистема Windows для Linux, установите [Ubuntu в Windows](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).  
    Подробные сведения о подсистеме Windows для Linux см. в [этой документации](https://docs.microsoft.com/windows/wsl/about).

2. На панели инструментов введите **Ubuntu** и выберите **Открыть**.

## <a name="create-a-key-with-ssh-keygen"></a>Создание ключа с помощью ssh-keygen

1. Введите следующие команды в командной строке Bash:

    ```bash  
    ssh-keygen -t rsa
    ```

    В Bash отобразится следующий запрос:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Введите здесь имя файла и парольную фразу. Повторите ввод парольной фразы.

    В Bash отобразятся следующие сведения:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. Чтобы просмотреть содержимое открытого ключа SSH, выполните следующую команду:

    ```bash
    cat /home/<username>/<filename>
    ```

    В Bash отобразятся примерно такие результаты:

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Скопируйте текст `ssh-rsa [...]` вплоть до `username@machinename`. Следите за тем, чтобы этот текст не содержал возвратов каретки. Этот текст вам пригодится при создании виртуальной машины или кластера Kubernetes с помощью подсистемы AKS.

5. Если вы работаете на компьютере Windows, к файлам Linux можно обращаться с помощью **\\\\wsl$** .

    1. Введите `\\wsl$` на панели инструментов. Стандартное окно, открываемое для дистрибутива.

    2. Перейдите к `\\wsl$\Ubuntu\home\<username>` и найдите здесь открытый и закрытый ключи, затем сохраните их в надежном расположении.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание кластера Kubernetes с обработчиком AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Краткое руководство. Создание виртуальной машины с сервером Linux с помощью портала Azure Stack Hub](azure-stack-quick-linux-portal.md).