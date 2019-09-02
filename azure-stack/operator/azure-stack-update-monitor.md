---
title: Мониторинг обновлений в Azure Stack с помощью PowerShell | Документация Майкрософт
description: Узнайте, как отслеживать обновления в Azure Stack с помощью PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: a49f2f5c6b06f3362e4cb85553b765ea9fea1a72
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010331"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>Мониторинг обновлений в Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack*

Для наблюдения за обновлениями и управления ими можно использовать конечные точки администрирования Azure Stack. Они доступны посредством PowerShell. Инструкции по настройке PowerShell для Azure Stack см. в статье [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack).

Для управления обновлениями можно использовать следующий командлет PowerShell.

| Командлет | ОПИСАНИЕ |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.7.2) | Получение списка доступных обновлений. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.7.2)| Получение списка расположений обновлений. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.7.2) | Получение списка выполнений обновлений.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.7.2) | Применение конкретного обновления из расположения обновлений. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.7.2) | Возобновление запущенного ранее обновления, которое завершилось сбоем. |

## <a name="get-a-list-of-update-runs"></a>Получение списка выполнений обновлений

Чтобы получить список выполнений обновлений, выполните следующую команду.

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Возобновление операции в случае сбоя

В случае сбоя обновления можно возобновить обновление с точки, где оно остановилось, выполнив следующую команду.

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Дополнительная информация

-   [Общие сведения об управлении обновлениями в Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)