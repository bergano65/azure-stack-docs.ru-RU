---
title: Создание учетной записи хранения в Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Узнайте, как создать учетную запись хранения в Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 77f0ebf58f20943de39975d913c7019578c73d0e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698170"
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
