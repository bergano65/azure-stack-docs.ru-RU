---
title: Развертывание виртуальной машины Azure Stack Hub с помощью пароля, хранящегося в Key Vault | Документация Майкрософт
description: Узнайте, как развернуть виртуальную машину с помощью пароля, хранящегося в хранилище ключей Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 866862f237490a9d59211ed6a87fc1cff2fe7c11
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536153"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>Развертывание виртуальной машины Azure Stack Hub с помощью пароля, хранящегося в Key Vault

В этой статье описано, как развернуть виртуальную машину Windows Server с помощью пароля, хранящегося в Key Vault в Azure Stack Hub. Использовать пароль из Key Vault безопаснее, чем передавать незашифрованный пароль.

## <a name="overview"></a>Обзор

Такие значения, как пароль, можно хранить в Key Vault Azure Stack Hub в виде секрета. После создания секрета на него можно ссылаться в шаблонах Azure Resource Manager. Использование секретов в Resource Manager обеспечивает следующие преимущества:

* не нужно вручную вводить секрет при каждом развертывании ресурса;
* можно указать, какие пользователи или субъекты-службы могут получить доступ к секрету.

## <a name="prerequisites"></a>Предварительные требования

* Необходимо подписаться на предложение, включающее службу Key Vault.
* [Установка PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md).
* [Настройте среду PowerShell.](azure-stack-powershell-configure-user.md)

Ниже описано, как создать виртуальную машину путем извлечения пароля, хранящегося в Key Vault:

1. Создание секрета хранилища ключей.
2. Обновите файл `azuredeploy.parameters.json`.
3. Разверните шаблон.

> [!NOTE]  
> Эти шаги можно выполнить из Пакета средств разработки Azure Stack (ASDK) или из внешнего клиента при подключении через VPN.

## <a name="create-a-key-vault-secret"></a>Создание секрета хранилища ключей

Следующий скрипт создает хранилище ключей и сохраняет в нем пароль в виде секрета. Используйте параметр `-EnabledForDeployment` при создании хранилища ключей. Благодаря этому параметру на хранилище ключей можно ссылаться из шаблонов Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Выполнение предыдущего скрипта возвращает URI секрета. Запишите его. Вам нужно связать его с шаблоном [Развертывание виртуальной машины Windows с помощью пароля, хранящегося в хранилище ключей](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Скачайте папку [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) на компьютер разработчика. Эта папка содержит файлы `azuredeploy.json` и `azuredeploy.parameters.json`, которые понадобятся далее.

Измените файл `azuredeploy.parameters.json`, указав значения для своей среды. Особый интерес представляют параметры имени хранилища, группы ресурсов хранилища и URI секрета (сгенерированный предыдущим скриптом). Ниже приведен пример файла параметров:

## <a name="update-the-azuredeployparametersjson-file"></a>Обновление файла azuredeploy.parameters.json

В файле `azuredeploy.parameters.json` укажите значения URI KeyVault, secretName, adminUsername для виртуальной машины с учетом среды. Ниже приведен пример JSON-файла параметров шаблона:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
       "adminPassword":  {
         "reference":  {
            "keyVault":  {
              "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
              },
            "secretName":  "MySecret"
         }
       },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Развертывание шаблона

Теперь разверните шаблон с помощью следующего скрипта PowerShell:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

При успешном развертывании шаблона выводятся следующие выходные данные:

![Выходные данные развертывания](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Sample application that uses keys and secrets stored in a key vault](azure-stack-key-vault-sample-app.md) (Пример приложения, использующего ключи и секреты из хранилища ключей)
* [Create a virtual machine and include certificate retrieved from a key vault](azure-stack-key-vault-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)
