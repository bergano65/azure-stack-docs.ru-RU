---
title: Использование Docker для запуска PowerShell в Azure Stack | Документация Майкрософт
description: Использование Docker для запуска PowerShell в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 42ed9766b43ce3c0c1c455d8ea286a5b453df325
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394379"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>Использование Docker для запуска PowerShell в Azure Stack

В этой статье описано, как с помощью Docker создавать контейнеры на основе Windows и запускать для них PowerShell такой версии, которая требуется для работы разных интерфейсов. В Docker нужно использовать контейнеры на основе Windows.

## <a name="docker-prerequisites"></a>Предварительные требования Docker

1. Установите [Docker](https://docs.docker.com/install/).

1. В командной строке, например Powershell или Bash, введите следующую команду:

    ```bash
        Docker -version
    ```

1. Вам нужно запустить Docker с помощью контейнеров Windows, для которых требуется Windows 10. Запустив Docker, переключитесь на контейнеры Windows.

1. Запустите Docker с компьютера, присоединенного к тому же домену, что и Azure Stack. Если вы используете Пакет средств разработки Azure Stack, установите [VPN на удаленном компьютере](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Создание субъекта-службы для использования PowerShell

Чтобы с помощью PowerShell получить доступ к ресурсам в Azure Stack, в клиенте Azure Active Directory (Azure AD) нужно создать субъекта-службу. Вы можете делегировать разрешения с помощью управления доступом на основе ролей пользователей.

1. См. подробнее о [предоставлении приложениям доступа к ресурсам Azure Stack путем создания субъектов-служб](azure-stack-create-service-principals.md).

2. Запишите идентификатор приложения, секрет и идентификатор клиента для дальнейшего использования.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker — модуль профилей API Azure Stack

Dockerfile открывает образ Microsoft *microsoft/windowsservercore*, в котором установлен Windows PowerShell 5.1. Файл затем загружает NuGet, модули PowerShell для Azure Stack и инструменты из средства Azure Stack Tools.

1. Скачайте репозиторий [azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell) в формате ZIP или клонируйте его.

2. Откройте папку репозитория из терминала.

3. Откройте интерфейс командной строки в репозитории и введите следующую команду:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Создав образ, запустите интерактивный контейнер, введя следующую команду:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Оболочка готова для командлетов.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Подключитесь к экземпляру Azure Stack с помощью субъекта-службы. Теперь вы используете командную строку PowerShell в Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell возвращает объект учетной записи.

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Проверьте подключения, создав группу ресурсов в Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Дополнительная информация

-  См. подробнее об [использовании Azure Stack PowerShell в Azure Stack](azure-stack-powershell-overview.md).
- См. подробнее об [использовании профилей API для PowerShell в Azure Stack](azure-stack-version-profiles.md).
- Установите [Powershell для Azure Stack](../operator/azure-stack-powershell-install.md).
- Чтобы обеспечить согласованность данных в облаке, см. статью [Рекомендации по использованию шаблона Azure Resource Manager](azure-stack-develop-templates.md).
