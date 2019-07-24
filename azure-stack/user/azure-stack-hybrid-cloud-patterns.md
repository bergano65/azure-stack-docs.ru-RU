---
title: Создание решений на основе конструктивных шаблонов гибридного облака для Azure Stack | Документация Майкрософт
description: Сведения о создании гибридных облачных приложений с помощью Azure и Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 538a626b2e89d15aa4b816674dbb8c374ec4a262
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286808"
---
#  <a name="build-solutions-hybrid-cloud-design-patterns-for-azure-stack"></a>Создание решений на основе конструктивных шаблонов гибридного облака для Azure Stack

Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость облачных вычислений в локальной среде и пограничной зоне за счет реализации гибридного облака. Вы можете создавать гибридные облачные приложения в Azure и развертывать их в подключенном или неподключенном центре обработки данных, расположенном в любой точке мира.

Microsoft Azure — это согласованное гибридное облако. Azure позволяет повторно использовать код, разработанный в Azure, и развертывать приложения в независимых облаках Azure и Azure Stack. Приложения, которые выполняются в облаках, также называют *гибридными приложениями*.

Гибридные сценарии могут значительно отличаться в зависимости от ресурсов, доступных для разработки. В таких сценариях учитываются такие аспекты, как географическое расположение, безопасность, доступ к Интернету и др. Хотя эти сценарии могут не соответствовать вашим требованиям, они предоставляют некоторые ключевые рекомендации и примеры, связанные с реализацией гибридных решений.

## <a name="hybrid-cloud-patterns"></a>Шаблоны для гибридного облака

- [Шаблон масштабирования в нескольких облаках](azure-stack-edge-pattern-cross-cloud-scaling.md)
- [Геораспределенный шаблон](azure-stack-edge-pattern-geo-distribution.md)
- [Шаблон DevOps](azure-stack-edge-pattern-hybrid-ci-cd.md)

## <a name="step-by-step-tutorials"></a>Пошаговые инструкции

- [Развертывание приложений в Azure и Azure Stack](azure-stack-solution-pipeline.md)
- [Развертывание приложений в Azure Stack и Azure](azure-stack-solution-hybrid-identity.md)
- [Настройка удостоверения гибридного облака в приложениях Azure и Azure Stack](azure-stack-solution-hybrid-connectivity.md)
- [Настройка подключения к гибридному облаку в Azure и Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Создание решения для аналитики промежуточных данных с помощью Azure и Azure Stack](azure-stack-solution-staged-data.md)
- [Создание решений для масштабирования в нескольких облаках в Azure](azure-stack-solution-cloud-burst.md)
- [Создание решения для географически распределенного приложения с помощью Azure и Azure Stack](azure-stack-solution-geo-distributed.md)
- [Развертывание гибридного облачного решения с помощью Azure и Azure Stack](azure-stack-solution-hybrid-cloud.md)
- [Развертывание MongoDB в Azure и Azure Stack](azure-stack-solution-mongodb-ha.md)
- [Развертывание SQL Server 2016 в Azure и Azure Stack](azure-stack-solution-sql-ha.md)


## <a name="next-steps"></a>Дополнительная информация

- В [рекомендациях по проектированию гибридных приложений](azure-stack-edge-pattern-overview.md) описаны характеристики качественного программного обеспечения, которых следует придерживаться при разработке, развертывании и использовании гибридных приложений.
- [Настройте среду разработки в Azure Stack](azure-stack-dev-start.md) и [разверните свое первое приложение](azure-stack-dev-start-deploy-app.md) в Azure Stack.
