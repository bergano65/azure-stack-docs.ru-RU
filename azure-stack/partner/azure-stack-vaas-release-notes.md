---
title: Заметки о выпуске проверки как службы Azure Stack | Документация Майкрософт
description: Заметки о выпуске проверки Azure Stack как службы.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: eefd39c751bdbd9ed9c8f3b9112fee1ddbffb9a0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310100"
---
# <a name="release-notes-for-validation-as-a-service"></a>Заметки о выпуске для проверки как службы

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Эта статья содержит заметки о выпуске для проверки как службы Azure Stack.

## <a name="version-405"></a>Версия 4.0.5

17 января 2019 г.

- Тест на идентификацию диска обновлен для устранения несоответствий в пуле носителей. Версия: 5.1.14.0 -> 5.1.15.0
- Проверка ежемесячного обновления Azure Stack обновлена для устранения несоответствий в проверке утвержденного программного обеспечения и содержимого. Версия: 5.1.14.0 -> 5.1.17.0
- Тест OEM Extension Package Verification (Проверка пакетов для расширения OEM) обновлен для выполнения необходимых процедур проверки перед шагом обновления Azure Stack. Версия: 5.1.14.0 -> 5.1.16.0
- Внутренние исправление ошибок

## <a name="version-402"></a>Версия 4.0.2

7 января 2019 г.

Если вы запускаете рабочий процесс проверки ежемесячного обновления Azure Stack, а версия вашего пакета обновлений изготовителя оборудования не 1810 или выше, вы получите сообщение об ошибке, как только перейдете к шагу обновления изготовителя оборудования. Это ошибка. Исправление разрабатывается. Далее приведены шаги по устранению рисков.

1. Запустите обновление изготовителя оборудования в обычном режиме.
2. Выполните Test-AzureStack после успешного применения пакета и сохраните выходные данные.
3. Отмените тест.
4. Отправьте сохраненные выходные данные в VaaSHelp@microsoft.com, чтобы получить результаты передачи для запуска.

## <a name="version-402"></a>Версия 4.0.2

30 ноября 2018 г.

- Внутренние исправление ошибок

## <a name="version-401"></a>Версия 4.0.1

8 октября 2018 г.

- Предварительные требования для VaaS

    `Install-VaaSPrerequisites` больше не требуются учетные данные администратора облака. Если вы используете последнюю версию этого командлета, см. раздел [Скачивание и установка агента](azure-stack-vaas-local-agent.md#download-and-install-the-agent) с исправленными командами для установки необходимых компонентов. Используйте следующие команды:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Версия 4.0.0

29 августа 2018 г.

- Изменения для предварительных требований VaaS и виртуального жесткого диска

    Теперь `Install-VaaSPrerequisites` требуются учетные данные администратора облака для устранения проблемы при проверке пакета. В документацию из раздела [Скачивание и установка агента](azure-stack-vaas-local-agent.md#download-and-install-the-agent) были внесены следующие изменения:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Необходимые сценарию `$CloudAdminCreds` предназначены для проверяемого экземпляра Azure Stack. Они отличаются от учетных данных Azure Active Directory, используемых клиентом VaaS.

- Обновление локального агента

    Предыдущая версия локального агента несовместима с текущей версией службы 4.0.0. Все пользователи должны обновить свои локальные агенты. Инструкции по установке последней версии агента см. в разделе [Скачивание и установка агента](azure-stack-vaas-local-agent.md#download-and-install-the-agent).

- Обновление автоматизации PowerShell

    Были внесены изменения сценарии PowerShell `LaunchVaaSTests`, которым требуется последняя версия пакетов создания сценариев. Инструкции по установке последней версии пакета создания сценариев см. в разделе [Запуск процесса прохождения теста](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow).

- Портал проверки как услуги

  - Уведомления о подписании пакета

    Когда пакет настройки OEM отправляется в рамках рабочего процесса проверки пакета, формат пакета будет проверен, чтобы подтвердить его соответствие опубликованной спецификации. Если пакет не соответствует, происходит сбой выполнения. Уведомления по электронной почте направляются на адрес электронной почты зарегистрированного контактного лица Azure Active Directory для клиента.

  - Категория интерактивных тестов

    Добавлена категория **интерактивных** тестов. Эти тесты позволяют выполнять неавтоматические интерактивные сценарии Azure Stack.

  - Интерактивная проверка функций

    Теперь в рабочем процессе тестового прохода доступна возможность отправки целевых отзывов по определенным функциям. Тест `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` проверяет, правильно ли применены определенные обновления, и собирает отзывы.

## <a name="next-steps"></a>Дополнительная информация

- [Проверка как услуга: устранение неполадок](azure-stack-vaas-troubleshoot.md)
