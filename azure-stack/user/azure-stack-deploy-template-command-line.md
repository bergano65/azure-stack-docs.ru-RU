---
title: Развертывание шаблона в Azure Stack с помощью командной строки | Документация Майкрософт
description: Узнайте, как использовать кроссплатформенный интерфейс командной строки (CLI) для развертывания шаблонов в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691371"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Развертывание шаблона в Azure Stack с помощью командной строки

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью интерфейса командной строки Azure можно развертывать шаблоны Azure Resource Manager в Azure Stack. Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции.

## <a name="before-you-begin"></a>Перед началом работы

- [Выполните установку и подключение](azure-stack-version-profiles-azurecli2.md) к Azure Stack с помощью Azure CLI.
- Скачайте файлы *azuredeploy.json* и *azuredeploy.parameters.json* из [примера шаблона создания учетной записи хранения](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Развертывание шаблона

Перейдите к папке, куда скачаны эти файлы, и выполните следующую команду для развертывания шаблона:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Эта команда развертывает шаблон в группу ресурсов **cliRG** в стандартном расположении подтверждения концепции Azure Stack.

## <a name="validate-template-deployment"></a>Проверка развертывания шаблона

Чтобы просмотреть эту группу ресурсов и учетную запись хранения, используйте следующие команды CLI.

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о [развертывании шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md).
