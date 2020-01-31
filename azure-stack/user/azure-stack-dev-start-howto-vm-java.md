---
title: Развертывание WAR-файла Java на виртуальной машине в Azure Stack Hub
description: Разверните WAR-файл Java на виртуальной машине в Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6a696417b296da978b2d077c6f2c01b94a106d2e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884907"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack-hub"></a>Развертывание веб-приложения Java на виртуальной машине в Azure Stack Hub

Вы можете создать виртуальную машину для размещения веб-приложения Python в Azure Stack Hub. В рамках этой статьи вы установите и настроите сервер Apache Tomcat на виртуальной машине Linux в Azure Stack Hub. Затем вы загрузите на сервер файл ресурсов веб-приложения Java (WAR). WAR-файл используется для распространения коллекции файлов архива Java (JAR-файлы). Это сжатые файлы, содержащие ресурсы Java, такие как классы, текст, изображения, XML- и HTML-файлы, а также другие ресурсы, которые используются для предоставления веб-приложения.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Настройте виртуальную машину в Azure Stack Hub, следуя инструкциям из статьи о [развертывании виртуальной машины Linux для размещения веб-приложения в Azure Stack Hub](azure-stack-dev-start-howto-deploy-linux.md).

2. В области сети виртуальных машин разрешите доступ к следующим портам:

    | Порт | Протокол | Описание |
    | --- | --- | --- |
    | 80 | HTTP | HTTP — это протокол, который используется для доставки веб-страниц с серверов. Клиенты подключаются по протоколу HTTP, используя DNS-имя или IP-адрес. |
    | 443 | HTTPS | HTTPS — это безопасная версия протокола HTTP, которая использует сертификат безопасности и обеспечивает передачу данных в зашифрованном виде. |
    | 22 | SSH | Secure Shell (SSH) — это сетевой протокол с применением шифрования для безопасного обмена данными. Такое соединение используется клиентом SSH для настройки виртуальной машины и развертывания приложений. |
    | 3389 | RDP | Необязательный параметр. Протокол RDP позволяет подключаться к удаленному рабочему столу, чтобы использовать графический пользовательский интерфейс на вашем компьютере.   |
    | 8080 | Другой | Используемый по умолчанию порт для службы Apache Tomcat. Для рабочего сервера разработки вам нужно перенаправить трафик через порты 80 и 443. |

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

    а. Создайте группу Tomcat с помощью приведенной ниже команды:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Создайте пользователя Tomcat. Добавьте этого пользователя в группу Tomcat с домашним каталогом */opt/tomcat*. Разверните Tomcat в этот каталог.

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Установите Tomcat. Для этого сделайте следующее:

    а. Получите URL-адрес tar для последней версии Tomcat 8 на [странице скачивания Tomcat 8](http://tomcat.apache.org/download-80.cgi).

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

   а. В Tomcat нужно указать путь к установленному экземпляру ​​Java. Этот путь обычно называется *JAVA_HOME*. Найдите расположение, выполнив команду:

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

    d. Сохраните файл и закройте его.

    д) Перезагрузите управляющую программу systemd, чтобы указать ей данные о служебном файле:

    ```bash  
        sudo systemctl daemon-reload
    ```

    е) Запустите службу Tomcat. 

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

    Если вы не добавили *правила для входящих портов* для виртуальной машины Azure Stack Hub, добавьте их сейчас. Дополнительные сведения см. в разделе [о создании виртуальной машины](#create-a-vm).

1. Откройте браузер в той же сети Azure Stack Hub, а затем откройте сервер *yourmachine.local.cloudapp.azurestack.external:8080*.

    ![Apache Tomcat на виртуальной машине Azure Stack Hub](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    На вашем сервере загружается страница Apache Tomcat. Далее настройте сервер, чтобы получить доступ к сведениям о его состоянии, приложению диспетчера и диспетчеру узлов.

1. Включите служебный файл, чтобы Tomcat автоматически запускался при перезагрузке сервера:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Настройте сервер Tomcat, чтобы получить доступ к веб-интерфейсу управления. 

   а. Измените файл *tomcat-users.xml* и определите роль и пользователя, чтобы войти в систему. Определите пользователя, чтобы получить доступ к `manager-gui` и `admin-gui`.

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

    c. Сохраните файл и закройте его.

1. Tomcat ограничивает доступ к приложениям *менеджера* и *менеджера узлов* для подключений, исходящих с сервера. Так как вы устанавливаете Tomcat на виртуальную машину в Azure Stack Hub, снимите это ограничение. Измените ограничения IP-адресов для этих приложений, отредактировав соответствующие файлы *context.xml*.

    а. Обновите *context.xml* в приложении диспетчера.

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

    c. Сохраните файл и закройте его.

    d. Обновите файл *context.xml* диспетчера узлов аналогичным образом.

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    д) Сохраните файл и закройте его.

1. Перезапустите службу Tomcat, чтобы применить изменения на сервере.

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Откройте браузер в той же сети Azure Stack Hub, а затем откройте сервер *yourmachine.local.cloudapp.azurestack.external:8080*.

    а. Щелкните **Server Status** (Состояние сервера), чтобы проверить состояние сервера Tomcat и убедиться, что у вас есть к нему доступ.

    b. Войдите, используя свои учетные данные Tomcat.

    ![Apache Tomcat на виртуальной машине Azure Stack Hub](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Создание приложения

Нужно создать WAR-файл, чтобы развернуть Tomcat. Если вам нужно только проверить свою среду, ознакомьтесь с примером WAR-файла на [сайте Apache TomCat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Инструкции по разработке приложений Java в Azure см. в статье [Создание и развертывание приложений Java в Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Развертывание и запуск приложения

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Остановите работу службы Tomcat, чтобы загрузить на сервер пакет приложения.

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Добавьте пользователя FTP в группу Tomcat, чтобы можно было выполнять запись в папку webapps. Пользователь FTP — это пользователь, которого вы определяете при создании виртуальной машины в Azure Stack Hub.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Подключитесь к виртуальной машине с помощью FileZilla, чтобы очистить папку webapps, а затем загрузите новый или обновленный WAR-файл. Дополнительные сведения об использовании FileZila см. в разделе [Подключение по SFTP с помощью FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    а. Очистите папку *TOMCAT_HOME/webapps*.

    b. Скопируйте свой WAR-файл в папку *TOMCAT_HOME/webapps* (например */opt/tomcat/webapps/* ).

1.  Tomcat автоматически расширяет и развертывает приложение. Его можно просмотреть с помощью DNS-имени, которое вы создали ранее. Пример:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [разработке для Azure Stack Hub](azure-stack-dev-start.md).
- Дополнительные сведения о распространенных сценариях развертывания IaaS для Azure Stack Hub см. [здесь](azure-stack-dev-start-deploy-app.md).
- Дополнительные сведения о языке программирования Java и дополнительные ресурсы см. на сайте [Java.com](https://www.java.com).
