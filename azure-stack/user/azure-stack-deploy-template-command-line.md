---
title: Развертывание шаблона в Azure Stack Hub с помощью командной строки | Документация Майкрософт
description: Узнайте, как использовать кроссплатформенный интерфейс командной строки Azure для развертывания шаблонов в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 25479837bd73816abae92badfcd03bb21317f84c
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536357"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Развертывание шаблона в Azure Stack Hub с помощью командной строки

С помощью интерфейса командной строки Azure (Azure CLI) можно развертывать шаблоны Azure Resource Manager в Azure Stack Hub. Используя шаблоны Azure Resource Manager, можно развернуть и настроить все ресурсы для приложения в рамках одного скоординированного действия.

## <a name="deploy-template"></a>Развертывание шаблона

1. Перейдите в репозиторий [AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) и найдите шаблон **101-create-storage-account**. Сохраните шаблон (`azuredeploy.json`) и файлы параметров `(azuredeploy.parameters.json`) в расположение на локальном диске, например `C:\templates\`.
2. Перейдите в папку со скачанными файлами. 
3. [Выполните установку и подключение](azure-stack-version-profiles-azurecli2.md) к Azure Stack Hub с помощью Azure CLI.
4. Замените значения региона и расположения в приведенной ниже команде. Если используется ASDK, укажите `local` для параметра location. Чтобы развернуть шаблон, выполните следующую команду:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Эта команда развертывает шаблон в группу ресурсов **testDeploy** в экземпляре Azure Stack Hub.

## <a name="validate-template-deployment"></a>Проверка развертывания шаблона

Чтобы просмотреть эту группу ресурсов и учетную запись хранения, выполните следующие команды CLI.

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [развертывать шаблоны с помощью PowerShell](azure-stack-deploy-template-powershell.md).
