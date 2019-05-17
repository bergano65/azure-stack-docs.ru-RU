---
title: Создание гибридных облачных приложений с помощью Azure и Azure Stack | Документация Майкрософт
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 05/06/2019
ms.openlocfilehash: a1e13b471c9eaed9dcac79c4002ceca6b3b8e7d2
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212672"
---
# <a name="create-hybrid-cloud-apps-with-azure-and-azure-stack"></a>Создание гибридных облачных приложений с помощью Azure и Azure Stack

Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость облачных вычислений в локальной среде и пограничной зоне за счет реализации гибридного облака. Вы можете создавать гибридные облачные приложения в Azure и развертывать их в подключенном или неподключенном центре обработки данных, расположенном в любой точке мира.

Microsoft Azure — это согласованное гибридное облако. Azure позволяет повторно использовать код, разработанный в Azure, и развертывать приложения в независимых облаках Azure и Azure Stack. Приложения, которые выполняются в облаках, также называют *гибридными приложениями*.

Гибридные сценарии могут значительно отличаться в зависимости от ресурсов, доступных для разработки. В таких сценариях учитываются такие аспекты, как географическое расположение, безопасность, доступ к Интернету и др. Хотя эти сценарии могут не соответствовать вашим требованиям, они предоставляют некоторые ключевые рекомендации и примеры, связанные с реализацией гибридных решений.

Гибридное облако можно использовать для таких сценариев:
- примеры многоуровневых данных;
- SQL Server в Azure и Azure Stack;
- комплексные развертывания базы данных Mongo в Azure и Azure Stack;
- обнаружение аннуляции выводов при использовании искусственного интеллекта в Microsoft Edge.

## <a name="step-by-step-tutorials"></a>Пошаговые руководства

- [Развертывание приложений в Azure и Azure Stack](azure-stack-solution-pipeline.md)
- [Развертывание приложений в Azure Stack и Azure](azure-stack-solution-hybrid-identity.md)
- [Настройка удостоверения гибридного облака в приложениях Azure и Azure Stack](azure-stack-solution-hybrid-connectivity.md)
- [Настройка подключения к гибридному облаку в Azure и Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Создание решения для аналитики промежуточных данных с помощью Azure и Azure Stack](azure-stack-solution-cloud-burst.md)
- [Создание решений для масштабирования в нескольких облаках в Azure](azure-stack-solution-cloud-burst.md)
- [Создание решения для географически распределенного приложения с помощью Azure и Azure Stack](azure-stack-solution-geo-distributed.md)
- [Развертывание гибридного облачного решения с помощью Azure и Azure Stack](azure-stack-solution-hybrid-cloud.md)

## <a name="next-steps"></a>Дополнительная информация

- В [рекомендациях по проектированию гибридных приложений](https://aka.ms/hybrid-cloud-applications-pillars) описаны характеристики качественного программного обеспечения, которых следует придерживаться при разработке, развертывании и использовании гибридных приложений.
- [Настройте среду разработки в Azure Stack](azure-stack-dev-start.md) и [разверните свое первое приложение](azure-stack-dev-start-deploy-app.md) в Azure Stack.
