---
title: Планирование тестов на портале проверки Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Сведения о планировании тестов на портале проверки Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e41fe02946b3f08d34cdb0a6d81c08885ef9d71b
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704613"
---
# <a name="scheduling-a-test"></a>Планирование теста

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Запланируйте тест на портале проверки Microsoft Azure Stack для решения Azure Stack Hub. Решение "проверка как услуга" (VaaS) — это решение Azure Stack Hub с определенными спецификациями оборудования (BoM). Вы можете запланировать тест, чтобы убедиться, что на оборудовании можно запустить Azure Stack Hub.

Чтобы проверить решение, создайте рабочий процесс для теста. Рабочий процесс VaaS работает в контексте решения VaaS. Он представляет набор тестов, проверяющих функциональность развертывания Azure Stack Hub на вашем оборудовании. Добавьте параметры среды решения и выберите один или несколько тестов для выполнения в своем решении.

Хотя рабочий процесс прохождения теста можно использовать для запуска любого теста, предоставляемого VaaS, включая тесты из рабочих процессов проверки, результаты такого рабочего процесса не считаются *официальными*. Сведения об официальных рабочих процессах проверки см. в разделе [Рабочие процессы](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к этому краткому руководству, выполните приведенные ниже задачи:

- [Настройка ресурсов Azure AD и хранилища для службы "Проверка как услуга"](azure-stack-vaas-set-up-resources.md)
- [Развертывание локального агента](azure-stack-vaas-local-agent.md) (обязательно)
- [Проверка как услуга: основные понятия](azure-stack-vaas-key-concepts.md) (обязательно)

## <a name="start-a-workflow"></a>Запуск рабочего процесса

![Вход на портал VaaS](media/vaas_portalsignin.png)

Войдите на портал, выберите решение или создайте и выберите решение.

1. Войдите на [портал VaaS](https://azurestackvalidation.com).
2. Введите имя существующего решения или выберите **Создать решение**, чтобы создать новое решение. Инструкции см. в разделе [Создание решения на портале VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal).
3. Нажмите **Запустить** на плитке **Прохождение теста**.

## <a name="specify-parameters"></a>Указание параметров

![Указание параметров на портале VaaS](media/vaas_test_pass_parameters.png)

Укажите параметры, которые применяются ко всем тестам в рамках рабочего процесса.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Выберите **Далее**, чтобы выбрать тесты для планирования.

## <a name="select-tests-to-run"></a>Выбор тестов для выполнения

Выполнение выбранных тестов планируется после создания рабочего процесса.

1. Выберите тесты, которые будут выполняться в рабочем процессе.

    Если вы хотите переопределить общие параметры (параметры, предоставленные в предыдущем разделе) для любого теста, нажмите на ссылку **Изменить** и укажите новые значения.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Нажмите **Далее** для просмотра рабочего процесса.

## <a name="review-and-submit"></a>Просмотр и отправка

Завершите создание рабочего процесса.

1. Просмотрите отображаемые сведения.

    Служба создает рабочий процесс на основе указанных данных, выбранные тесты будут запланированы.

    Если что-то выглядит неправильно, нажмите кнопку **Назад**, чтобы вернуться в предыдущий раздел.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)
