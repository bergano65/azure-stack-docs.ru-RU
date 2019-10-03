---
title: Развертывание шаблонов в Azure Stack с помощью портала | Документация Майкрософт
description: Из этой статьи вы узнаете, как использовать портал Azure Stack для развертывания шаблонов.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: bbbbbc5548397f82752a43c7a1aaca7b62151b75
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708998"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Развертывание шаблонов в Azure Stack с помощью портала

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью портала можно развертывать шаблоны Azure Resource Manager в Azure Stack.

## <a name="to-deploy-a-template"></a>Развертывание шаблона

1. Войдите на портал, выберите команду **+ Создать ресурс** и выберите **Настраиваемый**.

   ![Создание ресурса на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Выберите **Развертывание шаблона**.

   ![Развертывание шаблона на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Щелкните **Изменить шаблон** и вставьте код JSON шаблона в окно кода. Щелкните **Сохранить**.

   ![Изменение шаблона на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Выберите **Изменить параметры**, укажите значения параметров, которые отображаются, а затем нажмите кнопку **ОК**.

   ![Изменение параметров на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Выберите **Подписка**. Выберите подписку, которую нужно использовать, и нажмите кнопку **ОК**.

   ![Выбор подписки на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Выберите **Группа ресурсов**. Выберите имеющуюся группу ресурсов или создайте новую. Затем нажмите кнопку **ОК**.

   ![Выбор группы ресурсов на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Нажмите кнопку **Создать**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

   ![Создание шаблона на портале Azure Stack](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о развертывании шаблонов см. в статье

- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
