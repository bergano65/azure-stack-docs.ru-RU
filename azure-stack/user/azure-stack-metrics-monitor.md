---
title: Использование данных мониторинга из Azure Stack | Документация Майкрософт
description: Узнайте о вариантах использования данных мониторинга из Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 64935022f8ace33bd4350ab4ef5421636ff3991e
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418510"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Использование данных мониторинга из Azure Stack

*Область применения: интегрированные системы Azure Stack*

Данные мониторинга можно найти в одном расположении с помощью конвейера Azure Monitor, как и данные Azure Monitor в глобальной среде Azure. Но не все данные мониторинга, найденные в глобальной среде Azure, доступны в Azure Stack. В этой статье приводятся сводные сведения о различных способах использования данных мониторинга в Azure Stack.
 
## <a name="options-for-data-consumption"></a>Варианты использования данных

| Тип данных | Категория | Поддерживаемые службы | Варианты доступа |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Метрики Azure Monitor уровня платформы | Метрики | [Метрики, поддерживаемые Azure Monitor в Azure Stack](azure-stack-metrics-supported.md) | REST API |
| Метрики вычислений гостевой ОС (например, счетчики производительности) | Метрики | Виртуальные машины Windows и Linux | Хранилище таблиц или BLOB-объектов:<br>Система диагностики Azure (Linux или Windows) <br>Концентратор событий:<br>Система диагностики Azure (Windows) |
| Метрики хранения | Метрики | Хранилище Azure | Таблица хранилища:<br>аналитики хранилища |
| Журнал действий | События | Все службы Azure | REST API:<br>API событий Azure Monitor |
| Журналы вычислений гостевой ОС (например, системные журналы IIS, ETW) | События | Виртуальные машины Windows и Linux | Хранилище таблиц или BLOB-объектов:<br>Система диагностики Azure (Linux или Windows) <br>Концентратор событий:<br>Система диагностики Azure (Windows) |
| Журналы хранилища | События | Хранилище Azure | Таблица хранилища:<br>аналитики хранилища |

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о службе [Azure Monitor в Azure Stack](azure-stack-metrics-azure-data.md).
