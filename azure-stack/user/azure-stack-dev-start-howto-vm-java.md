---
title: Развертывание WAR-файла Java на виртуальной машине в Azure Stack | Документация Майкрософт
description: Разверните WAR-файл Java на виртуальной машине в Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 1738d106a0688518f7a739d3fb02ec1b16c2b8b9
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838369"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Развертывание веб-приложения Java на виртуальной машине в Azure Stack

Вы можете создать виртуальную машину для размещения веб-приложения Python в Azure Stack. В рамках этой статьи вы установите и настроите сервер Apache Tomcat на виртуальной машине Linux в Azure Stack. Затем вы загрузите на сервер файл ресурсов веб-приложения Java (WAR). WAR-файл используется для распространения коллекции файлов архива Java (JAR-файлы). Это сжатые файлы, содержащие ресурсы Java, такие как классы, текст, изображения, XML- и HTML-файлы, а также другие ресурсы, которые используются для предоставления веб-приложения.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Настройте виртуальную машину в Azure Stack, следуя инструкциям из статьи [Развертывание виртуальной машины Linux для размещения веб-приложения в Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. В области сети виртуальных машин разрешите доступ к следующим портам:

    | Порт | Протокол | ОПИСАНИЕ |
    | --- | --- | --- |
    | 80 | HTTP | HTTP — это протокол, который используется для доставки веб-страниц с серверов. Клиенты подключаются по протоколу HTTP, используя DNS-имя или IP-адрес. |
    | 443 | HTTPS | HTTPS — это безопасная версия протокола HTTP, которая использует сертификат безопасности и обеспечивает передачу данных в зашифрованном виде. |
    | 22 | SSH | Secure Shell (SSH) — это сетевой протокол с применением шифрования для безопасного обмена данными. Такое соединение используется клиентом SSH для настройки виртуальной машины и развертывания приложений. |
    | 3389 | RDP | Необязательный элемент. Протокол RDP позволяет подключаться к удаленному рабочему столу, чтобы использовать графический пользовательский интерфейс на вашем компьютере.   |
    | 8080 | Пользовательская | Используемый по умолчанию порт для службы Apache Tomcat. Для рабочего сервера разработки вам нужно перенаправить трафик через порты 80 и 443. |

## <a name="install-java"></a>Установка Java

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. В командной строке bash на виртуальной машине выполните следующую команду:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Проверьте установку. Не прерывая подключение к виртуальной машине в сеансе SSH, выполните следующую команду:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Установка и настройка Tomcat

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Создайте пользователя Tomcat. Для этого сделайте следующее:

    a. Создайте группу Tomcat с помощью приведенной ниже команды:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Создайте пользователя Tomcat. Добавьте этого пользователя в группу Tomcat с домашним каталогом */opt/tomcat*. Разверните Tomcat в этот каталог.

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Установите Tomcat. Для этого сделайте следующее:

    a. Получите URL-адрес tar для последней версии Tomcat 8 на [странице скачивания Tomcat 8](http://tomcat.apache.org/download-80.cgi).

    b. С помощью cURL скачайте последнюю версию по ссылке. Выполните следующие команды:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Установите Tomcat в каталог */opt/tomcat*. Создайте папку и откройте архив.

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Обновите разрешения для Tomcat с помощью следующих команд:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Создайте файл службы *systemd*, чтобы можно было использовать Tomcat как службу.

   a. В Tomcat нужно указать путь к установленному экземпляру ​​Java. Этот путь обычно называется *JAVA_HOME*. Найдите расположение, выполнив команду:

    ```bash  
        sudo update-java-alternatives -l
    ```

    Должны отобразиться следующие выходные данные:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Вы можете создать переменную *JAVA_HOME*, используя путь из выходных данных и добавив к нему */jre*. В нашем примере путь будет таким: */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. С помощью значения своего сервера создайте файл службы systemd.

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Вставьте следующее содержимое в файл службы. При необходимости измените значение *JAVA_HOME* таким образом, чтобы оно соответствовало значению, найденному в вашей системе. Вы также можете изменить параметры выделения памяти, указанные в CATALINA_OPTS.

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. Сохраните и закройте файл.

    д. Перезагрузите управляющую программу systemd, чтобы указать ей данные о служебном файле:

    ```bash  
        sudo systemctl daemon-reload
    ```

    Е. Запустите службу Tomcat. 

    ```bash  
        sudo systemctl start tomcat
    ```

    ж. Убедитесь, что она работает без ошибок. Для этого введите следующую команду:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Проверьте сервер Tomcat. Для приема обычных запросов Tomcat использует порт 8080. Разрешите поток трафика на этот порт, выполнив следующую команду:

    ```bash  
        sudo ufw allow 8080
    ```

    Если вы не добавили *правила для входящих портов* для виртуальной машины Azure Stack, добавьте их сейчас. Дополнительные сведения см. в разделе [о создании виртуальной машины](#create-a-vm).

1. Откройте браузер в той же сети Azure Stack, а затем откройте сервер *yourmachine.local.cloudapp.azurestack.external:8080*.

    ![Apache Tomcat на виртуальной машине Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    На вашем сервере загружается страница Apache Tomcat. Далее настройте сервер, чтобы получить доступ к сведениям о его состоянии, приложению диспетчера и диспетчеру узлов.

1. Включите служебный файл, чтобы Tomcat автоматически запускался при перезагрузке сервера:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Настройте сервер Tomcat, чтобы получить доступ к веб-интерфейсу управления. 

   a. Измените файл *tomcat-users.xml* и определите роль и пользователя, чтобы войти в систему. Определите пользователя, чтобы получить доступ к `manager-gui` и `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Добавьте в раздел `<tomcat-users>` следующие элементы:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Ваш окончательный файл может выглядеть примерно так:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Сохраните и закройте файл.

1. Tomcat ограничивает доступ к приложениям *менеджера* и *менеджера узлов* для подключений, исходящих с сервера. Так как вы устанавливаете Tomcat на виртуальную машину в Azure Stack, снимите это ограничение. Измените ограничения IP-адресов для этих приложений, отредактировав соответствующие файлы *context.xml*.

    a. Обновите *context.xml* в приложении диспетчера.

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Закомментируйте ограничение IP-адреса, чтобы разрешить подключения из любого расположения, или добавьте IP-адрес компьютера, который используете для подключения к Tomcat.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Сохраните и закройте файл.

    d. Обновите файл *context.xml* диспетчера узлов аналогичным образом.

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    д. Сохраните и закройте файл.

1. Перезапустите службу Tomcat, чтобы применить изменения на сервере.

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Откройте браузер в той же сети Azure Stack, а затем откройте сервер *yourmachine.local.cloudapp.azurestack.external:8080*.

    a. Щелкните **Server Status** (Состояние сервера), чтобы проверить состояние сервера Tomcat и убедиться, что у вас есть к нему доступ.

    b. Войдите, используя свои учетные данные Tomcat.

    ![Apache Tomcat на виртуальной машине Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Создание приложения

Нужно создать WAR-файл, чтобы развернуть Tomcat. Если вам нужно только проверить свою среду, ознакомьтесь с примером WAR-файла на [сайте Apache TomCat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Инструкции по разработке приложений Java в Azure см. в статье [Создание и развертывание приложений Java в Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Развертывание и запуск приложения

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Остановите работу службы Tomcat, чтобы загрузить на сервер пакет приложения.

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Добавьте пользователя FTP в группу Tomcat, чтобы можно было выполнять запись в папку webapps. Пользователь FTP — это пользователь, которого вы определяете при создании виртуальной машины в Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Подключитесь к виртуальной машине с помощью FileZilla, чтобы очистить папку webapps, а затем загрузите новый или обновленный WAR-файл. Дополнительные сведения об использовании FileZila см. в разделе [Подключение по SFTP с помощью FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Очистите папку *TOMCAT_HOME/webapps*.

    b. Скопируйте свой WAR-файл в папку *TOMCAT_HOME/webapps* (например */opt/tomcat/webapps/*).

1.  Tomcat автоматически расширяет и развертывает приложение. Его можно просмотреть с помощью DNS-имени, которое вы создали ранее. Например: 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [develop for Azure Stack](azure-stack-dev-start.md).
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).
