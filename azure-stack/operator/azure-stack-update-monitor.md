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
ms.openlocfilehash: f14b4180e567df13e576b520cdac01a676c101ea
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717949"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Мониторинг обновлений в Azure Stack Hub с помощью PowerShell

*Область применения: интегрированные системы Azure Stack Hub*

Для наблюдения за обновлениями и управления ими можно использовать конечные точки администрирования Azure Stack Hub. Они доступны посредством PowerShell. Инструкции по настройке PowerShell для Azure Stack Hub см. в статье [Install PowerShell for Azure Stack Hub](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack Hub).

Для управления обновлениями можно использовать следующий командлет PowerShell.

| Командлет | Description |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Получение списка доступных обновлений. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Получение списка расположений обновлений. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Получение списка выполнений обновлений.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Применение конкретного обновления из расположения обновлений. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Возобновление запущенного ранее обновления, которое завершилось сбоем. |

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

## <a name="next-steps"></a>Дальнейшие действия

-   [Общие сведения об управлении обновлениями в Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
