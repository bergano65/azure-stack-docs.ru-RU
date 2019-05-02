---
title: Использование баз данных MySQL как PaaS в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть поставщик ресурсов MySQL и предоставить базы данных MySQL как услугу в Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: e610f946868940f9fde66932bedec1dc998f390b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307100"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Использование баз данных MySQL в Microsoft Azure Stack

Базы данных MySQL обычно используются на веб-сайтах и поддерживают множество платформ веб-сайтов. Например, можно создавать веб-сайты WordPress с помощью надстройки поставщика ресурсов служб приложений (PaaS).

После развертывания поставщика ресурсов вы можете:

* создавать серверы и базы данных MySQL, используя шаблоны развертывания Azure Resource Manager;
* предоставлять базы данных MySQL как службу.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов MySQL

Поставщик ресурсов состоит из следующих компонентов.

* **Виртуальная машина адаптера поставщика ресурсов MySQL** — виртуальная машина Windows Server, на которой работают службы поставщика.
* **Поставщик ресурсов** — обрабатывает запросы к ресурсам базы данных и предоставляет к ним доступ.
* **Серверы, на которых размещается сервер MySQL Server**. Эти серверы размещения предоставляют емкость для баз данных. Можно создавать экземпляры MySQL самостоятельно или предоставить доступ к внешним экземплярам MySQL. [Коллекция быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) содержит пример шаблона, который позволяет:

  * создать сервер MySQL;
  * скачать и развернуть сервер MySQL Server из Microsoft Azure Marketplace.

> [!NOTE]
> Серверы размещения, установленные в интегрированных системах Azure Stack, необходимо создавать на основе подписки клиента. Их нельзя создать из подписки поставщика по умолчанию. Эти серверы необходимо создавать на портале клиента или в сеансе PowerShell, используя соответствующее имя для входа. Все серверы размещения — это платные виртуальные машины, которые должны иметь соответствующие лицензии. Администратор служб может быть владельцем подписки клиента.

### <a name="required-privileges"></a>Необходимые привилегии

У системной учетной записи должны быть такие разрешения:

* **база данных** — создание, удаление;
* **имя для входа** — создание, установка параметров, удаление, предоставление разрешений, отмена.  

## <a name="next-steps"></a>Дополнительная информация

[Развертывание поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-deploy.md)
