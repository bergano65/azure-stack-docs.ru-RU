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
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985545"
---
# <a name="use-docker-to-run-powershell"></a>Использование Docker для запуска PowerShell

Docker можно использовать для создания контейнеров на основе Windows, для которых будут запущены определенные версии PowerShell, необходимые для работы разных интерфейсов. В Docker необходимо использовать контейнеры на основе Windows.

## <a name="docker-prerequisites"></a>Предварительные требования Docker

1. Установите [Docker](https://docs.docker.com/install/).
2. Откройте командную строку, например Powershell или Bash, и введите следующую команду.

    ```bash
        Docker -version
    ```

3. Необходимо запустить Docker с помощью контейнеров Windows, для которых требуется Windows 10. При выполнении Docker переключитесь на контейнеры Windows.

4. Docker необходимо запустить с компьютера, присоединенного к тому же домену, что и Azure Stack. Если вы используете ASDK, необходимо установить [VPN на удаленном компьютере](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="service-principals-for-using-powershell"></a>Субъекты-службы для использования PowerShell

Требуется субъект-служба в клиенте Azure AD, чтобы использовать PowerShell для доступа к ресурсу в Azure Stack. Вы можете делегировать разрешения с помощью управления доступом на основе ролей пользователей.

1. Настройте субъект, следуя инструкциям в статье [Give applications access to Azure Stack resources by creating service principals](azure-stack-create-service-principals.md) (Предоставление приложениям доступа к ресурсам Azure Stack путем создания субъектов-служб).
2. Запишите идентификатор приложения, секрет и идентификатор клиента.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker — модуль профиля API Azure Stack

Dockerfile открывает образ Microsoft microsoft/windowsservercore, в котором установлен Windows PowerShell 5.1. Файл затем загружает NuGet, модули PowerShell для Azure Stack и инструменты из средства Azure Stack Tools.

1. Скачайте этот репозиторий в формате ZIP или клонируйте репозиторий.  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Откройте папку репозитория из терминала.

3. Откройте интерфейс командной строки в репозитории и введите следующую команду.

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. После создания изображения можно запустить интерактивный контейнер. Тип:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Оболочка готова для командлетов.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Подключитесь к Azure Stack с помощью субъекта-службы. Теперь вы используете командную строку PowerShell в Docker. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell возвращает объект учетной записи.

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Проверьте подключения, создав группу ресурсов в Azure Stack.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Дополнительная информация

-  Ознакомьтесь с обзором в статье [Get started with PowerShell on Azure Stack](azure-stack-powershell-overview.md) (Начало работы с PowerShell в Azure Stack).
- См. статью [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md).
- [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Установка Powershell для Azure Stack).
- Чтобы обеспечить согласованность данных в облаке, см. статью [Рекомендации по использованию шаблона Azure Resource Manager](azure-stack-develop-templates.md).