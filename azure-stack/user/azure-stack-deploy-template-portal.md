---
title: Развертывание шаблонов в Azure Stack Hub с помощью портала | Документация Майкрософт
description: Из этой статьи вы узнаете, как использовать портал Azure Stack Hub для развертывания шаблонов.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 6feca120c01704aaf999899c660c64ecfecd3d97
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536391"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Развертывание шаблонов в Azure Stack Hub с помощью портала

С помощью портала можно развертывать шаблоны Azure Resource Manager в Azure Stack Hub.

## <a name="to-deploy-a-template"></a>Развертывание шаблона

1. Войдите на портал, выберите команду **+ Создать ресурс** и выберите **Настраиваемый**.

   ![Создание ресурса на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Выберите **Развертывание шаблона**.

   ![Развертывание шаблона на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Щелкните **Изменить шаблон** и вставьте код JSON шаблона в окно кода. Щелкните **Сохранить**.

   ![Изменение шаблона на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Выберите **Изменить параметры**, укажите значения параметров, которые отображаются, а затем нажмите кнопку **ОК**.

   ![Изменение параметров на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Выберите **Подписка**. Выберите подписку, которую нужно использовать, и нажмите кнопку **ОК**.

   ![Выбор подписки на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Выберите **Группа ресурсов**. Выберите имеющуюся группу ресурсов или создайте новую. Затем нажмите кнопку **ОК**.

   ![Выбор группы ресурсов на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Нажмите кнопку **создания**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

   ![Создание шаблона на портале Azure Stack Hub](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о развертывании шаблонов см. в статье

- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
