---
title: Заметки о выпуске для поставщика ресурсов MySQL 1.1.47.0 для Azure Stack Hub | Документация Майкрософт
description: Просмотрите заметки о выпуске, чтобы узнать, что нового в обновлении поставщика ресурсов MySQL 1.1.47.0 для Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 43bbfb0c044dc90986a099c1d5729ad54082dc88
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882221"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Заметки о выпуске для поставщика ресурсов MySQL 1.1.47.0

В этих заметках о выпуске описываются улучшения и известные проблемы поставщика ресурсов MySQL версии 1.1.47.0.

## <a name="build-reference"></a>Указание сборки
Скачайте двоичный файл поставщика ресурсов MySQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack Hub. Минимальная версия выпуска Azure Stack Hub, необходимая для установки этой версии поставщика ресурсов MySQL:

> |минимальная версия Azure Stack Hub|Версия поставщика ресурсов MySQL|
> |-----|-----|
> |Версия 1910 (1.1910.0.58)|[Поставщик ресурсов MySQL версии 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Установите минимальное поддерживаемое обновление Azure Stack Hub для вашей интегрированной системы Azure Stack Hub или разверните последнюю версию Пакета средств разработки Azure Stack (ASDK) перед развертыванием последней версии поставщика ресурсов MySQL.

## <a name="new-features-and-fixes"></a>Новые функции и исправления

Эта версия поставщика ресурсов Azure Stack Hub MySQL — это выпуск исправлений, позволяющий обеспечить совместимость поставщика ресурсов с некоторыми последними изменениями портала в обновлении 1910 без каких-либо новых функций.

Она также поддерживает текущий профиль версии API Azure Stack Hub 2019-03-01 — гибридный модуль и модуль Azure Stack Hub PowerShell 1.8.0. Поэтому во время развертывания и обновления не нужно устанавливать специальные версии журналов модулей.

Советуем применить исправление 1.1.47.0 для поставщика ресурсов MySQL после обновления Azure Stack Hub до версии 1910.

## <a name="known-issues"></a>Известные проблемы

Нет.

## <a name="next-steps"></a>Дальнейшие действия
[Использование баз данных MySQL в Microsoft Azure Stack](azure-stack-mysql-resource-provider.md)

[Развертывание поставщика ресурсов MySQL в Azure Stack](azure-stack-mysql-resource-provider-deploy.md#prerequisites)

[Обновление поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-update.md) 
