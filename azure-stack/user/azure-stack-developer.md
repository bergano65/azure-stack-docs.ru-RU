---
title: Разработка приложений для Azure Stack | Документация Майкрософт
description: Рекомендации по разработке для создания прототипов приложений в Azure Stack с использованием служб Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 059e7961cae5c6d6faa8d79d05dbc08a05a13893
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658566"
---
# <a name="develop-for-azure-stack"></a>Разработка для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете сразу приступить к разработке приложений даже при отсутствии доступа к среде Azure Stack. Azure Stack предоставляет службы Microsoft Azure, выполняемые в вашем центре обработки данных. Это значит, что при разработке в среде Azure Stack вы можете использовать те же инструменты и процессы, что и для Azure.

## <a name="development-considerations"></a>Вопросы разработки

Подготовившись и ознакомившись с приведенными ниже разделами, вы сможете использовать Azure для эмуляции среды Azure Stack.

* В Azure можно создавать шаблоны Azure Resource Manager, которые можно развертывать в Azure Stack. Ознакомьтесь с [руководством по разработке шаблонов для обеспечения переносимости](azure-stack-develop-templates.md).
* Среды Azure и Azure Stack отличаются уровнем доступности служб и возможностями управления версиями. Вы можете использовать [модуль политики Azure Stack](azure-stack-policy-module.md), чтобы ограничить доступность служб и типы ресурсов Azure для компонентов, доступных в Azure Stack. Ограничив доступность служб, вы обеспечите использование приложениями служб, доступных в Azure Stack.
* [Шаблоны быстрого запуска для Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) — это общие примеры того, как создавать шаблоны, которые можно развертывать как в Azure, так и в Azure Stack.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о разработке в Stack см. в следующих статьях:

* [Рекомендации по работе с шаблонами Azure Resource Manager](azure-stack-develop-templates.md)
* [Microsoft Azure Stack Quickstart Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) (Шаблоны быстрого запуска Microsoft Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates)