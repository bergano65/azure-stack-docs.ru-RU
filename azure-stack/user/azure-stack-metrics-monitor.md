---
title: Использование данных мониторинга из Azure Stack Hub
description: Узнайте о вариантах использования данных мониторинга из Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: bb03193c1c4b061a996e3bef135c6cfc630f36b0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883423"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Использование данных мониторинга из Azure Stack Hub

Данные мониторинга можно найти в одном расположении с помощью конвейера Azure Monitor, как и данные Azure Monitor в глобальной среде Azure. Но не все данные мониторинга, найденные в глобальной среде Azure, доступны в Azure Stack Hub. В этой статье приводятся сводные сведения о различных способах использования данных мониторинга в Azure Stack Hub.
 
## <a name="options-for-data-consumption"></a>Варианты использования данных

| Тип данных | Категория | Поддерживаемые службы | Варианты доступа |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Метрики Azure Monitor уровня платформы | Метрики | [Метрики, поддерживаемые Azure Monitor в Azure Stack Hub](azure-stack-metrics-supported.md) | REST API |
| Метрики вычислений гостевой ОС (например, счетчики производительности) | Метрики | Виртуальные машины Windows и Linux | Хранилище таблиц или BLOB-объектов:<br>Система диагностики Azure (Linux или Windows) <br>Концентратор событий:<br>Система диагностики Azure (Windows) |
| Метрики хранения | Метрики | Хранилище Azure | Таблица хранилища:<br>аналитики хранилища |
| Журнал действий | События | Все службы Azure | REST API:<br>API событий Azure Monitor |
| Журналы вычислений гостевой ОС (например, системные журналы IIS, ETW) | События | Виртуальные машины Windows и Linux | Хранилище таблиц или BLOB-объектов:<br>Система диагностики Azure (Linux или Windows) <br>Концентратор событий:<br>Система диагностики Azure (Windows) |
| Журналы хранилища | События | Хранилище Azure | Таблица хранилища:<br>аналитики хранилища |

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о службе [Azure Monitor в Azure Stack Hub](azure-stack-metrics-azure-data.md).
