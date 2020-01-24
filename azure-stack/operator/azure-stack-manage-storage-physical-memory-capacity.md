---
title: Управление емкостью физической памяти в Azure Stack Hub | Документация Майкрософт
description: Узнайте, как отслеживать емкость и физическую память и управлять ими в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cfacebb9c4589332b57d3140b6ef43c1156ea55c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882340"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Управление емкостью физической памяти в Azure Stack Hub

Чтобы увеличить общую емкость доступной памяти в Azure Stack Hub, можно добавить дополнительный объем памяти. Физический сервер в Azure Stack Hub также называется *узлом единицы масштабирования*. Все узлы единиц масштабирования, входящие в одну единицу масштабирования, должны иметь одинаковый объем памяти.

> [!note]  
> Прежде чем продолжить, обратитесь к документации изготовителя оборудования, чтобы узнать, поддерживается ли в этом оборудовании физическое обновление памяти. По контракту на поддержку с поставщиком оборудования OEM от поставщика может требоваться выполнять физическую замену серверной стойки и обновление встроенного ПО устройства.

На блок-схеме ниже показан общий процесс добавления памяти для каждого узла единицы масштабирования.

![Процесс добавления памяти в каждый узел единицы масштабирования](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Добавление памяти в существующий узел
В следующих шагах представлен общий обзор процесса добавления памяти.

> [!Warning]
> Не выполняйте приведенные ниже действия, не ознакомившись с документацией изготовителя оборудования.
> 
> [!Warning]
> Вся единица масштабирования должна быть отключена, так как последовательное обновление памяти не поддерживается.

1. Остановите Azure Stack Hub, следуя инструкциям, приведенным в статье [Запуск и остановка Azure Stack Hub](azure-stack-start-and-stop.md).
2. Обновите память на каждом физическом компьютере, используя документацию изготовителя оборудования.
3. Запустите Azure Stack Hub, следуя инструкциям, приведенным в статье [Запуск и остановка Azure Stack Hub](azure-stack-start-and-stop.md).

## <a name="next-steps"></a>Дальнейшие действия

 - Сведения об управлении учетными записями хранения в Azure Stack Hub см. в разделе [Управление учетными записями хранения Azure Stack Hub](azure-stack-manage-storage-accounts.md).
 - Сведения об отслеживании емкости хранилища Azure Stack Hub и управления ею, см. в разделе [Управление емкостью хранилища для Azure Stack Hub](azure-stack-manage-storage-shares.md).
