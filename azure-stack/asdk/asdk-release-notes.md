---
title: Заметки к выпуску Пакета средств разработки Azure Stack | Документация Майкрософт
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack.
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
ms.openlocfilehash: a2b000f60c5867e557ef5b0621f994ea7ec23913
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493782"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](../operator/azure-stack-updates.md#determine-the-current-version).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11907020"></a>Сборка 1.1907.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов Marketplace развертывание могло не завершаться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](../operator/azure-stack-release-notes-1907.md#fixes) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1907.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1907.md#hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-11906030"></a>Сборка 1.1906.0.30

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="changes"></a>изменения

- Добавлена поддержка виртуальной машины для вызова **AzS-SRNG01**, в которой размещена служба сбора журналов для Azure Stack. Подробные сведения см. в статье [Архитектура Пакета средств разработки Azure Stack](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов Marketplace развертывание могло не завершаться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#fixes) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1906.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-11905040"></a>Сборка 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Исправлена проблема, при которой вам приходилось редактировать скрипт PowerShell **RegisterWithAzure.psm1** для успешной [регистрации ASDK](asdk-register.md).
- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1905.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-11904036"></a>Сборка 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Так как может произойти превышение времени ожидания у субъекта-службы при выполнении скрипта регистрации, чтобы успешно [зарегистрировать ASDK](asdk-register.md), необходимо изменить скрипт PowerShell **RegisterWithAzure.psm1**. Выполните следующее:

  1. На главном компьютере ASDK откройте в редакторе файл **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** с повышенным уровнем разрешений.
  2. В конце строки 1249 добавьте параметр `-TimeoutInSeconds 1800`. Это необходимо, чтобы избежать превышение времени ожидания у субъекта-службы при запуске скрипта регистрации. Теперь строка 1249 должна выглядеть следующим образом:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Исправлена проблема VPN-подключения, обнаруженная в выпуске 1902.

- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1904.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) нельзя применять к Azure Stack ASDK.

