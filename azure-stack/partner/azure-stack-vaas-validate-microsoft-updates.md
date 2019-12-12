---
title: Проверка обновлений программного обеспечения корпорации Майкрософт с помощью проверки как услуги Azure Stack | Документация Майкрософт
description: Узнайте, как проверять обновления программного обеспечения корпорации Майкрософт с помощью проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3399288b801114f076afe3b68d3f9790bfe2af29
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954491"
---
# <a name="validate-software-updates-from-microsoft"></a>Проверка обновлений программного обеспечения от корпорации Майкрософт

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Корпорация Майкрософт периодически выпускает обновления для программного обеспечения Azure Stack. Эти обновления предоставляются партнерам по совместной разработке Azure Stack. Обновления предоставляются до того, как они станут общедоступными. Вы можете проверить наличие обновлений для своего решения и отправить отзыв корпорации Майкрософт.

Обновления программного обеспечения Майкрософт для Azure Stack обозначены с использованием соглашения об именовании. Например, 1803 указывает на обновление за март 2018 г. Сведения о политике обновления, заметках о частоте и выпуске Azure Stack см. в разделе [Политика обслуживания Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем выполнять ежемесячный процесс обновления в VaaS, необходимо ознакомиться со следующими темами:

- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Проверка как услуга: основные понятия)

## <a name="required-tests"></a>Обязательные тесты

Для ежемесячной проверки программного обеспечения приведенные ниже тесты нужно выполнять в следующем порядке:

- Рабочий процесс проверки OEM

## <a name="validating-software-updates"></a>Проверка обновлений программного обеспечения

1. Создайте рабочий процесс **проверки пакетов**.
1. Для перечисленных выше обязательных тестов выполните инструкции из раздела [Запуск тестов проверки пакета](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Дополнительные инструкции о тесте **проверки ежемесячного обновления Azure Stack** см. в разделе ниже.

Если у вас есть вопросы или проблемы, обращайтесь в [службу поддержки VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)