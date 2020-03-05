---
title: Подключение к Azure Stack Hub в качестве пользователя с помощью PowerShell
description: Узнайте, как подключиться к Azure Stack Hub с помощью PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 3656a5a6a992788ca8d4d975ac819f69793edb02
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702046"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Подключение к Azure Stack Hub в качестве пользователя с помощью PowerShell

Вы можете подключиться к Azure Stack Hub для управления ресурсами с помощью PowerShell. Например, с помощью PowerShell можно будет подписываться на предложения, создавать виртуальные машины и развертывать шаблоны Azure Resource Manager.

Для настройки сделайте следующее:
  - У вас есть такие требования:
  - Присоединиться к Azure Active Directory (Azure AD) и службам федерации Active Directory (AD FS). 
  - Зарегистрируйте поставщиков ресурсов.
  - Проверьте подключение.

## <a name="prerequisites-to-connecting-with-powershell"></a>Предварительные требования для подключения с помощью PowerShell

Выполните предварительные требования с помощью [пакета средств разработки](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) или внешнего клиента на основе Windows, если используется [VPN-подключение](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Установите [совместимые с Azure Stack Hub модули Azure PowerShell](../operator/azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack Hub](../operator/azure-stack-powershell-download.md).

Обязательно замените приведенные ниже переменные сценария значениями из конфигурации Azure Stack Hub:

- **Имя клиента Azure AD**  
  Имя вашего клиента Azure AD, используемое для управления Azure Stack Hub. например yourdirectory.onmicrosoft.com.
- **Конечная точка Azure Resource Manager**  
  Для пакета средств разработки Azure Stack используется значение https://management.local.azurestack.external. Чтобы получить это значение для интегрированных систем Azure Stack Hub, обратитесь к поставщику услуг.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Подключение к Azure Stack Hub с помощью Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Подключение к Azure Stack Hub с помощью AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
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

Настроив все необходимое, проверьте подключение с помощью PowerShell для создания ресурсов в Azure Stack Hub. Для проверки создайте группу ресурсов для приложения и добавьте в нее виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов MyResourceGroup.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка шаблонов для Azure Stack Hub](azure-stack-develop-templates.md)
- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
- [Модуль Azure Stack Hub 1.7.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Вы также можете настроить среду PowerShell для облачного оператора. Это описывается в разделе [Подключение к Azure Stack Hub с помощью PowerShell](../operator/azure-stack-powershell-configure-admin.md).
