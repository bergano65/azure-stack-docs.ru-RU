---
title: Проверка обновлений программного обеспечения от корпорации Майкрософт
titleSuffix: Azure Stack Hub
description: Сведения о проверке обновлений программного обеспечения корпорации Майкрософт с помощью службы "Проверка как услуга" Azure Stack Hub.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a8e0b3ee678fc56a94a947ab6d390d9e99296977
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625362"
---
# <a name="validate-software-updates-from-microsoft"></a>Проверка обновлений программного обеспечения от корпорации Майкрософт

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Корпорация Майкрософт периодически выпускает обновления для программного обеспечения Azure Stack Hub. Эти обновления предоставляются партнерам по совместной разработке Azure Stack Hub. Эти обновления предоставляются до того, как появятся общедоступные обновления. Вы можете проверить наличие обновлений для своего решения и отправить отзыв корпорации Майкрософт.

Обновления программного обеспечения Майкрософт для Azure Stack Hub обозначены с использованием соглашения об именовании. Например, имя 1803 указывает, что это обновление за март 2018 г. Сведения о политике обслуживания и заметки о выпуске для Azure Stack Hub см. в статье [Политика обслуживания Azure Stack Hub](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем выполнять ежемесячный процесс обновления в VaaS (проверка как услуга), необходимо ознакомиться со следующими темами:

- [Проверка как услуга: основные понятия](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Обязательные тесты

Для ежемесячной проверки программного обеспечения приведенные ниже тесты нужно выполнять в следующем порядке:

- Рабочий процесс проверки OEM

## <a name="validating-software-updates"></a>Проверка обновлений программного обеспечения

1. Создайте рабочий процесс **проверки пакетов**.
1. Для перечисленных выше обязательных тестов выполните инструкции из раздела [Запуск тестов проверки пакета](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Дополнительные инструкции по тесту для **ежемесячной проверки обновлений Azure Stack Hub** см. в разделе ниже.

Если у вас есть вопросы или проблемы, обращайтесь в [службу поддержки VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Дальнейшие действия

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)