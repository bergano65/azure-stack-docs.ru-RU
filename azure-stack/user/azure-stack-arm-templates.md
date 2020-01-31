---
title: Использование шаблонов Azure Resource Manager в Azure Stack Hub
description: Узнайте, как использовать шаблоны Azure Resource Manager в Azure Stack Hub для подготовки ресурсов.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 48e418c3fa8dd761282f67f59b1cf1ebfc18610b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883957"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack-hub"></a>Использование шаблонов Azure Resource Manager в Azure Stack Hub

Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции. Кроме того, можно повторно развернуть шаблоны, чтобы внести изменения в ресурсы в группе ресурсов.

Эти шаблоны можно развертывать с помощью портала Microsoft Azure Stack Hub, PowerShell, командной строки и Visual Studio.

На сайте [GitHub](https://aka.ms/azurestackgithub) доступны следующие шаблоны для быстрого запуска:

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Развертывание SharePoint Server (с обычным уровнем доступности)

Используйте расширение PowerShell [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC), чтобы [создать ферму SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), которая включает следующие ресурсы:

* виртуальную сеть;
* три учетные записи хранения;
* два внешних балансировщика нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;
* одну виртуальную машину, настроенную в качестве изолированного сервера SQL Server 2014;
* одну виртуальную машину, настроенную в качестве фермы SharePoint Server 2013 с одним компьютером.

## <a name="deploy-ad-non-high-availability-deployment"></a>Развертывание AD (с обычным уровнем доступности)

Используйте расширение PowerShell DSC для [создания сервера контроллера домена AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), который включает в себя следующие ресурсы:

* виртуальную сеть;
* одна учетная запись хранения;
* один внешний балансировщик нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;

## <a name="deploy-adsql-non-high-availability-deployment"></a>Развертывание AD и SQL (с обычным уровнем доступности)

Используйте расширение PowerShell DSC для [создания изолированного сервера SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), который включает в себя следующие ресурсы:

* виртуальную сеть;
* две учетные записи хранения;
* один внешний балансировщик нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;
* одну виртуальную машину, настроенную в качестве изолированного сервера SQL Server 2014;

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Используйте расширение PowerShell DSC, чтобы настроить локальный диспетчер конфигурации (LCM) существующей виртуальной машины и зарегистрировать ее на опрашивающем сервере DSC учетных записей службы автоматизации Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Создание виртуальной машины из пользовательского образа

[Можно создать виртуальную машину из настраиваемого пользовательского образа.](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage) Этот шаблон также развертывает виртуальную сеть (с DNS), общедоступный IP-адрес и сетевой интерфейс.

## <a name="basic-virtual-machine"></a>Виртуальная машина уровня "Базовый"

[Разверните виртуальную машину Windows](https://aka.ms/aa6zdzx) с виртуальной сетью (с DNS), общедоступным IP-адресом и сетевым интерфейсом.

## <a name="cancel-a-running-template-deployment"></a>Отмена выполняющегося развертывания шаблона

Чтобы отменить выполняющееся развертывание шаблона, используйте [командлет](/powershell/scripting/developer/cmdlet/cmdlet-overview) PowerShell [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment).

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание шаблонов с помощью портала](azure-stack-deploy-template-portal.md)
* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
* [Развертывание шаблонов с помощью Visual Studio](azure-stack-deploy-template-visual-studio.md)
* [Общие сведения об Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
