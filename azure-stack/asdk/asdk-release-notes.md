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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101031"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какую версию используете, [проверьте ее с помощью портала](../operator/azure-stack-updates.md).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

::: moniker range="azs-1908"
## <a name="build-11908020"></a>Сборка 1.1908.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1908).
- Обратите внимание, что доступные исправления Azure Stack неприменимы к ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Сборка 1.1907.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов marketplace развертывание могло не завершиться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1907).
- Обратите внимание, что [доступные исправления Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) нельзя применять к Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>Сборка 1.1906.0.30

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) заметок к выпуску Azure Stack.

### <a name="changes"></a>изменения

- Добавлена поддержка виртуальной машины для вызова **AzS-SRNG01**, в которой размещена служба сбора журналов для Azure Stack. Дополнительные сведения см. в статье [Архитектура ASDK](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов marketplace развертывание могло не завершиться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1906).
- Обратите внимание, что [доступные исправления Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) нельзя применять к Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>Сборка 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Исправлена проблема, при которой вам приходилось редактировать скрипт PowerShell **RegisterWithAzure.psm1** для успешной [регистрации ASDK](asdk-register.md).
- Список исправленных проблем для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1905).
- Обратите внимание, что [доступные исправления Azure Stack](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) нельзя применять к Azure Stack ASDK.
::: moniker-end
