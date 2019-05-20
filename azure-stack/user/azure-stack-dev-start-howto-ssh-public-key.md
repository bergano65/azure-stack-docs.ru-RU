---
title: Использование открытого ключа SSH с помощью Azure Stack | Документация Майкрософт
description: Использование открытого ключа SSH
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0f4133052022963e1ed0457dd479a00bcc5bb749
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490062"
---
# <a name="how-to-use-an-ssh-public-key"></a>Использование открытого ключа SSH

Вам может потребоваться создать пару из открытого и закрытого ключей SSH, чтобы использовать открытое SSH-подключение с компьютера разработки и сервера виртуальной машины в Azure Stack, где размещено веб-приложение. В этой статье описаны шаги получения ключей и их использования для подключения к серверу. Вы можете использовать клиент SSH, чтобы получить строку bash на сервере Linux, или клиент SFTP, чтобы переместить файлы на сервер и с него.

## <a name="create-an-ssh-public-key-on-windows"></a>Создание открытого ключа SSH в Windows

В этом разделе вы будете использовать генератор ключей PuTTY, чтобы создать пару из открытого и закрытого ключей SSH и использовать ее при создании безопасного подключения к компьютерам Linux в Azure Stack. PuTTY — это бесплатная реализация SSH и Telnet для платформ Windows и Unix с эмулятором терминала `xterm`.

1. [Скачайте и установите PuTTY на свой компьютер.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Откройте генератор ключей PuTTY.

1. В области **Параметры** задайте **RSA**.

1. Укажите такое количество битов в созданном ключе: `2048`.  

    ![Использование PuTTY для создания открытого ключа SSH](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Выберите **Создать**. В области **Ключ** создайте случайный набор знаков, переместив курсор на пустую область.

1. Добавьте **парольную фразу ключа** и подтвердите это значение в поле **Подтвердить**. Запишите парольную фразу.
    ![Использование PuTTY для создания открытого ключа SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Выберите **Save the public key** (Сохранить открытый ключ) и сохраните ключ в доступном расположении.

1. Выберите **Save private key** (Сохранить закрытый ключ) и сохраните ключ в доступном расположении. Помните, что расположение принадлежит открытому ключу.

Открытый ключ хранится в сохраненном текстовом файле. Если вы откроете его, он будет содержать такой текст:

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

При использовании открытого ключа вы копируете и вставляете все контексты текстового поля в качестве значения, когда приложение запросит ключ.

<!-- 
## Create an SSH public key on Linux

ToDo: I need to write this section.

-->
## <a name="connect-with-ssh-using-putty"></a>Подключение по SSH с помощью PuTTY

Если вы установили PuTTY, у вас есть генератор ключей и клиент PuTTY. Откройте клиент SSH, PuTTY, настройте значения подключения и ключ SSH, и если вы работаете в той же сети, что и Azure Stack, подключитесь к виртуальной машине.

Прежде чем подключиться, вам потребуется:
- PuTTY;
- IP-адрес и имя пользователя для компьютера Linux в Azure Stack, использующего открытый ключ SSH в качестве типа аутентификации;
- открытый порт 22 для компьютера;
- открытый ключ SSH, который использовался при создании компьютера;
- клиентский компьютер с PuTTY, запущенным в той же сети, что и Azure Stack.

### <a name="connect-via-ssh-with-putty"></a>Подключение через SSH с помощью PuTTy

1. Откройте PuTTy.

    ![Использование PuTTy для подключения](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Добавьте имя пользователя и общедоступный IP-адрес компьютера. Например, `username@192.XXX.XXX.XX` в качестве **имени узла**. 
3. Убедитесь, что в поле **Порт** установлено значение `22`, а в области **Тип подключения** — значение `SSH`.
4. Разверните узлы **SSH** > **Проверка подлинности** в дереве **Категория**.

    ![Закрытый ключ SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Выберите **Обзор** и найдите файл закрытого ключа (filename.ppk) пары открытого и закрытого ключей.
6. Выберите "Сеанс" в дереве "Категория".

    ![Открытый и закрытый ключи SSH](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Введите имя для сеанса в разделе **Saved Sessions** (Сохраненные сеансы) и выберите **Сохранить**.
8. Выберите имя сеанса и щелкните **Загрузить**.
9. Щелкните **Open**(Открыть). Откроется сеанс SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Подключение по SFTP с помощью FileZilla

Вы можете использовать FileZilla в качестве клиента FTP, который поддерживает SFTP для перемещения файлов на компьютер Linux и с него. FileZilla работает в системе Windows 10, Linux и macOS. Клиент FileZilla поддерживает FTP, а также FTP через TLS (FTPS) и SFTP. Это программное обеспечение с открытым кодом, которое распространяется бесплатно в соответствии с условиями универсальной общедоступной лицензии GNU.

### <a name="set-your-connection"></a>Настройка подключения

1. [Скачайте и установите FileZilla](https://filezilla-project.org/download.php).
1. Откройте FileZilla.
1. Выберите **File** > **Site Manager** (Файл > Диспетчер сайтов).

    ![Открытый и закрытый ключи SSH](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Выберите **протокол SFTP (протокол передачи файлов SSH)** для параметра **Протокол**.
1. Добавьте общедоступный IP-адрес для компьютера в поле **Узел**.
1. Для параметра **sign in Type** (Тип входа) выберите **Normal** (Обычный).
1. Добавьте имя пользователя и пароль.
1. Нажмите кнопку **ОК**.
1. Выберите **Изменить** > **Параметры**.

    ![Открытый и закрытый ключи SSH](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Разверните узел **Подключение** в дереве **Выбор страницы**. Выберите **SFTP**.
1. Выберите **Add key file** (Добавить файл ключа) и добавьте файл закрытого ключа, например filename.ppk.
1. Нажмите кнопку **ОК**.

### <a name="open-your-connection"></a>Открытие подключения

1. Откройте FileZilla.
1. Выберите **File** > **Site Manager** (Файл > Диспетчер сайтов).
1. Выберите имя сайта и щелкните **Подключить**.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о разработке для Azure Stack см. [здесь](azure-stack-dev-start.md).