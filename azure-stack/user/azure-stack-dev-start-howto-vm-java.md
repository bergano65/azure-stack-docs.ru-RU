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
ms.openlocfilehash: e788be6315078fccee020fefe6ad79a20485c382
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482388"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Развертывание веб-приложения Java на виртуальной машине в Azure Stack

Вы можете создать виртуальную машину для размещения веб-приложения Python в Azure Stack. В этой статье рассматриваются шаги, которые нужно выполнить при настройке сервера, конфигурировании сервера для размещения вашего веб-приложения Python и последующем развертывании приложения.

Java — это универсальный язык компьютерного программирования, который является параллельным, основанным на классах, объектно-ориентированным и разработан с минимумом зависимостей реализации. Он предназначен для того, чтобы позволить разработчикам приложений "писать код один раз, запускать из любого расположения". Это означает, что скомпилированный код Java может работать на всех платформах, поддерживающих Java, без необходимости перекомпиляции. Дополнительные сведения о языке программирования Java и дополнительные ресурсы см. на сайте [Java.com](https://www.java.com).

В этой статье мы рассмотрим установку и настройку сервера Apache Tomcat на виртуальной машине Linux в Azure Stack, а затем загрузим файл ресурсов веб-приложения Java (WAR) на сервер. WAR-файл используется для распространения коллекции JAR-файлов, страниц JavaServer, Java-сервлетов, классов Java, файлов XML, библиотек тегов, статических веб-страниц (HTML и связанных файлов) и других ресурсов, которые вместе составляют веб-приложение.

Apache Tomcat, часто называемый Tomcat Server, является контейнером сервлетов Java с открытым кодом, разработанным Apache Software Foundation. Tomcat реализует спецификации технологий Java EE, включая сервлеты Java, страницы JavaServer, Java EL и WebSocket, а также обеспечивает чистую среду HTTP-веб-серверов Java для выполнения Java-кода.

## <a name="create-a-vm"></a>Создание виртуальной машины

1. Настройте виртуальную машину в Azure Stack. Выполните действия, описанные в статье [Развертывание виртуальной машины Linux для размещения веб-приложения в Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. В колонке виртуальной машины должны быть доступны следующие порты:

    | Порт | Протокол | ОПИСАНИЕ |
    | --- | --- | --- |
    | 80 | HTTP | Протокол передачи гипертекста (HTTP) — это прикладной протокол для распределенных, совместных информационных систем гиперсред. Клиенты будут подключаться к вашему веб-приложению с помощью общедоступного IP-адреса или DNS-имени вашей виртуальной машины. |
    | 443 | HTTPS | Протокол HTTPS является расширением протокола передачи гипертекста (HTTP). Он используется для безопасной связи через компьютерную сеть. Клиенты будут подключаться к вашему веб-приложению с помощью общедоступного IP-адреса или DNS-имени вашей виртуальной машины. |
    | 22 | SSH | Secure Shell (SSH) —это сетевой протокол шифрования, предназначенный для безопасного использования сетевых служб по незащищенной сети. Вы будете использовать это соединение с клиентом SSH для настройки виртуальной машины и развертывания приложений. |
    | 3389 | RDP | Необязательный элемент. Протокол удаленного рабочего стола позволяет выполнять подключение к удаленному рабочему столу, чтобы использовать графический пользовательский интерфейс вашего компьютера.   |
    | 8080 | Пользовательская | По умолчанию для службы Apache Tomcat используется порт 8080. Для сервера разработки вам необходимо перенаправить трафик через порты 80 и 443. |

## <a name="install-java"></a>Установка Java

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. В командной строке bash на виртуальной машине введите следующие команды:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Проверьте установку. Не прерывая подключение к виртуальной машине в сеансе SSH, введите следующие команды:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Установка и настройка Tomcat

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).

2. Создайте пользователя Tomcat.
    - Во-первых, создайте группу Tomcat.
        ```bash  
            sudo groupadd tomcat
        ```
     
    - Во-вторых, создайте пользователя Tomcat и сделайте его членом группы tomcat с домашним каталогом `/opt/tomcat`, куда вы будете устанавливать Tomcat с оболочкой `/bin/false` (чтобы никто не мог войти в учетную запись):
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Установите Tomcat.
    - Во-первых, получите URL-адрес tar для последней версии Tomcat 8 со страницы скачивания Tomcat 8 по адресу: [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
    - Во-вторых, с помощью curl скачайте последнюю версию по ссылке.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - В-третьих, установите Tomcat в каталог `/opt/tomcat`. Создайте каталог, затем распакуйте архив с помощью следующих команд:

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. Обновите разрешения для Tomcat.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Создайте служебный файл `systemd`, чтобы иметь возможность использовать Tomcat, как службу.

    - Tomcat должен знать, где установлен экземпляр ​​Java. Этот путь обычно называется **JAVA_HOME**. Найдите расположение, выполнив команду:

        ```bash  
            sudo update-java-alternatives -l
        ```

        Вы получите результат, похожий на приведенный ниже:

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        Значение переменной **JAVA_HOME** можно создать, используя путь из выходных данных, добавив `/jre`. Например, используя приведенный выше пример: `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`

    - Используйте значение своего сервера, чтобы создать файл службы systemd.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - Вставьте следующее содержимое в файл службы. При необходимости измените значение **JAVA_HOME**, чтобы оно соответствовало значению, найденному в вашей системе. Вы также можете изменить параметры выделения памяти, указанные в CATALINA_OPTS:

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

    - Сохраните и закройте файл.
    - Перезагрузите управляющую программу systemd, чтобы указать ей данные о служебном файле:

        ```bash  
            sudo systemctl daemon-reload
        ```

    - Запуск службы Tomcat 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Убедитесь, что она работает без ошибок, введя:

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Проверьте сервер Tomcat. Для приема обычных запросов Tomcat использует порт 8080. Разрешите поток трафика на этот порт, выполнив следующую команду:

    ```bash  
        sudo ufw allow 8080
    ```

    Если вы не добавили **правила для входящих портов** для виртуальной машины Azure Stack, добавьте их. Дополнительные сведения см. в разделе [о создании виртуальной машины](#create-a-vm).

7. Откройте браузер в сети Azure Stack и откройте сервер: `yourmachine.local.cloudapp.azurestack.external:8080`

    ![Apache Tomcat на виртуальной машине Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    На вашем сервере загружается страница Apache Tomcat. Затем настройте сервер, чтобы получить доступ к состоянию сервера, приложению диспетчера и диспетчеру узлов.

8. Включите служебный файл, чтобы Tomcat автоматически запускался при перезагрузке сервера:

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Настройте сервер Tomcat, чтобы получить доступ к веб-интерфейсу управления. Измените `tomcat-users.xml` и определите роль и пользователя, чтобы войти в систему. Определите пользователя, чтобы получить доступ к `manager-gui` и `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Добавьте в раздел `<tomcat-users>` следующие элементы:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Например, ваш окончательный файл может выглядеть примерно так:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Сохраните и закройте файл.


10. Tomcat ограничивает доступ к приложениям **менеджера** и **менеджера узлов** для подключений, исходящих с сервера. Так как вы устанавливаете Tomcat на виртуальную машину в Azure Stack, снимите это ограничение. Измените ограничения IP-адресов для этих приложений, отредактировав соответствующие файлы `context.xml`.

    - Обновите приложение диспетчера `context.xml`:

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Закомментируйте ограничение IP-адреса, чтобы разрешить подключения из любого расположения, или добавьте IP-адрес компьютера, который вы используете для подключения к Tomcat.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Сохраните и закройте файл.

    - Обновите приложение менеджера узлов `context.xml` с помощью следующего обновления:

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Сохраните и закройте файл.

11. Перезапустите службу Tomcat, чтобы внести на сервер следующие изменения:

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Откройте браузер в сети Azure Stack и откройте сервер: `yourmachine.local.cloudapp.azurestack.external:8080`

    - Щелкните "Состояние сервера", чтобы проверить состояние сервера Tomcat и убедиться, что у вас есть к нему доступ.
    - Войдите, используя свои учетные данные Tomcat.

![Apache Tomcat на виртуальной машине Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Создание приложения

Создайте WAR-файл, чтобы развернуть Tomcat. Если вы хотите проверить свою среду, ознакомьтесь с примером WAR-файла на сайте Apache TomCat: [пример приложения](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Инструкции по разработке приложений Java в Azure см. в статье [Создание и развертывание приложений Java в Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Развертывание и запуск приложения

1. Подключитесь к виртуальной машине c помощью клиента SSH. Инструкции см. в разделе [Подключение по протоколу SSH с помощью PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Остановите службу Tomcat, чтобы обновить сервер с помощью пакета приложений:

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Добавьте пользователя FTP в группу tomcat, чтобы иметь возможность выполнять запись в папку webapps. Пользователь FTP — это пользователь, которого вы определяете при создании виртуальной машины в Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Подключитесь к виртуальной машине с помощью FileZilla, чтобы очистить папку webapps, а затем загрузите новый или обновленный WAR-файл. Дополнительные сведения по использованию FileZila см. в разделе [Подключение к SFTP в FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).
    - Выполните очистку `TOMCAT_HOME/webapps`.
    - Добавьте WAR-файл в ` TOMCAT_HOME/webapps`, например `/opt/tomcat/webapps/`.

4.  Tomcat автоматически расширяет и развертывает приложение. Его можно просмотреть с помощью DNS-имени, которое вы создали ранее. Например: 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).