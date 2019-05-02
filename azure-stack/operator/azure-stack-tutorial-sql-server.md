---
title: Обеспечение доступности баз данных SQL для пользователей Azure Stack | Документация Майкрософт
description: Учебное пособие по установке поставщика ресурсов SQL Server и созданию предложений, позволяющих пользователям Azure Stack создавать базы данных SQL.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
origin.date: 02/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: 26ab5b0528f54a351c7168269cb1cfb4b1e13f9e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447390"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Руководство. Обеспечение доступности баз данных SQL для пользователей Azure Stack

В качестве администратора облака Azure Stack вы можете создавать предложения, позволяющие пользователям (клиентам) создавать базы данных SQL, которые они смогут использовать с собственными облачными приложениями, веб-сайтами и рабочими нагрузками. Предоставляя пользователям такие настраиваемые облачные базы данных по запросу, вы помогаете им экономить время и ресурсы. Для этого вам потребуется выполнить следующие действия:

> [!div class="checklist"]
> * Развертывание поставщика ресурсов SQL Server.
> * Создание предложения
> * Тестирование предложения.

## <a name="deploy-the-sql-server-resource-provider"></a>Развертывание поставщика ресурсов SQL Server.

Процесс развертывания подробно описан в статье [Использование баз данных SQL в Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) и состоит из следующих основных шагов:

1. [Развертывание поставщика ресурсов SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Проверка развертывания](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Указание емкости путем подключения к серверу размещения SQL. Дополнительные сведения см. в статье [Добавление серверов размещения для поставщика ресурсов SQL](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Создание предложения

1.  [Задайте квоту](azure-stack-plan-offer-quota-overview.md ) и присвойте ей имя *SQLServerQuota*. Выберите **Microsoft.SQLAdapter** в поле **Пространство имен**.
2.  [Создайте план](azure-stack-create-plan.md). Присвойте ему имя *TestSQLServerPlan*, выберите службу **Microsoft.SQLAdapter** и квоту **SQLServerQuota**.

    > [!NOTE]
    > Чтобы позволить пользователям создавать другие приложения, в плане могут потребоваться другие службы. Например, для службы "Функции Azure" необходимо, чтобы план содержал службу **Microsoft.Storage**, а для Wordpress — **Microsoft.MySQLAdapter**.

3.  [Создайте предложение](azure-stack-create-offer.md), назовите его **TestSQLServerOffer** и выберите план **TestSQLServerPlan**.

## <a name="test-the-offer"></a>Тестирование предложения

Теперь, когда поставщик ресурсов SQL Server развернут и приложение создано, можно войти как пользователь, подписаться на предложение и создать базу данных.

### <a name="subscribe-to-the-offer"></a>Оформление подписки на предложение

1. Войдите на портал Azure Stack (https://portal.local.azurestack.external) как клиент.
2. Выберите **Получить подписку**, а затем введите **TestSQLServerSubscription** в поле **Отображаемое имя**.
3. Выберите пункт **Выбрать предложение** > **TestSQLServerOffer** > **Создать**.
4. Выберите **Все службы** > **Подписки** > **TestSQLServerSubscription** > **Поставщики ресурсов**.
5. Выберите **Зарегистрировать** рядом с поставщиком **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Создание базы данных SQL

1. Выберите **+** > **Данные+хранилище** > **База данных SQL**.
2. Сохраните значения по умолчанию или используйте эти примеры для следующих полей.
    - **Имя базы данных**: SQLdb
    - **Максимальный размер (в МБ)**: 100
    - **Подписка**: TestSQLOffer
    - **Группа ресурсов**. SQL-RG
3. Выберите **Login Settings** (Параметры входа), введите учетные данные для базы данных и нажмите кнопку **ОК**.
4. Последовательно выберите **SKU** > SQL SKU, созданный для сервера размещения SQL, > а затем нажмите кнопку **OK**.
5. Нажмите кнопку **Создать**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Развертывание поставщика ресурсов SQL Server.
> * Создание предложения
> * Тестирование предложения.

Перейдите к следующему руководству, чтобы изучить дальнейшие действия:

> [!div class="nextstepaction"]
> [Обеспечение доступности веб-приложений, мобильных приложений и приложений API для пользователей Azure Stack]( azure-stack-tutorial-app-service.md)

<!-- Update_Description: wording update -->