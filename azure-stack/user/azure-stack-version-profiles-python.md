---
title: Использование профилей версий API и Python в Azure Stack | Документы Майкрософт
description: Узнайте, как использовать профили версий API с помощью Python в Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: bf44716c160948f3deafdc8afb87b9b6d49f9eb5
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814448"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Использование профилей версий API и Python в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пакет SDK для Python поддерживает профили версии API для разных облачных платформ, например Azure Stack и глобальной среды Azure. Вы можете использовать профили API при создании решений для гибридного облака.

Для выполнения инструкций в этой статье нужна подписка Microsoft Azure. Если у вас ее нет, получите [бесплатную пробную версию](https://go.microsoft.com/fwlink/?LinkId=330212).

## <a name="python-and-api-version-profiles"></a>Использование профилей версии API с помощью Python

Пакет SDK для Python поддерживает такие профили API:

- **Актуальная**  
    Этот профиль предназначен для последних версий API всех поставщиков служб на платформе Azure.
- **2019-03-01-hybrid**  
    Этот профиль предназначен для последних версий API всех поставщиков ресурсов на платформе Azure Stack с версией 1904 или более поздней.
- **2018-03-01-hybrid**  
    Этот профиль предназначен для почти любых совместимых версий API всех поставщиков ресурсов на платформе Azure Stack.
- **2017-03-09-profile**  
    Профиль предназначен для наиболее совместимых версий API поставщиков ресурсов, поддерживаемых Azure Stack.

   Дополнительные сведения о профилях API и Azure Stack см. в статье [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Установка пакета Azure SDK для Python

1. Установите Git с [официального сайта](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Инструкции по установке пакета SDK для Python см. в разделе [Установка пакета SDK](/python/azure/python-sdk-azure-install?view=azure-python).
3. При необходимости создайте подписку и сохраните ее идентификатор для дальнейшего использования. Сведения о создании подписки см. в статье [Создание подписок для предложений в Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Создайте субъект-службу и сохраните его идентификатор и секрет. Инструкции по созданию субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Убедитесь, что субъект-служба имеет роль участника или владельца в вашей подписке. Сведения о том, как назначить роль субъекту-службе, см. в статье [Использование удостоверения приложения для доступа к ресурсам](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для Python и Azure Stack, укажите следующие значения и задайте их для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями для используемой операционной системы, которые приводятся после следующей таблицы.

| Значение | Переменные среды | ОПИСАНИЕ |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID | `AZURE_TENANT_ID` | [Идентификатор клиента](../operator/azure-stack-identity-overview.md) Azure Stack. |
| Идентификатор клиента | `AZURE_CLIENT_ID` | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. |
| Идентификатор подписки | `AZURE_SUBSCRIPTION_ID` | [Идентификатор подписки](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) используется для доступа к предложениям в Azure Stack. |
| Секрет клиента | `AZURE_CLIENT_SECRET` | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы. |
| Конечная точка Resource Manager | `ARM_ENDPOINT` | Дополнительные сведения см. в статье [Конечная точка Resource Manager для Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Расположение ресурса | `AZURE_RESOURCE_LOCATION` | Расположение ресурса в конкретной среде Azure Stack.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно явным образом настроить доверие корневому сертификату ЦС на удаленном компьютере. Для интегрированных систем Azure Stack доверие корневому сертификату ЦС не требуется.

#### <a name="windows"></a>Windows

1. Найдите расположение хранилища сертификатов Python на своем компьютере. Это расположение зависит от того, куда вы установили Python. Откройте командную строку или строку PowerShell с повышенными правами и введите следующую команду:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Запишите расположение хранилища сертификатов, например: **~/lib/python3.5/site-packages/certifi/cacert.pem**. Конкретный путь зависит от операционной системы и установленной версии Python.

2. Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

> [!NOTE]  
> Если вы используете **virtualenv** для разработки с помощью пакета SDK для Python, как указано ниже в разделе [Выполнение примера Python](#run-the-python-sample), упомянутый выше сертификат необходимо также добавить в хранилище сертификатов виртуальной среды. Соответствующий путь будут выглядеть следующим образом: `..\mytestenv\Lib\site-packages\certifi\cacert.pem`.

## <a name="python-samples-for-azure-stack"></a>Примеры Python для Azure Stack

Ниже указаны некоторые примеры кода, доступные для Azure Stack с использованием пакета SDK для Python.

- [Hybrid-ResourceManager-Python-Manage-Resources](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Hybrid-Storage-Python-Manage-Storage-Account](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Управление виртуальными машинами](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/). В этом примере используется профиль **2019-03-01-hybrid**, который нацелен на последние версии API, поддерживаемые Azure Stack.

## <a name="manage-virtual-machine-sample"></a>Пример управления виртуальной машиной

Следующий пример кода Python позволяет выполнять типичные задачи управления для виртуальных машин в Azure Stack. Этот пример кода демонстрирует следующие операции.

- Создание виртуальных машин:
  - Создание виртуальной машины Linux
  - Создание виртуальной машины Windows
- Обновление виртуальной машины:
  - Расширение диска
  - Добавление тега для виртуальной машины
  - Присоединение дисков данных
  - Отключение дисков данных
- Управление виртуальной машиной:
  - Запуск виртуальной машины
  - Остановка виртуальной машины
  - Перезапуск виртуальной машины
- Вывод списка виртуальных машин
- Удаление виртуальной машины

Чтобы просмотреть код для выполнения этих операций, изучите функцию **run_example()** в сценарии Python **example.py** из репозитория GitHub [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Каждая операция однозначно помечается комментарием и функцией печати. Примеры необязательно следуют в порядке, показанном в приведенном выше списке.

## <a name="run-the-python-sample"></a>Запуск примера Python

1. [Установите Python](https://www.python.org/downloads/), если вы еще это не сделали. Этот пример (и пакет SDK) совместим с Python версий 2.7, 3.4, 3.5 и 3.6.

2. Общей рекомендацией для разработки на Python является использование виртуальной среды. Дополнительные сведения см. в [документации Python](https://docs.python.org/3/tutorial/venv.html).

3. Установите и инициализируйте виртуальную среду с помощью модуля **venv** из Python 3 (для Python 2.7 нужно установить [virtualenv](https://pypi.python.org/pypi/virtualenv)):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Клонируйте репозиторий.

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Установите зависимости с помощью **PIP**:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Создайте [субъект-службу](../operator/azure-stack-create-service-principals.md) для работы с Azure Stack. Убедитесь, что субъект-служба имеет [роль участника или владельца](../operator/azure-stack-create-service-principals.md#assign-a-role) в вашей подписке.

7. Задайте указанные ниже переменные среды и экспортируйте их в вашу текущую оболочку.

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Чтобы запустить этот пример, в Azure Stack Marketplace должны присутствовать образы Ubuntu 16.04-LTS и WindowsServer 2012-R2-Datacenter. Эти образы можно [скачать из Azure](../operator/azure-stack-download-azure-marketplace-item.md) или добавить в [репозиторий образов платформы](../operator/azure-stack-add-vm-image.md).

9. Запустите пример:

    ```python
    python example.py
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Центр разработки на Python Azure](https://azure.microsoft.com/develop/python/)
- [Документация по виртуальным машинам Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Схема обучения для виртуальных машин](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
