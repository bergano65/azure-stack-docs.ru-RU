---
title: Устранение неполадок с обработчиком AKS в Azure Stack Hub
description: В этой статье описано, как устранять неполадки с обработчиком AKS в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: aa41ddde0986716e49073d571e967a050ef660f6
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79313028"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack-hub"></a>Устранение неполадок с обработчиком AKS в Azure Stack Hub

При развертывании обработчика AKS в Azure Stack Hub или при работе с ним могут возникать проблемы. В этой статье описано, как устранять неполадки при развертывании обработчика AKS, собирать сведения об обработчике AKS, собирать журналы Kubernetes, изучать коды ошибок для расширения пользовательских скриптов, а также регистрировать проблемы с обработчиком AKS на сайте GitHub.

## <a name="troubleshoot-the-aks-engine-install"></a>Устранение неполадок с установкой обработчика AKS

### <a name="try-gofish"></a>Попробуйте GoFish

Если при установке обработчика AKS произошел сбой, попробуйте установить его с помощью диспетчера пакетов GoFish. [GoFish](https://gofi.sh) идентифицирует себя как Homebrew с кроссплатформенной поддержкой.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Установка обработчика AKS с помощью GoFish в Linux

Установите GoFish через [страницу установки](https://gofi.sh/#install).

1. В командной строке bash выполните следующую команду:

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Выполните следующую команду, чтобы установить обработчик AKS с помощью GoFish:

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Установка обработчика AKS с помощью GoFish в Windows

Установите GoFish через [страницу установки](https://gofi.sh/#install).

1. Выполните следующую команду в сеансе PowerShell с повышенными правами:

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Выполните в том же сеансе следующую команду, чтобы установить обработчик AKS с помощью GoFish:

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Список проверки для типичных проблем развертывания

Если вы столкнетесь с ошибками при развертывании кластера Kubernetes с помощью обработчика AKS, проверьте следующее:

1.  Правильно ли указаны учетные данные субъекта-службы?
2.  Имеет ли субъект-служба роль "Участник" для подписки Azure Stack Hub?
3. Есть ли в плане Azure Stack Hub достаточно большой объем квоты?
4.  Не выполняется ли в текущий момент обновление этого экземпляра Azure Stack Hub?

Дополнительные сведения см. в статье [об устранении проблем](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md) в репозитории **Azure/aks-engine** на сайте GitHub.

## <a name="collect-aks-engine-logs"></a>Сбор журналов обработчика AKS

Вы можете использовать проверочные сведения, которые создаются обработчиком AKS. Обработчик AKS сообщает сведения о состоянии и ошибках при запусках приложения. Вы можете направить эти выходные данные в текстовый файл или скопировать прямо из консоли командной строки. См. список кодов ошибок, активируемых обработчиком AKS, в списке [кодов ошибок для расширения пользовательских скриптов](#review-custom-script-extension-error-codes).

1.  Соберите стандартные выходные данные и сведения об ошибках, которые отображаются в программе командной строки обработчика AKS.

2. Получите журналы из локального файла. Выходной каталог можно задать через параметр **--output-directory**.

    Чтобы задать локальный путь для журналов, выполните следующую команду:

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Сбор журналов Kubernetes

Кроме журналов обработчика AKS, вы также можете просматривать сведения о состоянии и ошибках, генерируемые компонентами Kubernetes. Эти журналы можно получить с помощью скрипта bash с именем [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/diagnosis-v0.1.3).

Этот скрипт автоматически собирает сведения из следующих журналов: 

 - журналы агента Linux в Microsoft Azure (waagent);
 - журналы расширения пользовательских скриптов;
 - метаданные выполняемого контейнера kube-system;
 - журналы выполняемого контейнера kube-system;
 - состояние и журнал службы Kubelet;
 - состояние и журнал службы Etcd;
 - журналы DVM для элемента коллекции;
 - моментальный снимок kube-system.

Без этого скрипта вам придется вручную подключаться к каждому узлу в кластере, находить и скачивать все эти журналы. Кроме того, скрипт может передавать собранные журналы в учетную запись хранения, что позволяет предоставить журналы в совместный доступ другим пользователям.

Требования

 - Виртуальная машина Linux, Git bash или Bash в Windows.
 - Установленный [Azure CLI](azure-stack-version-profiles-azurecli2.md) на компьютере, с которого будет выполняться скрипт.
 - Удостоверение субъекта-службы, который создает сеанс подключения Azure CLI к Azure Stack Hub. Поскольку скрипт для своей работы обнаруживает и создает ресурсы ARM, ему потребуется Azure CLI и удостоверение субъекта-службы.
 - Учетная запись пользователя (подписка), в среде которой уже выбран кластер Kubernetes. 
1. Скачайте последний выпуск скрипта в формате TAR-файла на клиентскую виртуальную машину, любой компьютер с доступом к кластеру Kubernetes или тот самый компьютер, на котором вы развернули кластер с помощью обработчика AKS.

    Выполните следующие команды:

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. Найдите обязательные параметры для работы скрипта `getkuberneteslogs.sh`. Этот скрипт использует следующие параметры:

    | Параметр | Описание | Обязательно | Пример |
    | --- | --- | --- | --- |
    | -h, --help | Отображение сведений об использовании команд. | нет | 
    -u,--user | Имя пользователя администратора для виртуальной машины кластера. | да | azureuser<br>(значение по умолчанию) |
    | -i, --identity-file | Закрытый ключ RSA, привязанный к открытому ключу, который использовался для создания кластера Kubernetes (иногда называется "id_rsa")  | да | `./rsa.pem` (Putty)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g, --resource-group    | Группа ресурсов кластера Kubernetes | да | k8sresourcegroup |
    |   -n, --user-namespace               | Сбор журналов из контейнеров в указанных пространствах имен (журналы kube-system собираются всегда) | нет |   monitoring |
    |       --api-model                    | Сохранение файла apimodel.json в учетной записи хранения Azure Stack Hub. Отправка файла apimodel.json в учетную запись хранения выполняется, если указан параметр --upload-logs. | нет | `./apimodel.json` |
    | --all-namespaces               | Сбор журналов из контейнеров во всех пространствах имен. Этот параметр имеет более высокий приоритет, чем --user-namespace. | нет | |
    | --upload-logs                  | Сохранение полученных журналов в учетной записи хранения Azure Stack Hub. Журналы можно найти в группе ресурсов KubernetesLogs. | нет | |
    --disable-host-key-checking    | Задает для параметра SSH StrictHostKeyChecking значение "no" (нет) на время выполнения скрипта. Используйте только в безопасной среде. | нет | |

3. Выполните любой из приведенных ниже примеров команд для своего набора данных:

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Проверка кодов ошибок для расширения пользовательских скриптов

Вы можете проверить список кодов ошибок, которые создаются расширением пользовательских скриптов при работе кластера. Ошибки расширения пользовательских скриптов могут быть полезны при диагностике основной причины проблемы. Расширение пользовательских скриптов для сервера Ubuntu, который используется в кластере Kubernetes, поддерживает множество операций обработчика AKS. Дополнительные сведения о кодах выхода расширения пользовательских скриптов см. в файле [cse_helpers.sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>Предоставление журналов Kubernetes сотруднику службы поддержки Майкрософт

Если после сбора и изучения журналов вы по-прежнему не можете решить проблему, вы можете начать процесс создания запроса в службу поддержки и указать журналы, собранные с помощью команды `getkuberneteslogs.sh` с набором параметров `--upload-logs`. 

Свяжитесь с вашим оператором Azure Stack Hub. Оператор использует информацию из журналов для создания обращения в службу поддержки.

В процессе устранения любых проблем с поддержкой специалист службы поддержки Майкрософт может запросить, чтобы оператор Azure Stack Hub собрал системные журналы Azure Stack Hub. Вам может потребоваться предоставить оператору сведения об учетной записи хранения, куда вы передавали журналы Kubernetes, запустив `getkuberneteslogs.sh`.

Ваш оператор может запустить командлет PowerShell **Get-AzureStackLog**. Эта команда использует параметр (`-InputSaSUri`), указывающий учетную запись хранения, в которой хранятся журналы Kubernetes.

Оператор может сочетать созданные вами журналы с другими системными журналами, которые могут потребоваться службе поддержки Майкрософт, и предоставлять их корпорации Майкрософт.

## <a name="open-github-issues"></a>Сообщение о проблеме на сайте GitHub

Если вам не удается устранить ошибку развертывания, попробуйте сообщить о проблеме на сайте GitHub. 

1. Откройте [проблему GitHub](https://github.com/Azure/aks-engine/issues/new) в репозитории обработчика AKS.
2. Введите заголовок в следующем формате: C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. Добавьте следующую информацию о проблеме:

    - Файл конфигурации кластера `apimodel json`, из которого выполнялось развертывание. Перед публикацией в GitHub удалите все секреты и ключи.  
     - Выходные данные следующей команды **kubectl**`get nodes`.  
     - Содержимое файлов `/var/log/azure/cluster-provision.log` и `/var/log/cloud-init-output.log`.

## <a name="next-steps"></a>Дальнейшие действия

- См. сведения об [обработчике AKS в Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md).
