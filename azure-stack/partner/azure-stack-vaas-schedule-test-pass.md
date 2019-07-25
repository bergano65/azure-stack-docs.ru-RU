---
title: Планирование первого теста с помощью портала проверки как услуги для Azure Stack | Документация Майкрософт
description: Запланируйте первый тест с помощью портала проверки как услуги для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: bf377234b71683e6c56bef030cd5de5b4a06a743
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418386"
---
# <a name="scheduling-a-test"></a>Планирование теста

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Запланируйте тест на портале проверки как услуги (VaaS) для решения Azure Stack. Решение VaaS представляет решение Azure Stack с определенными спецификациями оборудования. Вы можете запланировать тест, чтобы проверить, что на оборудовании можно запустить Azure Stack.

Чтобы проверить решение, создайте рабочий процесс для теста. Рабочий процесс VaaS работает в контексте решения VaaS. Он представляет набор тестов, проверяющих функциональность развертывания Azure Stack на вашем оборудовании. Добавьте параметры среды решения и выберите один или несколько тестов для выполнения в своем решении.

Хотя рабочий процесс прохождения теста можно использовать для запуска любого теста, предоставляемого VaaS, включая тесты из рабочих процессов проверки, результаты такого рабочего процесса не считаются *официальными*. Сведения об официальных рабочих процессах проверки см. в разделе [Рабочие процессы](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к этому краткому руководству, ознакомьтесь со следующими разделами:

- [Настройка ресурсов службы VaaS](azure-stack-vaas-set-up-resources.md)
- [Развертывание локального агента](azure-stack-vaas-local-agent.md) (обязательно)
- [Проверка как услуга: основные понятия](azure-stack-vaas-key-concepts.md) (обязательно)

## <a name="start-a-workflow"></a>Запуск рабочего процесса

![Вход на портал VaaS](media/vaas_portalsignin.png)

Войдите на портал, выберите решение или создайте и выберите решение.

1. Войдите на [портал VaaS](https://azurestackvalidation.com).
2. Введите имя существующего решения или выберите **Создать решение**, чтобы создать новое решение. Инструкции см. в разделе [Создание решения на портале VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Нажмите **Запустить** на плитке **Прохождение теста**.

## <a name="specify-parameters"></a>Указание параметров

![Альтернативный текст](media/vaas_test_pass_parameters.png)

Укажите параметры, которые применяются ко всем тестам в рамках рабочего процесса.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Выберите **Далее**, чтобы выбрать тесты для планирования.

## <a name="select-tests-to-run"></a>Выбор тестов для выполнения

Выполнение выбранных тестов планируется после создания рабочего процесса.

1. Выберите тесты, которые будут выполняться в рабочем процессе.

    Если вы хотите переопределить общие параметры (то есть параметры, предоставленные в предыдущем разделе) для любого теста, нажмите на ссылку **Изменить** и укажите новые значения.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Нажмите **Далее** для просмотра рабочего процесса.

## <a name="review-and-submit"></a>Просмотр и отправка

Завершите создание рабочего процесса.

1. Просмотрите отображаемые сведения.

    Служба создает рабочий процесс на основе указанных данных, выбранные тесты будут запланированы.

    Если что-то выглядит неправильно, нажмите кнопку **Назад**, чтобы вернуться в предыдущий раздел.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)
