---
title: Подключение к Azure Stack с помощью PowerShell в роли оператора | Документы Майкрософт
description: Подключитесь к Azure Stack с помощью PowerShell в роли оператора
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 03/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 9d49727538f89e9429c1ae979057e89c40dc0ce9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308228"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Подключитесь к Azure Stack с помощью PowerShell в роли оператора.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack можно настроить для управления такими ресурсами, как создание предложений, планов, квот и предупреждений, с помощью PowerShell. Этот раздел поможет настроить среду оператора.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие предварительные требования с помощью [пакета SDK](../asdk/asdk-connect.md#connect-with-rdp) или внешнего клиента на базе Windows (при [подключении к Azure Stack с помощью VPN](../asdk/asdk-connect.md#connect-with-vpn)). 

 - Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
 - Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Подключение к Azure AD

Настройте среду оператора Azure Stack с помощью PowerShell. Выполните один из приведенных ниже скриптов. Замените значение tenantName для Azure Active Directory (Azure AD) и значение конечной точки Azure Resource Manager собственной конфигурацией среды. 

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.partner.onmschina.cn"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Подключение к AD FS

Подключитесь к среде оператора Azure Stack с помощью PowerShell и служб федерации Azure Active Directory (Azure AD FS). Для пакета средств разработки Azure Stack для этой конечной точки Azure Resource Manager устанавливается значение `https://adminmanagement.local.azurestack.external`. Чтобы получить конечную точку Azure Resource Manager для интегрированных систем Azure Stack, обратитесь к поставщику услуг.


  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS поддерживает только интерактивную проверку подлинности с удостоверениями пользователей. Если требуется объект учетных данных, вам необходимо использовать субъект-службу (SPN). Дополнительные сведения о том, как с помощью Azure Stack и AD FS настроить субъект-службу в качестве службы управления удостоверениями, см. в разделе [Управление субъектом-службой для AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, можно создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

- [Разработка шаблонов для Azure Stack](../user/azure-stack-develop-templates.md)
- [Развертывание шаблонов с помощью PowerShell](../user/azure-stack-deploy-template-powershell.md)
  - [Модуль Azure Stack 1.7.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview)  
