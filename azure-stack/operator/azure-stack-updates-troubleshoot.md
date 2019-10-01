---
title: Устранение неполадок с обновлениями в Azure Stack | Документация Майкрософт
description: Узнайте, как оператор Azure Stack может устранять проблемы с обновлением, чтобы как можно скорее вернуть Azure Stack в эксплуатацию.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301247"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Устранение проблем с исправлениями и обновлениями в Azure Stack

*Область применения: интегрированные системы Azure Stack*

Рекомендации, приведенные в этой статье, можно использовать для устранения проблем, возникающих при обновлении Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Применимо**. Данная проблема касается всех поддерживаемых выпусков.

**Причина.** При попытке установить обновление для Azure Stack может произойти сбой состояния обновления, после чего его состояние изменится на `PreparationFailed`. Причина этого в том, что поставщику ресурсов обновления (URP) не удается правильно передать файлы из контейнера хранилища в общую папку во внутренней инфраструктуре для обработки.

**Исправление**. Начиная с версии 1901 (1.1901.0.95), эту проблему можно обойти, еще раз щелкнув **Обновить сейчас** (а не **Возобновить**). URP очистит файлы предыдущей попытки и повторит скачивание. Если проблема не будет устранена, рекомендуем вручную загрузить пакет обновления, следуя инструкциям по [установке обновлений](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Периодичность**. Common

## <a name="next-steps"></a>Дополнительная информация

- [Обновление Azure Stack](azure-stack-updates.md)  
- [Справка и поддержка по Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
