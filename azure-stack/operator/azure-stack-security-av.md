---
title: Обновление антивирусной программы "Защитник Windows" в Azure Stack
description: Подробные сведения о поддержке актуальности антивируса в Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 6e9c4a5334ce44da777126f09ca0ecf578ccfcbd
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828108"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Обновление антивирусной программы "Защитник Windows" в Azure Stack

[Антивирусная программа "Защитник Windows"](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) — это решение для защиты от вредоносных программ, которое обеспечивает безопасность и защиту от вирусов. Каждый компонент инфраструктуры в Azure Stack (узлы Hyper-V и виртуальные машины) защищен с помощью антивирусной программы "Защитник Windows". Для актуальной защиты требуется периодически обновлять определения, модуль и платформу антивирусной программы "Защитник Windows". Применение обновлений зависит от конфигурации.

## <a name="connected-scenario"></a>Сценарий с подключением

[Поставщик ресурсов "Обновление"](azure-stack-updates.md#the-update-resource-provider) в Azure Stack загружает обновления определений и модуля защиты от вредоносных программ несколько раз в день. Каждый компонент инфраструктуры в Azure Stack получает обновление из поставщика ресурсов "Обновление" и применяет его автоматически.

Чтобы обновить платформу защиты от вредоносных программ, примените [ежемесячное обновление Azure Stack](azure-stack-apply-updates.md). Ежемесячное обновление Azure Stack содержит обновления платформы антивирусной программы "Защитник Windows" за текущий месяц.

## <a name="disconnected-scenario"></a>Сценарии без подключения

 Примените [ежемесячное обновление Azure Stack](azure-stack-apply-updates.md). Ежемесячное обновление Azure Stack содержит обновления определений, модуля защиты и платформы антивирусной программы "Защитник Windows" за текущий месяц.

## <a name="next-steps"></a>Дополнительная информация

[Система безопасности для инфраструктуры Azure Stack](azure-stack-security-foundations.md)
