---
title: Использование открытого ключа SSH с помощью Azure Stack | Документация Майкрософт
description: Использование открытого ключа SSH
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 3d2854511415421b69a6972cd807132639300f96
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824511"
---
# <a name="use-an-ssh-public-key"></a>Использование открытого ключа SSH

Чтобы использовать открытое SSH-подключение на компьютере разработки и сервере виртуальной машины в Azure Stack, на котором размещено ваше веб-приложение, возможно, вам потребуется создать пару из открытого и закрытого ключей SSH. 

В рамках этой статьи вы создадите ключи и с их помощью установите подключение к серверу. Вы можете использовать клиент SSH, чтобы получить строку bash на сервере Linux, или клиент SFTP, чтобы переместить файлы на сервер и с него.

## <a name="create-an-ssh-public-key-on-windows"></a>Создание открытого ключа SSH в Windows

В рамках этого раздела используется генератор ключей PuTTY, чтобы создать пару из открытого и закрытого ключей SSH и использовать ее при создании безопасного подключения к виртуальной машине Linux в Azure Stack. PuTTY — это бесплатный эмулятор терминала, с помощью которого можно подключаться к серверу по протоколам SSH и Telnet.

1. [Скачайте и установите PuTTY на свой компьютер.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Откройте генератор ключей PuTTY.

    ![Генератор ключей PuTTY с пустым полем ключа](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. В разделе **Parameters** (Параметры) выберите **RSA**.

1. В поле **Number of bits in a generated key** (Количество битов в созданном ключе) введите **2048**.  

1. Выберите **Создать**.

1. В области **Key** (Ключ) создайте случайный набор знаков, переместив курсор в пустую область.

    ![Генератор ключей PuTTY с заполненным полем ключа](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. В поле **Key passphrase** (Парольная фраза ключа) задайте пароль и подтвердите его в поле **Confirm passphrase** (Подтверждение парольной фразы ключа). Сохраните парольную фразу на будущее.

1. Выберите **Save public key** (Сохранить открытый ключ) и сохраните ключ в доступном расположении.

1. Выберите **Save private key** (Сохранить закрытый ключ) и сохраните ключ в доступном расположении. Учитывайте, что он закрытый ключ привязан к открытому.

Открытый ключ хранится в сохраненном текстовом файле. Файл выглядит примерно так:

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Когда приложение запрашивает ключ, нужно скопировать и вставить все содержимое текстового файла.

## <a name="connect-with-ssh-by-using-putty"></a>Подключение по протоколу SSH с помощью PuTTy

При установке PuTTY также устанавливаются генератор ключей PuTTY и клиент SSH. В рамках этого раздела вы откроете клиент SSH и PuTTY, а также настроите значения подключения и ключ SSH. В сети, в которой размещен экземпляр Azure Stack, подключитесь к виртуальной машине.

Прежде чем подключиться, вам потребуется:
- PuTTY;
- IP-адрес и имя пользователя для виртуальной машины Linux в экземпляре Azure Stack, использующей открытый ключ SSH в качестве типа аутентификации;
- открытый порт 22 для виртуальной машины;
- открытый ключ SSH, который использовался при создании виртуальной машины;
- клиентский компьютер с PuTTY должен находится в той же сети, что и Azure Stack.

1. Откройте PuTTY.

    ![Окно настроек PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. В поле **Host Name (or IP address)** (Имя узла или IP-адрес) введите имя пользователя и общедоступный IP-адрес виртуальной машины (например **username@192.XXX.XXX.XX** ). 
3. Убедитесь, что в поле **Port** (Порт) указано значение**22**, а в поле **Connection type** (Тип подключения) — **SSH**.
4. В дереве **Category** (Категория) разверните узлы **SSH** и **Auth**.

    ![Окно настроек PuTTY — закрытый ключ SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Рядом с полем **Private key file for authentication** (Файл закрытого ключа для аутентификации) выберите **Browse** (Обзор) и выполните поиск файла закрытого ключа ( *\<имя_файла>.ppk*) своей пары открытого и закрытого ключей.
6. В дереве **Category** (Категория) выберите **Session** (Сеанс).

    ![Окно настроек PuTTY — поле раздела Saved Sessions (Сохраненные сеансы)](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. В разделе **Saved Sessions** (Сохраненные сеансы) введите имя сеанса и нажмите кнопку **Save** (Сохранить).
8. В списке **Saved Sessions** (Сохраненные сеансы) выберите имя своего сеанса и нажмите кнопку **Load** (Загрузить).
9. Щелкните **Open**(Открыть). Откроется сеанс SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Подключение по SFTP с помощью FileZilla

Чтобы обмениваться файлами с виртуальной машиной Linux, вы можете использовать FileZilla в качестве клиента FTP, который поддерживает протокол SFTP. FileZilla работает в системе Windows 10, Linux и macOS. Клиент FileZilla поддерживает протоколы FTP, FTPS и SFTP. Это программное обеспечение с открытым кодом, которое распространяется бесплатно в соответствии с условиями универсальной общедоступной лицензии GNU.

### <a name="set-your-connection"></a>Настройка подключения

1. [Скачайте и установите FileZilla](https://filezilla-project.org/download.php).
1. Откройте FileZilla.
1. Выберите **File** > **Site Manager** (Файл > Диспетчер сайтов).

    ![Окно диспетчера сайтов FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. В раскрывающемся списке **Protocol** (Протокол) выберите **SFTP — SSH File Transfer Protocol** (SFTP — протокол передачи файлов SSH).
1. В поле **Host** (Узел) укажите общедоступный IP-адрес виртуальной машины.
1. В поле **Logon Type** (Тип входа) выберите **Normal** (Обычный).
1. Введите имя пользователя и пароль.
1. Нажмите кнопку **ОК**.
1. Выберите **Изменить** > **Параметры**.

    ![Окно настроек FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. В дереве **Select page** (Выбор страницы) разверните узел **Connection** (Подключение) и выберите **SFTP**.
1. Нажмите кнопку **Add key file** (Добавить файл ключа) и выберите файл своего закрытого ключа( *\<filename>.ppk*).
1. Нажмите кнопку **ОК**.

### <a name="open-your-connection"></a>Открытие подключения

1. Откройте FileZilla.
1. Выберите **File** > **Site Manager** (Файл > Диспетчер сайтов).
1. Выберите имя сайта и нажмите кнопку **Connect** (Подключить).

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [настроить среду разработки в Azure Stack](azure-stack-dev-start.md).
