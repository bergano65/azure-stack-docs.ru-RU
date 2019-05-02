---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: e9d59fadb9cbfeb7463f799bdab2e6da49208fe9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447780"
---
В рабочих процессах проверки при **планировании** теста используются общие параметры уровня рабочего процесса, которые вы указали во время его создания (см. раздел [Workflow common parameters for Azure Stack Validation as a Service](../azure-stack-vaas-parameters.md) (Общие параметры рабочих процессов для проверки как услуги в Azure Stack)). Если какие-либо из значений параметров станут недействительными, необходимо указать их повторно, как описано в разделе об [изменении параметров рабочего процесса](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> При планировании проверочного теста через существующий экземпляр на портале будет создан новый экземпляр вместо старого. Журналы для старого экземпляра будут сохранены, но недоступны на портале.  
После успешного завершения теста действие **Расписание** становится недоступным.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. В контекстном меню выберите **Расписание**, чтобы открыть командную строку для планирования выполнения тестового экземпляра.

1. Проверьте параметры теста, а затем выберите **Отправить**, чтобы запланировать выполнение теста.