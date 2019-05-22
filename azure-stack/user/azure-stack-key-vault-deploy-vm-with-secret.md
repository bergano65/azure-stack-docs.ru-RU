---
title: Развертывание виртуальной машины с паролем, безопасно хранящимся в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть виртуальную машину с использованием пароля, хранящегося в хранилище ключей Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2c775c982cf72d687d04b32db742fdbcd8e38fb9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986145"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Создание виртуальной машины с помощью безопасного пароля, хранящегося в Key Vault Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье приводятся пошаговые инструкции по развертыванию виртуальной машины Windows Server с помощью пароля, хранящегося в Key Vault Azure Stack. Использовать пароль из Key Vault безопаснее, чем передавать незашифрованный пароль.

## <a name="overview"></a>Обзор

Такие значения, как пароль, можно хранить в Key Vault Azure Stack в виде секрета. После создания секрета на него можно ссылаться в шаблонах Azure Resource Manager. Использование секретов в Resource Manager обеспечивает следующие преимущества:

* не нужно вручную вводить секрет при каждом развертывании ресурса;
* можно указать, какие пользователи или субъекты-службы могут получить доступ к секрету.

## <a name="prerequisites"></a>Предварительные требования

* Необходимо подписаться на предложение, включающее службу Key Vault.
* [Установите PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md).
* [Настройте среду PowerShell.](azure-stack-powershell-configure-user.md)

Следующие шаги описывают процесс, необходимый для создания виртуальной машины путем извлечения пароля, хранящегося в хранилище ключей:

1. Создайте секрет хранилища ключей.
2. Обновление файла azuredeploy.parameters.json соответствующим образом.
3. Разверните шаблон.

> [!NOTE]  
> Эти шаги можно выполнить из Пакета средств разработки Azure Stack или из внешнего клиента при подключении через VPN.

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

В результате выполнения предыдущего скрипта был выведен URI секрета. Запишите его. На него необходимо указать ссылку при [развертывании виртуальной машины Windows с паролем, хранящимся в шаблоне хранилища ключей](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Скачайте папку [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) на компьютер разработчика. Эта папка содержит файлы `azuredeploy.json` и `azuredeploy.parameters.json`, которые необходимы на следующих шагах.

Измените файл `azuredeploy.parameters.json`, указав значения для своей среды. Особый интерес представляют параметры имени хранилища, группы ресурсов хранилища и URI секрета (сгенерированный предыдущим скриптом). Ниже приведен пример файла параметров:

## <a name="update-the-azuredeployparametersjson-file"></a>Обновление файла azuredeploy.parameters.json

В файле azuredeploy.parameters.json укажите значения универсального кода ресурса (URI) KeyVault, secretName, adminUsername виртуальной машины в соответствии со средой. Ниже приведен пример JSON-файла параметров шаблона:

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

## <a name="next-steps"></a>Дополнительная информация

* [Sample application that uses keys and secrets stored in a key vault](azure-stack-key-vault-sample-app.md) (Пример приложения, использующего ключи и секреты из хранилища ключей)
* [Create a virtual machine and include certificate retrieved from a key vault](azure-stack-key-vault-push-secret-into-vm.md) (Создание виртуальной машины с сертификатом хранилища ключей)
