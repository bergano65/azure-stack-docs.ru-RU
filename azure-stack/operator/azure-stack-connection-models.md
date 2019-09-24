---
title: Модели подключения интегрированных систем Azure Stack | Документация Майкрософт
description: Определение моделей развертывания и другие решения по проектированию для интегрированных систем Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: de9051ceee89244182c1d6d9d5724fa80a594ae6
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094385"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Модели подключения интегрированных систем Azure Stack | Документация Майкрософт
Если вы заинтересованы в приобретении интегрированной системы Azure Stack, вам следует изучить [некоторые рекомендации по интеграции центра обработки данных](azure-stack-datacenter-integration.md) для развертывания Azure Stack, чтобы определить, как эта система будет внедрена в центр обработки данных. Кроме того, необходимо решить, как Azure Stack будет интегрироваться с гибридной облачной средой. В этой статье представлен обзор основных решений, в том числе по выбору модели подключения к Azure, хранилища удостоверений и модели выставления счетов.

Если вы решите приобрести интегрированную систему, ваш поставщик от изготовителя оборудования (OEM) поможет более подробно изучить многие вопросы, имеющие отношение к процессу планирования. Также поставщик оборудования OEM выполняет фактическое развертывание.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Выбор модели подключения для развертывания Azure Stack
Можно развернуть Azure Stack с подключением к Интернету (и к Azure) или без подключения. Для максимально эффективной работы с Azure Stack, включая гибридные сценарии между Azure Stack и Azure, выполняйте развертывание с подключением к Azure. Этот выбор определяет, какие варианты доступны для хранилища удостоверений (Azure Active Directory или службы федерации Active Directory) и модели выставления счетов (выставление счетов с оплатой по мере использования или на основе емкости), как показано на схеме и в таблице ниже.

![Сценарии развертывания и выставления счетов Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Это самое важное решение! Решение о выборе между использованием службы федерации Active Directory (AD FS) или Azure Active Directory (Azure AD) принимается один раз во время развертывания. Вы не сможете изменить хранилище удостоверений без повторного развертывания всей системы.  


|Параметры|С подключением к Azure|Без подключения к Azure|
|-----|:-----:|:-----:|
|Azure AD|![Поддерживаются](media/azure-stack-connection-models/check.png)| |
|AD FS|![Поддерживаются](media/azure-stack-connection-models/check.png)|![Поддерживаются](media/azure-stack-connection-models/check.png)|
|Выставление счетов на основе использования|![Поддерживаются](media/azure-stack-connection-models/check.png)| |
|Выставление счетов на основе емкости|![Поддерживаются](media/azure-stack-connection-models/check.png)|![Поддерживаются](media/azure-stack-connection-models/check.png)|
|Лицензирование| Соглашение Enterprise или поставщик облачных решений | Соглашение Enterprise |
|Исправления и обновления|Пакет обновления можно загрузить непосредственно из Интернета в Azure Stack. |  Обязательно<br><br>Кроме того, требуются съемный носитель<br> и отдельное подключенное устройство. |
| Регистрация | Автоматическая регистрация. | Обязательно<br><br>Кроме того, требуются съемный носитель<br> и отдельное подключенное устройство. |

Когда вы решите, какую модель подключения к Azure использовать для развертывания Azure Stack, в зависимости от выбранной модели подключения нужно принять дополнительные решения о хранилище удостоверений и методе выставления счетов.

## <a name="next-steps"></a>Дополнительная информация

[Решения, связанные с развертыванием Azure Stack с подключением к Azure](azure-stack-connected-deployment.md)

[Решения, связанные с развертыванием Azure Stack без подключения к Azure](azure-stack-disconnected-deployment.md)
