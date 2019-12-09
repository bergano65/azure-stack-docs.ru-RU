---
title: Заметки о выпуске поставщика ресурсов SQL версии 1.1.47.0 для Azure Stack | Документация Майкрософт
description: Сведения о новых возможностях в последнем обновлении поставщика ресурсов SQL для Azure Stack, об известных проблемах и о том, где можно скачать обновление.
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
ms.openlocfilehash: 703edc0cc2c5229c3301db0b68a400507fe4c872
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823530"
---
# <a name="sql-resource-provider-11470-release-notes"></a>Заметки о выпуске для поставщика ресурсов SQL 1.1.47.0

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описываются улучшения и известные проблемы поставщика ресурсов SQL версии 1.1.47.0.

## <a name="build-reference"></a>Указание сборки
Загрузите двоичный файл поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Минимальная версия выпуска Azure Stack, необходимая для установки этой версии поставщика ресурсов SQL:

> |Минимальная версия Azure Stack|Версия поставщика ресурсов SQL|
> |-----|-----|
> |Версия 1910 (1.1910.0.58)|[Поставщик ресурсов SQL версии 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Установите минимальное поддерживаемое обновление Azure Stack для вашей интегрированной системы Azure Stack или разверните последнюю версию Пакета средств разработки Azure Stack (ASDK) перед развертыванием последней версии поставщика ресурсов SQL.

## <a name="new-features-and-fixes"></a>Новые функции и исправления

Эта версия поставщика ресурсов Azure Stack SQL — это выпуск исправлений, позволяющий обеспечить совместимость поставщика ресурсов с некоторыми последними изменениями портала в обновлении 1910 без каких-либо новых функций.

Она также поддерживает текущий профиль версии API Azure Stack 2019-03-01 — гибридный модуль и модуль Azure Stack PowerShell 1.8.0. Поэтому во время развертывания и обновления не нужно устанавливать специальные версии журналов модулей.

Выполните процесс обновления поставщика ресурсов SQL, чтобы применить для него исправление версии 1.1.47.0, после обновления Azure Stack до версии 1910. Она поможет устранить известную ошибку на портале администрирования, при которой мониторинг емкости в поставщике ресурсов SQL обеспечивает загрузку.

## <a name="known-issues"></a>Известные проблемы

Отсутствует.

## <a name="next-steps"></a>Дополнительная информация
[Использование баз данных SQL в Microsoft Azure Stack](azure-stack-sql-resource-provider.md)

[Развертывание поставщика ресурсов SQL Server в Azure Stack](azure-stack-sql-resource-provider-deploy.md#prerequisites)

[Обновление поставщика ресурсов SQL](azure-stack-sql-resource-provider-update.md) 
