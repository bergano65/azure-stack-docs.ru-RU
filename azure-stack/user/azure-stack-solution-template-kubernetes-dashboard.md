---
title: Получение доступа к панели мониторинга Kubernetes в Azure Stack Hub
description: Сведения о получении доступа к панели мониторинга Kubernetes в Azure Stack Hub
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: e2b6598137774a5bf654aef1f9a75827da4f108a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703644"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack-hub"></a>Получение доступа к панели мониторинга Kubernetes в Azure Stack Hub 

> [!Note]   
> Используйте элемент Kubernetes Azure Stack Marketplace для развертывания кластеров в качестве проверки концепции. Для поддерживаемых кластеров Kubernetes в Azure Stack используйте[обработчик AKS](azure-stack-kubernetes-aks-engine-overview.md).

Служба Kubernetes включает в себя веб-панель мониторинга, которую можно использовать для выполнения базовых операций управления. Эта панель мониторинга позволяет просматривать базовое состояние работоспособности и метрики для приложений, создавать и развертывать службы, а также изменять существующие приложения. В этой статье показано, как настроить панель мониторинга Kubernetes в Azure Stack Hub.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Необходимые компоненты для настройки доступа к панели мониторинга Kubernetes

* кластер Kubernetes в Azure Stack Hub

    Вам нужно будет развернуть кластер Kubernetes в Azure Stack Hub. Дополнительные сведения см. в статье [Развертывание Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

* Клиент SSH.

    SSH-клиент необходим для безопасного подключения к главному узлу в кластере. В Windows вы можете использовать [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Вам понадобится закрытый ключ, используемый при развертывании кластера Kubernetes.

* Протокол FTP (PSCP).

    Вам также может понадобиться FTP-клиент, который поддерживает SSH и протокол передачи файлов SSH для передачи сертификатов с главного узла на ваш компьютер для управления Azure Stack Hub. Вы можете использовать [FileZilla](https://filezilla-project.org/download.php?type=client). Вам понадобится закрытый ключ, используемый при развертывании кластера Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Обзор шагов для включения панели мониторинга

1.  Экспорт сертификатов Kubernetes с главного узла в кластере. 
2.  Импорт сертификатов на компьютер для управления Azure Stack Hub.
2.  Открытие веб-панели мониторинга Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Экспорт сертификата с главного узла 

Вы можете извлечь URL-адрес для панели мониторинга с главного узла в вашем кластере.

1. Получите общедоступный IP-адрес и имя пользователя для главного кластера на панели мониторинга Azure Stack Hub. Для этого сделайте следующее:

    - Войдите на [портал Azure Stack Hub](https://portal.local.azurestack.external/)
    - Выберите **Все службы** > **Все ресурсы**. Найдите главный узел в группе кластерных ресурсов. Имя главного узла —`k8s-master-<sequence-of-numbers>`. 

2. Откройте главный узел на портале. Скопируйте **общедоступный IP-адрес**. Щелкните **Подключиться**, чтобы имя пользователя отобразилось в поле **Вход с использованием локальной учетной записи ВМ**. Это имя пользователя вы указали при создании кластера. Используйте общедоступный IP-адрес вместо частного адреса, указанного в колонке подключения.

3.  Подключитесь к главному узлу, используя клиент SSH. Если вы работаете в Windows, можно использовать [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) для создания подключения. Вы будете использовать общедоступный IP-адрес и имя пользователя для главного узла, а также добавите закрытый ключ, указанный при создании кластера.

4.  После подключения терминала введите `kubectl`, чтобы открыть клиент командной строки Kubernetes.

5. Выполните следующую команду:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Найдите URL-адрес для панели мониторинга. Например:  `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Извлеките самозаверяющий сертификат и преобразуйте его в формат PFX. Выполните следующую команду:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Получите список секретов в пространстве имен **kube-system**. Выполните следующую команду:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Запишите значение kubernetes-dashboard-token-\<XXXXX>. 

8.  Получите и сохраните токен. Обновите значение `kubernetes-dashboard-token-<####>`, указав значение секрета из предыдущего шага.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Импорт сертификата

1. Откройте Filezilla и подключитесь к главному узлу. Вам потребуется следующее:

    - общедоступный IP-адрес главного узла;
    - имя пользователя;
    - секрет;
    - **протокол SFTP (протокол передачи файлов SSH)** .

2. Скопируйте `/etc/kubernetes/certs/client.pfx` и `/etc/kubernetes/certs/ca.crt` на свой компьютер для управления Azure Stack Hub.

3. Запишите расположения файлов. Обновите сценарий, указав эти расположения, а затем откройте PowerShell в командной строке с повышенными привилегиями. Выполните обновленный сценарий.  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Открытие панели мониторинга Kubernetes 

1. Отключите блокировку всплывающих окон в веб-браузере.

2. Укажите в браузере URL-адрес, записанный после выполнения команды `kubectl cluster-info`. Например, https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy. 
3. Выберите сертификат клиента.
4. Введите токен. 
5. Повторно подключитесь к командной строке Bash на главном узле и предоставьте разрешения к `kubernetes-dashboard`. Выполните следующую команду:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Сценарий предоставляет `kubernetes-dashboard` права администратора облака. Дополнительные сведения см. в статье [Подключение веб-панели мониторинга Kubernetes в Службе Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Теперь вы можете использовать панель мониторинга. Дополнительные сведения о панели мониторинга Kubernetes см. [в этой статье](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

![Панель мониторинга Kubernetes в Azure Stack Hub](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Дальнейшие действия 

[Развертывание Kubernetes в Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)  

[Добавление кластера Kubernetes в Marketplace (для оператора Azure Stack Hub)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes в Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
