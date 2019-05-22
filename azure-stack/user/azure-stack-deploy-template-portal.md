---
title: Развертывание шаблонов в Azure Stack с помощью портала | Документация Майкрософт
description: Узнайте, как использовать портал Azure Stack для развертывания шаблонов.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 6e848af9049c7f105e6a31fdef8dc03308bd276a
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212457"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Развертывание шаблонов с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью портала можно развертывать шаблоны Azure Resource Manager в Azure Stack.

## <a name="to-deploy-a-template"></a>Развертывание шаблона

1. Войдите на портал, выберите команду **+ Создать ресурс** и выберите **Настраиваемый**.

   ![Создание](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Выберите **Развертывание шаблона**.

   ![Развертывание шаблона](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Щелкните **Изменить шаблон** и вставьте код JSON шаблона в окно кода. Щелкните **Сохранить**.

   ![Изменить шаблон](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Выберите **Изменить параметры**, укажите значения параметров, которые отображаются, а затем нажмите кнопку **ОК**.

   ![Изменение параметров](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Выберите **Подписка**. Выберите подписку, которую нужно использовать, и нажмите кнопку **ОК**.

   ![Подписка](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Выберите **Группа ресурсов**. Выберите имеющуюся группу ресурсов или создайте новую. Затем нажмите кнопку **ОК**.

   ![Группа ресурсов](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Нажмите кнопку **Создать**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

   ![Создание шаблона](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о развертывании шаблонов см. в статье

- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
