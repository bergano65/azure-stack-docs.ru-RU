---
title: Устранение неполадок с обновлением в Azure Stack Hub
description: Узнайте, как оператор Azure Stack Hub может устранять проблемы с обновлением, чтобы как можно скорее вернуть Azure Stack Hub в эксплуатацию.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/23/2019
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 1c481538183098b6a1a88143dac339a919a248f3
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508690"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Рекомендации по устранению неполадок с исправлением и обновлением в Azure Stack Hub

В этой статье приводятся общие рекомендации по устранению неполадок с исправлением и обновлением в Azure Stack Hub, а также сведения об известных проблемах.


Процесс исправления и обновления Azure Stack Hub позволяет операторам единообразно и стабильно применять пакеты обновления. В редких случаях в ходе исправления и процесса обновления могут возникать проблемы. Мы рекомендуем выполнить следующие действия, если вы столкнетесь с проблемой при исправлении или обновлении.

0. **Предварительные требования**: Убедитесь, что вы выполнили [контрольный список действий по обновлению](release-notes-checklist.md) и [настроили автоматический сбор журналов](azure-stack-configure-automatic-diagnostic-log-collection.md).
1. Следуйте инструкциям по исправлению, которые будут включены в оповещение об ошибке, которая произошла при обновлении.
2. Изучите [распространенные проблемы с исправлением и обновлением в Azure Stack Hub](#common-azure-stack-hub-patch-and-update-issues) и выполните рекомендуемые действия, если ваша проблема описана.
3. Если указанные действия не помогают решить проблему, создайте [запрос в службу поддержки Azure Stack Hub](azure-stack-help-and-support-overview.md). Убедитесь, что у вас есть [собранные журналы](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) за тот период времени, в который возникла проблема.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Типичные проблемы с исправлением и обновлением в Azure Stack Hub

*Область применения: интегрированные системы Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Применимо**. Данная проблема касается всех поддерживаемых выпусков.

**Причина.** При попытке установить обновление для Azure Stack Hub может произойти сбой состояния обновления, после чего его состояние изменится на `PreparationFailed`. В системах, подключенных к Интернету, обычно это указывает на то, что не удается правильно скачать пакет обновления из-за слабого подключения к Интернету. 

**Исправление**. Чтобы устранить эту ошибку, щелкните **Install now** (Установить сейчас) снова. Если проблема не будет устранена, рекомендуем вручную загрузить пакет обновления, следуя инструкциям по [установке обновлений](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Периодичность**. Распространенные

## <a name="next-steps"></a>Дальнейшие действия

- [Обновление Azure Stack Hub](azure-stack-updates.md)  
- [Справка и поддержка по Microsoft Azure Stack Hub](azure-stack-help-and-support-overview.md)
