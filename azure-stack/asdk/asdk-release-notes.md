---
title: Заметки о выпуске ASDK
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 03/20/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 32b4bfb500a9717f99085fe0759297f999244bbb
ms.sourcegitcommit: 961e3b1fae32d7f9567359fa3f7cb13cdc37e28e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80152230"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какую версию используете, [проверьте ее с помощью портала](../operator/azure-stack-updates.md).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

::: moniker range="azs-2002"
## <a name="build-12002035"></a>Сборка 1.2002.0.35

### <a name="new-features"></a>новые функции;

- Список исправленных проблем, изменений и новых функций в этом выпуске см. в соответствующих разделах статьи [Обновления Azure Stack: заметки о выпуске](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Пароль расшифровки сертификата — это новый параметр, который позволяет указать пароль для самозаверяющего сертификата (PFX), содержащего закрытый ключ, необходимый для расшифровки данных резервного копирования. Такой пароль требуется, только если резервная копия зашифрована с помощью сертификата.
- Список известных проблем Azure Stack в этом выпуске см. в [этой статье](../operator/known-issues.md).
- Обратите внимание, что доступные исправления Azure Stack неприменимы к ASDK.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>Сборка 1.1910.0.58

### <a name="new-features"></a>новые функции;

- Список исправленных проблем, изменений и новых функций в этом выпуске см. в соответствующих разделах статьи [Обновления Azure Stack: заметки о выпуске](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Исправлена проблема со сбором журналов и их хранением в контейнере больших двоичных объектов службы хранилища Azure. Ниже приведен синтаксис данной операции:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Исправлена проблема развертывания, при которой служба очереди печати с медленной загрузкой предотвращает удаление некоторых компонентов Windows и требует перезагрузки.
- Список известных проблем Azure Stack в этом выпуске см. в [этой статье](../operator/known-issues.md).
- Обратите внимание, что доступные исправления Azure Stack неприменимы к ASDK.
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>Сборка 1.1908.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1908).
- Обратите внимание, что доступные исправления Azure Stack неприменимы к ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>Сборка 1.1907.0.20

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) заметок к выпуску Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов marketplace развертывание могло не завершиться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**.
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](/azure-stack/operator/known-issues?view=azs-1907).
- Обратите внимание, что [доступные исправления Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) нельзя применять к Azure Stack ASDK.
::: moniker-end
