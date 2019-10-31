---
title: Рекомендации по проверке Azure Stack. | Документация Майкрософт
description: В этой статье приведены рекомендации по использованию проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 930a8ea40fde7a021a893e5289d16fa73398300f
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167265"
---
# <a name="best-practices-for-validation-as-a-service"></a>Рекомендации по использованию проверки как услуги

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

В этой статье описываются рекомендации по управлению ресурсами при использовании проверки как услуги (VaaS). Обзор ресурсов VaaS приводится в статье [Проверка как услуга: основные понятия](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Управление решениями

### <a name="naming-convention-for-vaas-solutions"></a>Соглашение об именовании для решений VaaS

Используйте единое соглашение об именовании для всех решений, зарегистрированных в VaaS. Например, имя решения может формироваться на основе свойств оборудования следующим образом:

|Название продукта | Уникальный элемент оборудования 1 | Уникальный элемент оборудования 2 | Имя решения
|---|---|---|---|
My Solution XYZ |  All Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Когда следует создавать новое решение VaaS

При выполнении рабочих процессов для одного и того же номера SKU оборудования следует использовать одно решение VaaS. Новое решение VaaS следует создавать только в том случае, когда изменяется номер SKU оборудования.

## <a name="workflow-management"></a>Управление рабочими процессами

### <a name="naming-convention-for-vaas-workflows"></a>Соглашение об именовании для рабочих процессов VaaS

Используйте единое соглашение об именовании для всех запусков рабочих процессов VaaS. Например, имя рабочего процесса может формироваться на основе свойств сборки следующим образом:

|Номер сборки (основная) | Дата | Размер решения | Имя рабочего процесса
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Проверка как услуга: основные понятия](azure-stack-vaas-key-concepts.md)
