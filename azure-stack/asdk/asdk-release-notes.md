---
title: Заметки о выпуске ASDK | Документация Майкрософт
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 3f11a7b5066d0b50d85a40be1df47dfe1a5ade38
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025846"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какую версию используете, [проверьте ее с помощью портала](../operator/azure-stack-updates.md).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11907020"></a>Сборка 1.1907.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов marketplace развертывание могло не завершиться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](../operator/azure-stack-release-notes-1907.md#fixes) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1907.md).
- Обратите внимание на то, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1907.md#hotfixes) невозможно применить к ASDK для Azure Stack.

## <a name="build-11906030"></a>Сборка 1.1906.0.30

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="changes"></a>изменения

- Добавлена поддержка виртуальной машины для вызова **AzS-SRNG01**, в которой размещена служба сбора журналов для Azure Stack. Дополнительные сведения см. в статье [Архитектура ASDK](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов marketplace развертывание могло не завершиться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#fixes) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1906.md).
- Обратите внимание на то, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) невозможно применить к ASDK для Azure Stack.

## <a name="build-11905040"></a>Сборка 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Исправлена проблема, при которой вам приходилось редактировать скрипт PowerShell **RegisterWithAzure.psm1** для успешной [регистрации ASDK](asdk-register.md).
- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1905.md).
- Обратите внимание на то, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) невозможно применить к ASDK для Azure Stack.

## <a name="build-11904036"></a>Сборка 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Так как может произойти превышение времени ожидания у субъекта-службы при выполнении сценария регистрации, чтобы успешно [зарегистрировать ASDK](asdk-register.md), необходимо изменить сценарий PowerShell **RegisterWithAzure.psm1**. Выполните следующее:

  1. На главном компьютере ASDK откройте в редакторе файл **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** с повышенным уровнем разрешений.
  2. В конце строки 1249 добавьте параметр `-TimeoutInSeconds 1800`. Это необходимо, чтобы избежать превышения времени ожидания субъектом-службой при запуске сценария регистрации. Теперь строка 1249 должна выглядеть следующим образом:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Исправлена проблема VPN-подключения, обнаруженная в выпуске 1902.

- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1904.md).
- Обратите внимание на то, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) невозможно применить к ASDK для Azure Stack.

