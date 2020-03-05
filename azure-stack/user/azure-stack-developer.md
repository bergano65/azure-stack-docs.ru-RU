---
title: Разработка приложений для Azure Stack Hub
description: Рекомендации по разработке для создания прототипов приложений в Azure Stack Hub с использованием служб Azure.
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 3404e0d0e5bbf2c8293d70a7cf816b9e4923261f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704086"
---
# <a name="develop-for-azure-stack-hub"></a>Разработка для Azure Stack Hub

Вы можете сразу приступить к разработке приложений даже при отсутствии доступа к среде Azure Stack Hub. Azure Stack Hub предоставляет службы Microsoft Azure, выполняемые в вашем центре обработки данных. Это значит, что при разработке в среде Azure Stack Hub вы можете использовать те же инструменты и процессы, что и для Azure.

## <a name="development-considerations"></a>Вопросы разработки

Подготовившись и ознакомившись с приведенными ниже разделами, вы сможете использовать Azure для эмуляции среды Azure Stack Hub.

* В Azure можно создавать шаблоны Azure Resource Manager, которые можно развертывать в Azure Stack Hub. Ознакомьтесь с [руководством по разработке шаблонов для обеспечения переносимости](azure-stack-develop-templates.md).
* Среды Azure и Azure Stack Hub отличаются уровнем доступности служб и возможностями управления версиями. Вы можете использовать [модуль политики Azure Stack Hub](azure-stack-policy-module.md), чтобы ограничить доступность служб и типы ресурсов Azure для компонентов, доступных в Azure Stack Hub. Ограничив доступность служб, вы обеспечите использование приложениями служб, доступных в Azure Stack Hub.
* [Шаблоны быстрого запуска для Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) — это общие примеры того, как создавать шаблоны, которые можно развертывать как в Azure, так и в Azure Stack Hub.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке в Azure Stack см. в следующих статьях:

* [Рекомендации по работе с шаблонами Azure Resource Manager](azure-stack-develop-templates.md)
* [Шаблоны быстрого запуска Azure Stack Hub в GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)
