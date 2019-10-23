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
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257739"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Устранение проблем с исправлениями и обновлениями в Azure Stack

*Область применения: интегрированные системы Azure Stack*

Рекомендации, приведенные в этой статье, можно использовать для устранения проблем, возникающих при обновлении Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Применимо**. Данная проблема касается всех поддерживаемых выпусков.

**Причина.** При попытке установить обновление для Azure Stack может произойти сбой состояния обновления, после чего его состояние изменится на `PreparationFailed`. В системах, подключенных к Интернету, обычно это указывает на то, что не удается правильно скачать пакет обновления из-за слабого подключения к Интернету. 

**Исправление**. Чтобы устранить эту ошибку, щелкните **Install now** (Установить сейчас) снова. Если проблема не будет устранена, рекомендуем вручную загрузить пакет обновления, следуя инструкциям по [установке обновлений](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Периодичность**. Common

## <a name="next-steps"></a>Дополнительная информация

- [Обновление Azure Stack](azure-stack-updates.md)  
- [Справка и поддержка по Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
