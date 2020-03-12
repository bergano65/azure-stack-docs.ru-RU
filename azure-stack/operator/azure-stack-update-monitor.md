---
title: Мониторинг обновлений в Azure Stack Hub с помощью PowerShell
description: Узнайте, как отслеживать обновления в Azure Stack Hub с помощью PowerShell.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: c611d901c62765462d5bccaee6894f5f7c0b0b18
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367290"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Мониторинг обновлений в Azure Stack Hub с помощью PowerShell

Для наблюдения за обновлениями и управления ими можно использовать конечные точки администрирования Azure Stack Hub. Они доступны посредством PowerShell. Инструкции по настройке PowerShell для Azure Stack Hub см. в статье [Install PowerShell for Azure Stack Hub](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack Hub).

Для управления обновлениями можно использовать следующий командлет PowerShell.

| Командлет | Описание |
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

-   [Общие сведения об управлении обновлениями в Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
