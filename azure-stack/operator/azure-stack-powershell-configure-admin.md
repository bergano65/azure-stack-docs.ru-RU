---
title: Подключение к Azure Stack Hub с помощью PowerShell
description: Узнайте, как подключиться к Azure Stack Hub с помощью PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 1c894dbb431d1f171457ac0300d214cc2e2b7c0f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881615"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Подключение к Azure Stack Hub с помощью PowerShell

Azure Stack Hub можно настроить для управления с помощью PowerShell такими ресурсами, как создание предложений, планов, квот и предупреждений. Этот раздел поможет настроить среду оператора.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие предварительные условия либо из [Пакета средств разработки Azure Stack (ASDK)](../asdk/asdk-connect.md#connect-with-rdp), либо из внешнего клиента на базе Windows, если вы [подключены к ASDK через VPN](../asdk/asdk-connect.md#connect-with-vpn).

- Установите [совместимые с Azure Stack Hub модули Azure PowerShell](azure-stack-powershell-install.md).  
- Скачайте [средства, необходимые для работы с Azure Stack Hub](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Подключение к Azure AD

Выполните один из приведенных ниже сценариев, чтобы настроить среду оператора Azure Stack Hub с помощью PowerShell. Замените значение tenantName для Azure Active Directory (Azure AD) и значение конечной точки Azure Resource Manager собственной конфигурацией среды.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Подключение к AD FS

Подключитесь к среде оператора Azure Stack Hub с помощью PowerShell и служб федерации Azure Active Directory (Azure AD FS). Для ASDK для этой конечной точки Azure Resource Manager устанавливается значение `https://adminmanagement.local.azurestack.external`. Чтобы получить конечную точку Azure Resource Manager для интегрированных систем Azure Stack Hub, обратитесь к поставщику услуг.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS поддерживает только интерактивную проверку подлинности с удостоверениями пользователей. Если требуется объект учетных данных, необходимо использовать субъект-службу (SPN). Дополнительные сведения о том, как с помощью Azure Stack Hub и AD FS настроить субъект-службу в качестве службы управления удостоверениями, см. в разделе [Управление субъектом-службой AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, можно создавать ресурсы в Azure Stack Hub с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка шаблонов для Azure Stack Hub](../user/azure-stack-develop-templates.md).
- [Развертывание шаблонов с помощью PowerShell](../user/azure-stack-deploy-template-powershell.md).
  - [Справочник по модулю Azure Stack Hub](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
