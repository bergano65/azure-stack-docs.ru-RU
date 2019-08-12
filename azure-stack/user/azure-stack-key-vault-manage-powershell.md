---
title: Управление Key Vault в Azure Stack с помощью PowerShell | Документация Майкрософт
description: Узнайте, как управлять Key Vault в Azure Stack с помощью PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: ca303590d4dc923380e10e50fc9b8b9ce2e5aac6
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842961"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Управление Key Vault в Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье объясняется, как создать и администрировать хранилище ключей в Azure Stack. Вы узнаете, как с помощью командлетов PowerShell для Key Vault выполнять следующие задачи:

* Создать хранилище ключей.
* сохранение криптографических ключей и секретов, а также управление ими;
* авторизация пользователей и приложений для вызова операций в хранилище.

>[!NOTE]
>Командлеты PowerShell для Key Vault, описанные в этой статье, доступны в пакете SDK для Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

* Необходимо подписаться на предложение, включающее службу Azure Key Vault.
* [Установите PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md).
* [Настройка пользовательской среды PowerShell в Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Включение операций с Key Vault для клиентской подписки

Чтобы выполнять любые операции с хранилищем ключей, они должны быть разрешены для подписки клиента. Чтобы убедиться, что хранилище включено, выполните следующую команду:

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Если для подписки разрешены операции с хранилищем, в выходных данных в столбце **RegistrationState** для всех типов ресурсов хранилища ключей будет указано значение **Registered**.

![Состояние регистрации Key Vault в PowerShell](media/azure-stack-key-vault-manage-powershell/image1.png)

Если операции с хранилищем не разрешены, выполните следующую команду, чтобы зарегистрировать службу Key Vault в своей подписке:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Если регистрация прошла успешно, возвращается следующий результат:

![Регистрация Key Vault в PowerShell выполнена](media/azure-stack-key-vault-manage-powershell/image2.png)

При вызове команд Key Vault могут появляться ошибки, например, "Подписка не зарегистрирована для использования пространства имен "Microsoft.KeyVault". При появлении такой ошибки убедитесь, что вы включили поставщик ресурсов Key Vault (см. сведения выше).

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Чтобы создать хранилище ключей, сначала нужно создать группу ресурсов для размещения всех относящихся к хранилищу ключей ресурсов. Выполните следующую команду, чтобы создать новую группу ресурсов:

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Группа ресурсов, созданная в PowerShell](media/azure-stack-key-vault-manage-powershell/image3.png)

Теперь воспользуйтесь командой **New-AzureRMKeyVault**, чтобы создать хранилище ключей в ранее созданной группе ресурсов. Эта команда принимает три обязательных параметра: имя группы ресурсов, имя хранилища ключей и географическое расположение.

Чтобы создать хранилище ключей, выполните следующую команду:

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Хранилище ключей, созданное в PowerShell](media/azure-stack-key-vault-manage-powershell/image4.png)

В выходных данных команды будут указаны свойства хранилища ключей, которое вы создали. Когда приложение обращается к этому хранилищу, нужно использовать свойство **Vault URI**. В этом примере оно имеет значение `https://vault01.vault.local.azurestack.external`.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Развертывание на основе служб федерации Active Directory (AD FS)

При развертывании AD FS может появиться предупреждение: "Политика доступа не установлена. "Access policy is not set. No user or application has access permission to use this vault" (Политика доступа не установлена. Нет пользователей или приложений с правами на использование этого хранилища). Чтобы устранить эту проблему, создайте для хранилища политику доступа с помощью команды [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-app-to-use-a-key-or-secret):

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Управление ключами и секретами

После создания хранилища выполните инструкции ниже, чтобы создать в хранилище ключи и секреты и управлять ими.

### <a name="create-a-key"></a>Создание ключа

Используйте команду **Add-AzureKeyVaultKey**, чтобы создать или импортировать ключ с программной защитой в хранилище ключей.

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

Параметр **Destination** позволяет указать, что ключ имеет программную защиту. После создания ключа команда выводит сведения об успешном выполнении операции.

![Ключ хранилища ключей, созданный в PowerShell](media/azure-stack-key-vault-manage-powershell/image5.png)

Теперь вы можете использовать созданный ключ, указывая его URI. Если вы создаете или импортируете ключ, имя которого совпадает с именем существующего ключа, все указанные для нового ключа параметры сохраняются в исходном ключе. Информацию о предыдущей версии ключа можно получить с помощью URI для конкретной версии. Например:

* `https://vault10.vault.local.azurestack.external:443/keys/key01` всегда предоставляет текущую версию.
* `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` позволяет получить конкретную версию.

### <a name="get-a-key"></a>Получение ключа

Используйте команду **Get-AzureKeyVaultKey**, чтобы считать ключ и сведения о нем.

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Создание секрета

Используйте команду **Set-AzureKeyVaultSecret**, чтобы создать или обновить секрет в хранилище. Если секрет еще не существует, создается новый секрет. Если секрет уже существует, создается новая версия этого секрета.

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Создание секрета в PowerShell](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Получение секрета

Используйте команду **Get-AzureKeyVaultSecret**, чтобы считать секрет из хранилища ключей. Эта команда может возвращать все версии секрета или только конкретные версии.

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Создав ключи и секреты, вы можете разрешить внешним приложениям использовать их.

## <a name="authorize-an-app-to-use-a-key-or-secret"></a>Авторизация приложения для использования ключа или секрета

Используйте команду **Set-AzureRmKeyVaultAccessPolicy**, чтобы разрешить приложению обращаться к ключу или секрету в хранилище ключей.

В следующем примере хранилище имеет имя *ContosoKeyVault*. Вам нужно разрешить приложению с идентификатором клиента *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed* обращаться к этому хранилищу. Для этого выполните следующую команду. Кроме того, вы можете предоставить разрешения пользователю, приложению или группе безопасности с помощью параметра **PermissionsToKeys**.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Чтобы разрешить этому же приложению считывать секреты из хранилища, выполните следующий командлет:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Дополнительная информация

* [Создание виртуальной машины с помощью пароля, хранящегося в хранилище ключей](azure-stack-key-vault-deploy-vm-with-secret.md).
* [Создание виртуальной машины и добавление сертификатов, полученных из хранилища ключей](azure-stack-key-vault-push-secret-into-vm.md).
