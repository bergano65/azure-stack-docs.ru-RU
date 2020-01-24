---
title: Модели подключения интегрированных систем Azure Stack Hub | Документация Майкрософт
description: Определение моделей подключения и других решений по проектированию для интегрированных систем Azure Stack Hub.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 6347d758a4dde2b06db6317a9a322df61014ba61
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817760"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Модели подключения интегрированных систем Azure Stack Hub | Документация Майкрософт
Если вы заинтересованы в приобретении интегрированной системы Azure Stack Hub, вам следует изучить [некоторые рекомендации по интеграции центра обработки данных](azure-stack-datacenter-integration.md) для развертывания Azure Stack Hub, чтобы определить, как эта система будет внедрена в центр обработки данных. Кроме того, необходимо решить, как Azure Stack Hub будет интегрироваться с гибридной облачной средой. В этой статье представлен обзор основных решений, в том числе по выбору модели подключения к Azure, хранилища удостоверений и модели выставления счетов.

Если вы решите приобрести интегрированную систему, ваш поставщик от изготовителя оборудования (OEM) поможет более подробно изучить многие вопросы, имеющие отношение к процессу планирования. Также поставщик оборудования OEM выполняет фактическое развертывание.

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>Выбор модели подключения для развертывания Azure Stack Hub
Вы можете выбрать развертывание Azure Stack Hub с подключением к Интернету (и Azure) или в автономном режиме. Для максимально эффективной работы с Azure Stack Hub, включая гибридные сценарии между Azure Stack Hub и Azure, выполняйте развертывание с подключением к Azure. Этот выбор определяет, какие варианты доступны для хранилища удостоверений (Azure Active Directory или службы федерации Active Directory) и модели выставления счетов (выставление счетов с оплатой по мере использования или на основе емкости), как показано на схеме и в таблице ниже.

![Сценарии развертывания и выставления счетов Azure Stack Hub](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Это самое важное решение! Решение о выборе между использованием службы федерации Active Directory (AD FS) или Azure Active Directory (Azure AD) принимается один раз во время развертывания. Вы не сможете изменить хранилище удостоверений без повторного развертывания всей системы.  


|Параметры|С подключением к Azure|Без подключения к Azure|
|-----|:-----:|:-----:|
|Azure AD|![Поддерживается](media/azure-stack-connection-models/check.png)| |
|AD FS|![Поддерживается](media/azure-stack-connection-models/check.png)|![Поддерживается](media/azure-stack-connection-models/check.png)|
|Выставление счетов на основе использования|![Поддерживается](media/azure-stack-connection-models/check.png)| |
|Выставление счетов на основе емкости|![Поддерживается](media/azure-stack-connection-models/check.png)|![Поддерживается](media/azure-stack-connection-models/check.png)|
|Лицензирование| Соглашение Enterprise или поставщик облачных решений | Соглашение Enterprise |
|Исправления и обновления|Пакет обновления можно загрузить непосредственно из Интернета в Azure Stack Hub |  Обязательно<br><br>Кроме того, требуются съемный носитель<br> и отдельное подключенное устройство. |
| Регистрация | Автоматическая регистрация. | Обязательно<br><br>Кроме того, требуются съемный носитель<br> и отдельное подключенное устройство. |

Когда вы решите, какую модель подключения к Azure использовать для развертывания Azure Stack Hub, в зависимости от выбранной модели подключения нужно принять дополнительные решения о хранилище удостоверений и методе выставления счетов.

## <a name="next-steps"></a>Дальнейшие действия

[Решения, связанные с развертыванием Azure Stack Hub с подключением к Azure](azure-stack-connected-deployment.md)

[Решения, связанные с развертыванием Azure Stack Hub без подключения к Azure](azure-stack-disconnected-deployment.md)
