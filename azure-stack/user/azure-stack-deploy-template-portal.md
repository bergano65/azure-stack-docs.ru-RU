---
title: Развертывание шаблонов в Azure Stack Hub с помощью портала
description: Из этой статьи вы узнаете, как использовать портал Azure Stack Hub для развертывания шаблонов.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8425b4f8492e57502ca93b0be802b63601f6ba34
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704324"
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
