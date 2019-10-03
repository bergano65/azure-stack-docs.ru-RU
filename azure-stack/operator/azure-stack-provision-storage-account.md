---
title: Учетные записи хранения в Azure Stack | Документация Майкрософт
description: Узнайте, как создать учетную запись хранения Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 60336477f1dec9618fd6cc439e9d2f5d098b3399
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829387"
---
# <a name="storage-accounts-in-azure-stack"></a>Учетные записи хранения в Azure Stack

Учетные записи хранения в Azure Stack включают службы больших двоичных объектов и таблиц, а также уникальное пространство имен для объектов данных хранилища. По умолчанию данные в учетной записи доступны только владельцу учетной записи хранения.

1. На компьютере подтверждения концепции Azure Stack войдите в `https://adminportal.local.azurestack.external` в качестве [администратора](../asdk/asdk-connect.md), а затем щелкните **+ Create a resource** (+ Создать ресурс)  > **Данные+хранилище** > **Учетная запись хранения**.

   ![Создание учетной записи хранения](media/azure-stack-provision-storage-account/image01.png)
2. В колонке **Создание учетной записи хранения** введите имя учетной записи хранения. Создайте **группу ресурсов** или выберите имеющуюся, а затем щелкните **Создать** для создания учетной записи хранения.

   ![Просмотр учетной записи хранения](media/azure-stack-provision-storage-account/image02.png)
3. Чтобы просмотреть новую учетную запись хранения, щелкните **Все ресурсы**, а затем найдите учетную запись хранения и щелкните ее имя.

    ![Имя учетной записи хранения](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Дополнительная информация
[Использование шаблонов диспетчера ресурсов Azure](../user/azure-stack-arm-templates.md)

[Узнайте об учетных записях хранения Azure](/azure/storage/common/storage-create-storage-account)

[Скачайте руководство по проверке согласованного с Azure хранилища Azure Stack](https://aka.ms/azurestacktp1doc)
