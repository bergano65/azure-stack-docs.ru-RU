---
title: Развертывание виртуальной машины с сертификатом, безопасно хранящимся в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть виртуальную машину и отправить в нее сертификат с помощью хранилища ключей в Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 12/27/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 965b9c0cde96e1e29828f37cdddaa9d30c0503a4
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310910"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Создание виртуальной машины и установка сертификатов, полученных из хранилища ключей Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Узнайте, как создать виртуальную машину Azure Stack с установленным сертификатом хранилища ключей.

## <a name="overview"></a>Обзор

Сертификаты используются во многих сценариях, например для аутентификации в Active Directory или шифрования веб-трафика. Вы можете безопасно хранить сертификаты в виде секретов в хранилище ключей Azure Stack. Хранилище ключей Azure Stack обеспечивает следующие преимущества:

* сертификаты не предоставляются в скриптах, журналах командной строки или шаблонах;
* упрощенное управление сертификатами;
* возможность управлять ключами, используемыми для доступа к сертификатам.

### <a name="process-description"></a>Описание процесса

Ниже представлены шаги, необходимые для отправки сертификата на виртуальную машину.

1. Создание секрета хранилища ключей.
2. Обновление файла azuredeploy.parameters.json соответствующим образом.
3. Разверните шаблон.

> [!NOTE]
> Эти шаги можно выполнить из Пакета средств разработки Azure Stack или из внешнего клиента при подключении через VPN.

## <a name="prerequisites"></a>Предварительные требования

* Необходимо подписаться на предложение, включающее службу Key Vault.
* [Установите PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md).
* [Настройка пользовательской среды PowerShell в Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Создание секрета хранилища ключей

Следующий скрипт создает сертификат в формате PFX, создает хранилище ключей и сохраняет в нем сертификат в качестве секрета.

> [!IMPORTANT]
> Используйте параметр `-EnabledForDeployment` при создании хранилища ключей. Благодаря этому параметру на хранилище ключей можно ссылаться из шаблонов Azure Resource Manager.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

В результате выполнения предыдущего скрипта был выведен URI секрета. Запишите его. На него необходимо указать ссылку при [отправке сертификата в шаблон Resource Manager для Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Скачайте папку шаблона [vm-push-certificate-windows template](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) на компьютер разработчика. Эта папка содержит файлы `azuredeploy.json` и `azuredeploy.parameters.json`, которые необходимы на следующих шагах.

Измените файл `azuredeploy.parameters.json`, указав значения для своей среды. Особый интерес представляют параметры имени хранилища, группы ресурсов хранилища и URI секрета (сгенерированный предыдущим скриптом). Ниже приведен пример файла параметров.

## <a name="update-the-azuredeployparametersjson-file"></a>Обновление файла azuredeploy.parameters.json

Измените в файле `azuredeploy.parameters.json` значения для параметров `vaultName`, секрета URI, `VmName` и других параметров на значения для своей среды. Ниже приведен пример JSON-файла параметров шаблона:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Разверните шаблон с помощью следующего скрипта PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

При успешном развертывании шаблона выводятся следующие выходные данные:

![Результаты развертывания шаблона](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

При развертывании этой виртуальной машины Azure Stack отправляет на нее сертификат. Расположение сертификата зависит от операционной системы виртуальной машины.

* В Windows сертификат добавляется в расположение сертификата **LocalMachine** с помощью хранилища сертификатов, предоставленного пользователем.
* В Linux сертификат размещается в каталоге `/var/lib/waagent directory`: для файла сертификата X509 имя — &lt;UppercaseThumbprint&gt;.crt, а для закрытого ключа — &lt;UppercaseThumbprint&gt;.prv.

## <a name="retire-certificates"></a>Списание сертификатов

Прекращение использования сертификатов — это часть процесса управления сертификатами. Вы не можете удалить более раннюю версию сертификата, но ее можно отключить с помощью командлета `Set-AzureKeyVaultSecretAttribute`.

В примере ниже показано, как отключить сертификат. Укажите собственные значения для параметров **VaultName**, **Name** и **Version**.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Дополнительная информация

* [Развертывание виртуальной машины с помощью пароля из хранилища ключей](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Пример приложения, использующего ключи и секретные данные, хранящиеся в хранилище ключей](azure-stack-key-vault-sample-app.md)
