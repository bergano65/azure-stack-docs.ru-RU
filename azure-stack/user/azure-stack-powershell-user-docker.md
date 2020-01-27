---
title: Использование Docker для запуска PowerShell в Azure Stack Hub | Документация Майкрософт
description: Использование Docker для запуска PowerShell в Azure Stack Hub
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 410a166d3d13107511a46cfad77c598f9118d026
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536000"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>Использование Docker для запуска PowerShell в Azure Stack Hub

В этой статье описано, как с помощью Docker создавать контейнеры на основе Windows и запускать для них PowerShell такой версии, которая требуется для работы разных интерфейсов. В Docker нужно использовать контейнеры на основе Windows.

## <a name="docker-prerequisites"></a>Предварительные требования Docker

1. Установите [Docker](https://docs.docker.com/install/).

1. В командной строке, например Powershell или Bash, введите следующую команду:

    ```bash
        Docker --version
    ```

1. Вам нужно запустить Docker с помощью контейнеров Windows, для которых требуется Windows 10. Запустив Docker, переключитесь на контейнеры Windows.

1. Запустите Docker с компьютера, присоединенного к тому же домену, что и Azure Stack Hub. Если вы используете Пакет средств разработки Azure Stack, установите [VPN на удаленном компьютере](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Создание субъекта-службы для использования PowerShell

Чтобы с помощью PowerShell получить доступ к ресурсам в Azure Stack Hub, в клиенте Azure Active Directory (Azure AD) нужно создать субъекта-службу. Вы можете делегировать разрешения с помощью управления доступом на основе ролей пользователей.

1. См. подробнее о [предоставлении приложениям доступа к ресурсам Azure Stack Hub путем создания субъектов-служб](azure-stack-create-service-principals.md).

2. Запишите идентификатор приложения, секрет и идентификатор клиента для дальнейшего использования.

## <a name="docker---azure-stack-hub-api-profiles-module"></a>Docker — модуль профилей API Azure Stack Hub

Dockerfile открывает образ Microsoft *microsoft/windowsservercore*, в котором установлен Windows PowerShell 5.1. Файл затем загружает NuGet, модули PowerShell для Azure Stack Hub и инструменты из средства Azure Stack Hub Tools.

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

6. Подключитесь к экземпляру Azure Stack Hub с помощью субъекта-службы. Теперь вы используете командную строку PowerShell в Docker. 

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

7. Проверьте подключение, создав группу ресурсов в Azure Stack Hub.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Дальнейшие действия

-  Ознакомьтесь с обзором в статье [Начало работы с PowerShell в Azure Stack](azure-stack-powershell-overview.md).
- См. подробнее об [использовании профилей API для PowerShell](azure-stack-version-profiles.md) в Azure Stack Hub.
- [Установка PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md).
- Чтобы обеспечить согласованность данных в облаке, см. статью [Рекомендации по использованию шаблона Azure Resource Manager](azure-stack-develop-templates.md).
