---
title: Предложение служб в Azure Stack | Документация Майкрософт
description: Как оператор облака вы можете предлагать своим пользователям службы.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: a85f7398e492a2d49c2f6c1b686f2e15042b0444
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268369"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Общие сведения о предложении служб в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

[Microsoft Azure Stack](azure-stack-overview.md) ― это гибридная облачная платформа, позволяющая доставлять службы из центра обработки данных. Как поставщик услуг вы можете предлагать службы своим клиентам. В коммерческой компании или государственном учреждении можно предоставлять локальные службы сотрудникам. 

Вы можете предлагать службы в формате [инфраструктура как услуга](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS), которые позволяют пользователям создавать по вычислительную инфраструктуру по запросу, подготавливая ее и управляя ею с пользовательского портала Azure Stack.

Вы можете также развертывать из Azure Stack службы в формате [платформа как услуга](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) корпорации Майкрософт и других сторонних поставщиков. К службам, которые можно доставлять, помимо прочего, относятся следующие:

- [Добавление поставщика ресурсов службы приложений в Azure Stack](azure-stack-app-service-overview.md)

- [Use SQL databases on Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) (Использование баз данных SQL в Microsoft Azure Stack)

- [Use MySQL databases on Microsoft Azure Stack](azure-stack-mysql-resource-provider-deploy.md) (Использование баз данных MySQL в Microsoft Azure Stack)


Можно даже комбинировать службы для интеграции и создания сложных решений для различных пользователей.

Для доставки этих служб пользователям вам сперва необходимо создать [планы, предложения и квоты](azure-stack-plan-offer-quota-overview.md). Затем пользователи смогут подписываться на ваши предложения и использовать службы.

## <a name="plan-your-service-offers"></a>Планирование предложений служб

При планировании предложений необходимо учитывать следующие моменты:

**Предложения пробного использования**. С помощью пробных предложений можно привлечь новых пользователей, которые затем перейдут на полноценное использование дополнительных служб. Чтобы создать пробное предложение, создайте небольшой [базовый план](azure-stack-plan-offer-quota-overview.md#base-plan) с необязательным дополнительным планом более крупного масштаба.

**Планирование ресурсов**. У вас может возникнуть проблема с пользователями, которые задействуют большие объемы ресурсов и затрудняют работу системы для остальных пользователей. Для улучшения производительности можно [настроить планы с квотами](azure-stack-plan-offer-quota-overview.md#plans) для ограничения нагрузки.

**Делегированные поставщики**. Вы можете предоставить другим возможность создавать предложения в вашей среде. Например, если вы поставщик услуг, вы можете [делегировать](azure-stack-delegated-provider.md) эту возможность торговым посредникам. Или, если вы представляете организацию, вы можете делегировать полномочия другим подразделениям и филиалам.

## <a name="next-steps"></a>Дополнительная информация

[Создание предложения в Azure Stack](azure-stack-create-offer.md)
