---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: d3b1a91c89147f0f945efd345f00823b26f0c7b1
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694596"
---
В рабочих процессах проверки при **планировании** теста используются общие параметры уровня рабочего процесса, которые вы указали во время его создания (см. раздел [Workflow common parameters for Azure Stack Validation as a Service](../azure-stack-vaas-parameters.md) (Общие параметры рабочих процессов для проверки как услуги в Azure Stack)). Если какие-либо из значений параметров станут недействительными, необходимо указать их повторно, как описано в разделе об [изменении параметров рабочего процесса](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> При планировании проверочного теста через существующий экземпляр на портале будет создан новый экземпляр вместо старого. Журналы для старого экземпляра будут сохранены, но недоступны на портале.  
После успешного завершения теста действие **Расписание** становится недоступным.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. В контекстном меню выберите **Расписание**, чтобы открыть командную строку для планирования выполнения тестового экземпляра.

1. Проверьте параметры теста, а затем выберите **Отправить**, чтобы запланировать выполнение теста.