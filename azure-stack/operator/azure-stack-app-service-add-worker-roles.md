---
title: Добавление рабочих ролей и инфраструктуры в Службе приложений в Azure Stack Hub | Документация Майкрософт
description: Подробное руководство по масштабированию Службы приложений Azure в Azure Stack Hub
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: b07d90f17d1c4380f65c43029215bf8946d209e2
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924206"
---
# <a name="add-workers-and-infrastructure-in-azure-app-service-on-azure-stack-hub"></a>Добавление рабочих ролей и инфраструктуры в Службе приложений Azure в Azure Stack Hub

*Область применения: интегрированные системы Azure Stack Hub и Пакет средств разработки Azure Stack Hub*  

Узнайте, как масштабировать рабочие роли и роли инфраструктуры в Службе приложений Azure в Azure Stack Hub. Здесь приведены инструкции по созданию дополнительных рабочих ролей с поддержкой приложений любого размера.

> [!NOTE]
> Если в среде Azure Stack Hub объем памяти не превышает 96 ГБ, при добавлении дополнительной емкости могут возникнуть проблемы.

По умолчанию Служба приложений Azure в Azure Stack Hub поддерживает бесплатные и общие рабочие уровни. Чтобы добавить другие рабочие уровни, вам потребуется больше рабочих ролей.

Если вы не уверены, какие компоненты были развернуты вместе со Службой приложений Azure при установке Azure Stack Hub, см. [обзор Службы приложений в Azure Stack Hub](azure-stack-app-service-overview.md).

Служба приложений Azure в Azure Stack Hub развертывает все роли в масштабируемых наборах виртуальных машин, что обеспечивает дополнительные возможности масштабирования. Чтобы использовать эти возможности, все операции масштабирования рабочих уровней выполняются с правами администратора службы приложений.

## <a name="add-additional-workers-with-powershell"></a>Добавление дополнительных рабочих ролей с помощью PowerShell

1. [Настройка среды администратора Azure Stack Hub в PowerShell](azure-stack-powershell-configure-admin.md)

2. Используйте этот пример, чтобы развернуть масштабируемый набор.
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > На выполнение этого шага может уйти несколько часов, в зависимости от типа роли и количества экземпляров.
   >
   >

3. Состояние новых экземпляров роли можно отслеживать, используя функцию администрирования Службы приложений. Чтобы проверить состояние для отдельного экземпляра роли, щелкните нужный тип роли в списке.

## <a name="add-additional-workers-using-the-administrator-portal"></a>Добавление дополнительных рабочих ролей с помощью портала администрирования

1. Войдите на портал администрирования Azure Stack Hub с правами администратора служб.

2. Перейдите к **службе приложений**.

    ![Служба приложений на портале администрирования Azure Stack Hub](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Выберите **Роли**. Здесь подробно представлены все развертываемые роли службы приложений.

4. Щелкните правой кнопкой мыши строку для типа роли, которую нужно масштабировать, и выберите **ScaleSet**.

    ![Роли ScaleSet Службы приложений на портале администрирования Azure Stack Hub](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Щелкните **Масштабирование**, выберите число экземпляров для масштабирования и нажмите кнопку **Сохранить**.

    ![Настройка экземпляров для масштабирования в ролях Службы приложений на портале администрирования Azure Stack Hub](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Служба приложений Azure в Azure Stack Hub добавит и настроит виртуальные машины, установит все необходимое программное обеспечение и отметит рабочие роли как готовые к использованию, когда процесс подготовки завершится. Весь процесс может занять около 80 минут.

7. Ход подготовки новых ролей можно отслеживать в списке рабочих ролей в колонке **Роли**.

## <a name="result"></a>Результат

Когда рабочие роли будут полностью развернуты и готовы к использованию, пользователи смогут размещать в них свои рабочие нагрузки. На следующем снимке экрана представлено несколько ценовых категорий, доступных по умолчанию. Если для определенного рабочего уровня нет доступных рабочих процессов, вы не сможете выбрать соответствующую ценовую категорию.

![Ценовые категории для новых планов Службы приложений App на портале администрирования Azure Stack Hub](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Чтобы масштабировать роли управления, интерфейса или издателя, выполните те же действия, выбирая соответствующий тип роли. Контроллеры не развертываются как масштабируемые наборы. Поэтому во время установки для всех рабочих развертываний нужно развернуть два контроллера.

### <a name="next-steps"></a>Дальнейшие действия

[Настройка источников развертывания](azure-stack-app-service-configure-deployment-sources.md)
