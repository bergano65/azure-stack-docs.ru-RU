---
title: Развертывание шаблонов в Azure Stack с помощью Visual Studio Hub | Документация Майкрософт
description: Узнайте, как развертывать шаблоны в Azure Stack Hub с помощью Visual Studio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 86cd483b2416dc613ac74cdca77e7bf3f94b1fe6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536289"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Развертывание шаблонов в Azure Stack Hub с помощью Visual Studio

С помощью Visual Studio можно развертывать шаблоны Azure Resource Manager в Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Развертывание шаблона

1. [Выполните установку и подключение](azure-stack-install-visual-studio.md) к Azure Stack Hub с помощью Visual Studio.
2. Запустите Visual Studio.
3. Выберите **Файл**, а затем выберите **Создать**. В диалоговом окне **Новый проект** выберите **Группа ресурсов Azure**.
4. Введите **имя** нового проекта и нажмите кнопку **ОК**.
5. В диалоговом окне **Выберите шаблон Azure** из раскрывающегося списка выберите **Azure Stack Hub Quickstart** (Краткое руководство по Azure Stack Hub).
6. Выберите **101-create-storage-account**, а затем нажмите кнопку **ОК**.
7. В новом проекте разверните узел **Шаблоны** в **обозревателе решений**, чтобы просмотреть доступные шаблоны.
8. В **обозревателе решений** выберите имя своего проекта, а затем щелкните **Развернуть**. Выберите **Новое развертывание**.
9. В диалоговом окне **Развернуть в группе ресурсов** из раскрывающегося списка **Подписка** выберите свою подписку Microsoft Azure Stack Hub.
10. Выберите существующую группу ресурсов из раскрывающегося списка **Группа ресурсов** или создайте новую.
11. Из списка **Расположение группы ресурсов** выберите расположение, затем нажмите кнопку **Развернуть**.
12. В диалоговом окне **Изменить параметры** введите значения параметров (которые зависят от шаблона), а затем нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание шаблонов с помощью командной строки](azure-stack-deploy-template-command-line.md)
* [Разработка шаблонов для Azure Stack Hub](azure-stack-develop-templates.md)
