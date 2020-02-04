---
title: Развертывание шаблона в Azure Stack Hub с помощью PowerShell.
description: Развертывание шаблона в Azure Stack Hub с помощью PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: e0398eb36834676fec53103bb07dbe24bdc57c5d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883822"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Развертывание шаблона в Azure Stack Hub с помощью PowerShell

С помощью PowerShell можно развертывать шаблоны Azure Resource Manager в Azure Stack Hub. В этой статье показано, как использовать PowerShell для развертывания шаблона.

## <a name="run-azurerm-powershell-cmdlets"></a>Выполнение командлетов PowerShell для AzureRM

Этот пример использует командлеты PowerShell для **AzureRM** и шаблон, хранящийся на сайте GitHub. Этот шаблон создает виртуальную машину Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Прежде чем выполнять этот пример, убедитесь, что вы [настроили PowerShell](azure-stack-powershell-configure-user.md) для пользователя Azure Stack Hub.

1. Перейдите в репозиторий [AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) и найдите шаблон **101-simple-windows-vm**. Сохраните шаблон в этом расположении: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Откройте командную строку PowerShell с повышенными привилегиями.
3. Замените `username` и `password` в приведенном ниже скрипте своим именем пользователя и паролем, а затем запустите этот скрипт.

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > При каждом последующем запуске этого сценария увеличивайте значение параметра `$myNum`, чтобы избежать перезаписи развертывания.

4. Откройте портал Azure Stack Hub, выберите **Обзор** > **Виртуальные машины** и найдите свою новую виртуальную машину (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Отмена выполняющегося развертывания шаблона

Чтобы отменить выполняющееся развертывание шаблона, используйте командлет PowerShell `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание шаблона с помощью Visual Studio](azure-stack-deploy-template-visual-studio.md)
