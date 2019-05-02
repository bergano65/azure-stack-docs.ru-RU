---
title: Использование шаблонов Azure Resource Manager в Azure Stack | Документация Майкрософт
description: Узнайте, как использовать шаблоны Azure Resource Manager в Azure Stack для подготовки ресурсов.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: b2f2427f7dcf9265f379995f1e854c382e24a9c7
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310082"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Использование шаблонов диспетчера ресурсов Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции. Кроме того, можно повторно развернуть шаблоны, чтобы внести изменения в ресурсы в группе ресурсов.

Эти шаблоны можно развертывать с помощью портала Microsoft Azure Stack, PowerShell, командной строки и Visual Studio.

В [GitHub](https://aka.ms/azurestackgithub) доступны следующие шаблоны быстрого запуска.

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Развертывание SharePoint Server (с обычным уровнем доступности)

Используйте расширение PowerShell DSC для [создания фермы SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), которая включает в себя следующие ресурсы:

* виртуальную сеть;
* три учетные записи хранения;
* два внешних балансировщика нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;
* одну виртуальную машину, настроенную в качестве изолированного сервера SQL Server 2014;
* одну виртуальную машину, настроенную в качестве фермы SharePoint Server 2013 с одним компьютером.

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

[Разверните виртуальную машину Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) с виртуальной сетью (с DNS), общедоступным IP-адресом и сетевым интерфейсом.

## <a name="cancel-a-running-template-deployment"></a>Отмена выполняющегося развертывания шаблона

Чтобы отменить выполняющееся развертывание шаблона, используйте командлет PowerShell [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment).

## <a name="next-steps"></a>Дополнительная информация

* [Развертывание шаблонов с помощью портала](azure-stack-deploy-template-portal.md)
* [Общие сведения об Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
