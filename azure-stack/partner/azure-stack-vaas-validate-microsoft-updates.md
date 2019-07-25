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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a507b7488a34715e528b6bbf291fec9832ef027
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418280"
---
# <a name="validate-software-updates-from-microsoft"></a>Проверка обновлений программного обеспечения от корпорации Майкрософт

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Корпорация Майкрософт периодически выпускает обновления для программного обеспечения Azure Stack. Эти обновления предоставляются партнерам по совместной разработке Azure Stack. Обновления предоставляются до того, как они станут общедоступными. Вы можете проверить наличие обновлений для своего решения и отправить отзыв корпорации Майкрософт.

Обновления программного обеспечения Майкрософт для Azure Stack обозначены с использованием соглашения об именовании. Например, 1803 указывает на обновление за март 2018 г. Сведения о политике обновления, заметках о частоте и выпуске Azure Stack см. в разделе [Политика обслуживания Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем выполнять ежемесячный процесс обновления в VaaS, необходимо ознакомиться со следующими темами:

- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Проверка как услуга: основные понятия)
- [тестирование интерактивной проверки функций](azure-stack-vaas-interactive-feature-verification.md).

## <a name="required-tests"></a>Обязательные тесты

Для ежемесячной проверки программного обеспечения приведенные ниже тесты необходимо выполнять в следующем порядке:

1. Monthly Azure Stack Update Verification;
2. Cloud Simulation Engine (Механизм имитации в облаке).

## <a name="validating-software-updates"></a>Проверка обновлений программного обеспечения

1. Создайте рабочий процесс **проверки пакетов**.
1. Для перечисленных выше обязательных тестов выполните инструкции из раздела [Запуск тестов проверки пакета](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Дополнительные инструкции о тесте **проверки ежемесячного обновления Azure Stack** см. в разделе ниже.

### <a name="apply-the-monthly-update"></a>Применение ежемесячного обновления

1. Выберите агент для выполнения тестов.
1. Запланируйте **проверку ежемесячного обновления Azure Stack**.
1. Укажите расположение пакета расширения OEM, в данный момент развернутого в метке, и расположение пакета расширения OEM, который будет применяться во время обновления. Сведения о настройке URL-адресов для этих пакетов см. в разделе об [управлении пакетами для проверки](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Выполните действия из выбранного агента в пользовательском интерфейсе.

Если у вас есть вопросы или проблемы, обращайтесь в [службу поддержки VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)