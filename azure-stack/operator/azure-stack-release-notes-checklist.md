---
title: Контрольный список действий по обновлению Azure Stack | Документация Майкрософт
description: Контрольный список для подготовки системы к установке последнего обновления Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 08/22/2019
ms.openlocfilehash: f0edd9dfd615b046ee4bad7af622855bb2bd2ca2
ms.sourcegitcommit: f1a21af6517978ddb62f4cbfa1d1df8c867814d1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70064151"
---
# <a name="azure-stack-update-activity-checklist"></a>Контрольный список действий по обновлению Azure Stack

*Область применения: интегрированные системы Azure Stack*

Изучите этот контрольный список, чтобы подготовить среду к установке обновления Azure Stack. Эта статья содержит контрольный список действий, связанных с обновлением, для операторов Azure Stack.

## <a name="prepare-for-azure-stack-update"></a>Подготовка к обновлению Azure Stack

| Действие | Сведения |
| --- | --- |
| Просмотр известных проблем |[Список известных проблем](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1906). |
| Просмотр обновлений для системы безопасности | [Список обновлений для системы безопасности](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1906). |
| Применение последней версии пакета OEM | Обратитесь к изготовителю оборудования, чтобы убедиться, что ваша система соответствует минимальным требованиям для установки пакета OEM, предназначенного для версии Azure Stack, до которой обновляется система. |
| Выполнение Test-AzureStack | Выполните команду `Test-AzureStack -Group UpdateReadiness`, чтобы выявить проблемы с работоспособностью. |
| Решение проблем | Устраните операционные проблемы, выявленные с помощью командлета `Test-AzureStack`. |
| Применение последних исправлений | Примените последние исправления к текущему установленному выпуску. |
| Запуск планировщика ресурсов | Обязательно используйте последнюю версию [планировщика ресурсов Azure Stack](azure-stack-capacity-planning-overview.md) для планирования рабочей нагрузки и определения необходимых ресурсов. В последней версии исправлены известные ошибки и реализованы новые функции, что делается при каждом обновлении Azure Stack. |
| Доступность обновления | Только подключенные сценарии: развертывания Azure Stack будут периодически проверять защищенную конечную точку и автоматически сообщать, доступно ли обновление для вашего облака. Отключенные клиенты могут скачать и импортировать новые пакеты, выполнив инструкции, описанные [здесь](https://docs.microsoft.com/azure-stack/operator/azure-stack-apply-updates). |


## <a name="during-azure-stack-update"></a>Действия при обновлении Azure Stack

| Действие | Сведения |
|--------------------|------------------------------------------------------------------------------------------------------|
| Управление обновлением |[Управляйте обновлениями в Azure Stack с помощью портала для операторов](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates). |
| Мониторинг обновления | Если портал для операторов недоступен, [отслеживайте обновления в Azure Stack с помощью привилегированной конечной точки](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-update). |
| Возобновление обновления | Устранив ошибки, из-за которых обновление завершилось сбоем, [возобновите обновления в Azure Stack с помощью привилегированной конечной точки](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-update). |

> [!Important]  
> Не выполняйте команду **Test-AzureStack** во время обновления, чтобы не остановить процесс.

## <a name="after-azure-stack-update"></a>Действия после обновления Azure Stack

| Действие | Сведения |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Применение последних исправлений | Примените последние исправления к обновленной версии. |
| Получение ключей шифрования | Извлеките ключи шифрования неактивных данных и надежно сохраните их за пределами развертывания Azure Stack. Следуйте указаниям [о том, как извлечь ключи](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker). |
| Повторное включение мультитенантности | В случае, если вы используете мультитенантную среду Azure Stack, убедитесь, что после успешного обновления [настроены все клиенты гостевых каталогов](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory). |

## <a name="next-steps"></a>Дополнительная информация

-   [Ознакомьтесь со списком известных проблем](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1907)  
-   [Ознакомьтесь со списком обновлений для системы безопасности](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1907)