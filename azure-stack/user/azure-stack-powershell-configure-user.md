---
title: Подключение к Azure Stack в роли пользователя с помощью PowerShell | Документация Майкрософт
description: Узнайте, как подключиться к Azure Stack с помощью PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6a75eb788afd84b6619326293ae2399d8ed5b0e1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824211"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Подключение к Azure Stack в роли пользователя с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете подключиться к Azure Stack для управления ресурсами с помощью PowerShell. Например, с помощью PowerShell можно будет подписываться на предложения, создавать виртуальные машины и развертывать шаблоны Azure Resource Manager.

Для настройки сделайте следующее:
  - У вас есть такие требования:
  - Присоединиться к Azure Active Directory (Azure AD) и службам федерации Active Directory (AD FS). 
  - Зарегистрируйте поставщиков ресурсов.
  - Проверьте подключение.

## <a name="prerequisites-to-connecting-with-powershell"></a>Предварительные требования для подключения с помощью PowerShell

Выполните предварительные требования с помощью [пакета средств разработки](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) или внешнего клиента на основе Windows, если используется [VPN-подключение](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Установите [совместимые с Azure Stack модули Azure PowerShell](../operator/azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](../operator/azure-stack-powershell-download.md).

Обязательно замените приведенные ниже переменные сценария значениями из конфигурации Azure Stack:

- **Имя клиента Azure AD**  
  Имя вашего клиента Azure AD, используемое для управления Azure Stack, например yourdirectory.onmicrosoft.com.
- **Конечная точка Azure Resource Manager**  
  Для пакета средств разработки Azure Stack используется значение https://management.local.azurestack.external. Чтобы получить это значение для интегрированных систем Azure Stack, обратитесь к поставщику услуг.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Подключение к Azure Stack с помощью Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-with-ad-fs"></a>Подключение к Azure Stack с помощью служб федерации Active Directory (AD FS)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Регистрация поставщиков ресурсов

Поставщики ресурсов не регистрируются автоматически в новых подписках пользователей, для которых на портале не развернуты какие-либо ресурсы. Можно явно зарегистрировать поставщик ресурсов, выполнив следующий сценарий.

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>Проверка подключения

Настроив все необходимое, проверьте подключение с помощью PowerShell для создания ресурсов в Azure Stack. Для проверки создайте группу ресурсов для приложения и добавьте в нее виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов MyResourceGroup.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
- [Модуль Azure Stack 1.7.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Вы также можете настроить среду PowerShell для облачного оператора. Это описывается в разделе [Настройка среды PowerShell оператора Azure Stack](../operator/azure-stack-powershell-configure-admin.md).
