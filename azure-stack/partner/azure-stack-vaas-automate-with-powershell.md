---
title: Автоматизация проверки Azure Stack с помощью PowerShell | Документация Майкрософт
description: Проверку Azure Stack можно автоматизировать с помощью PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7c6da84700c73e0976214bd999dfb3ca4ba0ee47
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167343"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Автоматическая проверка Azure Stack с помощью PowerShell

Проверка как услуга (VaaS) предоставляет возможность автоматизировать запуск тестов с помощью скрипта **LaunchVaaSTests.ps1**.

> [!NOTE]  
> Можно автоматизировать только рабочий процесс тестового прохода. Рабочие процессы проверки пакетов и решений можно автоматизировать только через портал VaaS.

Этот скрипт может использоваться для того, чтобы:

> [!div class="checklist"]
> * установить необходимые компоненты;
> * установить и запустить локального агента;
> * запустить категорию тестов, таких как *интеграция*, *функциональность*, *надежность*;
> * сообщить результаты теста.

## <a name="launch-the-test-pass-workflow"></a>Запуск процесса прохождения теста

1. Откройте командную строку PowerShell с повышенными привилегиями.

2. Запустите следующий скрипт, чтобы скачать скрипт автоматизации.

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Выполните следующий скрипт, используя соответствующие значения параметров:

    ```powershell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Параметры**

    | Параметр | ОПИСАНИЕ |
    | --- | --- |
    | VaaSUserId | Идентификатор пользователя VaaS. |
    | VaaSUserPassword | Пароль VaaS. |
    | VaaSAccountTenantId | GUID клиента VaaS. |
    | VaaSSolutionName | Имя решения VaaS, в котором будет выполняться прохождение теста. |
    | VaaSTestPassName | Имя рабочего процесса прохождения теста VaaS, который необходимо создать. |
    | VaaSTestCategories | `Integration`, `Functional` или `Reliability`. Если вы используете несколько значений, разделите их запятой.  |
    | ServiceAdminUserName | Учетная запись администратора службы Azure Stack.  |
    | ServiceAdminPassword | Пароль службы Azure Stack.  |
    | TenantAdminUserName | Администратор основного клиента.  |
    | TenantAdminPassword | Пароль основного клиента.  |
    | CloudAdminUserName | Имя пользователя администратора облака.  |
    | CloudAdminPassword | Пароль администратора облака.  |

4. Просмотрите результаты теста. Другие параметры см. в статье [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать больше о PowerShell в Azure Stack, ознакомьтесь с последними версиями модулей.

> [!div class="nextstepaction"]
> [Модуль Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
