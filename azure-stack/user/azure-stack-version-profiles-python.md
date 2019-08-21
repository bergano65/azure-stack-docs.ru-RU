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
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: 35ce331c29e89af3a81396a9658cf8a0f29018d3
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959423"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Использование профилей версий API и Python в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="python-and-api-version-profiles"></a>Использование профилей версии API с помощью Python

Пакет SDK для Python поддерживает профили версии API для разных облачных платформ, например Azure Stack и глобальной среды Azure. Вы можете использовать профили API при создании решений для гибридного облака. Пакет SDK для Python поддерживает такие профили API:

- **Актуальная**  
    Профиль предназначен для последних версий API всех поставщиков служб на платформе Azure.
- **2019-03-01-hybrid**  
    Профиль предназначен для последних версий API всех поставщиков ресурсов на платформе Azure Stack с меткой версии 1904 или более поздней.
- **2018-03-01-hybrid**  
    Профиль предназначен для наиболее совместимых версий API всех поставщиков ресурсов на платформе Azure Stack.
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

Чтобы использовать пакет SDK Azure для Python и Azure Stack, укажите следующие значения и задайте их для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для используемой операционной системы.

| Значение | Переменные среды | ОПИСАНИЕ |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Tenant ID | `AZURE_TENANT_ID` | Значение [идентификатора клиента](../operator/azure-stack-identity-overview.md) Azure Stack. |
| Идентификатор клиента | `AZURE_CLIENT_ID` | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. |
| Идентификатор подписки | `AZURE_SUBSCRIPTION_ID` | [Идентификатор подписки](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) для доступа к предложениям в Azure Stack. |
| Секрет клиента | `AZURE_CLIENT_SECRET` | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы. |
| Конечная точка Resource Manager | `ARM_ENDPOINT` | Дополнительные сведения см. в разделе о [конечной точке Azure Stack Resource Manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Расположение ресурса | `AZURE_RESOURCE_LOCATION` | Расположение ресурса в среде Azure Stack.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

Если вы используете ASDK, нужно обеспечить доверие корневому сертификату ЦС на удаленном компьютере. Если вы используете интегрированные системы, то это необязательно.

#### <a name="windows"></a>Windows

1. Найдите расположение хранилища сертификатов Python на своем компьютере. Это расположение зависит от того, куда вы установили Python. Откройте командную строку или строку PowerShell с повышенными правами и введите следующую команду:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение хранилища сертификатов. Например: *~/lib/python3.5/site-packages/certifi/cacert.pem*. Конкретный путь зависит от операционной системы и установленной версии Python.

2. Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

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
> Если вы используете virtualenv для разработки с помощью пакета SDK для Python, как указано ниже, упомянутый выше сертификат необходимо добавить в хранилище сертификатов виртуальной среды. Путь может выглядеть так: ...\mytestenv\Lib\site-packages\certifi\cacert.pem.



## <a name="python-samples-for-azure-stack"></a>Примеры Python для Azure Stack

Ниже указанны некоторые примеры кода, доступные для Azure Stack с использованием пакета SDK для Python.

- [Hybrid-ResourceManager-Python-Manage-Resources](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Hybrid-Storage-Python-Manage-Storage-Account](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Hybrid-Compute-Python-Manage-VM](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/) (Пример, использующий профиль 2019-03-01-hybrid, который предназначен для последних версий API, поддерживаемых Azure Stack.)

## <a name="python-manage-virtual-machine-sample"></a>Пример управления виртуальной машиной на Python

Используйте указанный ниже пример кода для выполнения типичных задач управления для виртуальных машин в Azure Stack. Из примера кода вы изучите:

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

2. Общей рекомендацией для разработки на Python является использование виртуальной среды. Дополнительные сведения см. в [документации по Python](https://docs.python.org/3/tutorial/venv.html).

3. Установите и инициализируйте виртуальную среду с помощью модуля "venv" в Python 3 (нужно установить [virtualenv](https://pypi.python.org/pypi/virtualenv) для Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Клонируйте репозиторий:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Установите зависимости с помощью PIP:

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

Если у вас нет подписки Microsoft Azure, [здесь](https://go.microsoft.com/fwlink/?LinkId=330212) можно создать учетную запись и получить бесплатную пробную версию.
