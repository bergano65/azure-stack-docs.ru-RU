---
title: Создание учетной записи хранения в Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Узнайте, как создать учетную запись хранения в Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: bc638be874518bec2e7b8a6db9935f93aa33e30f
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509693"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Создание учетной записи хранения в Azure Stack Hub

Учетные записи хранения в Azure Stack Hub включают службы больших двоичных объектов и таблиц, а также уникальное пространство имен для объектов данных хранилища. По умолчанию данные в учетной записи доступны только владельцу учетной записи хранения.

1. На компьютере подтверждения концепции Azure Stack Hub войдите в `https://adminportal.local.azurestack.external` в качестве [администратора](../asdk/asdk-connect.md), а затем щелкните **+ Create a resource** (+ Создать ресурс)  > **Data + Storage** (Данные и хранилище)  > **Учетная запись хранения**.

   ![Создание учетной записи хранения на портале администрирования Azure Stack Hub](media/azure-stack-provision-storage-account/image01.png)

2. В колонке **Создание учетной записи хранения** введите имя учетной записи хранения. Создайте **группу ресурсов** или выберите имеющуюся, а затем щелкните **Создать** для создания учетной записи хранения.

   ![Проверка учетной записи хранения на портале администрирования Azure Stack Hub](media/azure-stack-provision-storage-account/image02.png)

3. Чтобы просмотреть новую учетную запись хранения, щелкните **Все ресурсы**, а затем найдите учетную запись хранения и щелкните ее имя.

    ![Имя учетной записи хранения на портале администрирования Azure Stack Hub](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Дальнейшие действия

- [Использование шаблонов Azure Resource Manager](../user/azure-stack-arm-templates.md)
- [Узнайте об учетных записях хранения Azure](/azure/storage/common/storage-create-storage-account)
- [Скачать руководство по проверке согласованного с Azure хранилища Azure Stack Hub](https://aka.ms/azurestacktp1doc)
