---
title: Предложение сетевого решения для Azure Stack с помощью Fortinet FortiGate | Документация Майкрософт
description: Сведения об активации в Azure Stack сетевого решения с помощью Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962714"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Предложение сетевого решения для Azure Stack с помощью Fortinet FortiGate

В Azure Stack Marketplace можно добавить брандмауэр следующего поколения FortiGate. FortiGate позволяет пользователям создавать сетевые решения, такие как виртуальная частная сеть для Azure Stack и пиринг виртуальных сетей. Виртуальный сетевой модуль (NVA) используется для управления передачей сетевого трафика из сети периметра к другим сетям и подсетям. 

Дополнительные сведения о FortiGate в Azure Marketplace см. на странице [FortiGate Next-Generation Firewall — Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm) (Брандмауэр следующего поколения FortiGate для одной виртуальной машины).

## <a name="download-the-required-azure-stack-marketplace-items"></a>Скачивание необходимых элементов Marketplace для Azure Stack

1.  Войдите на портал администрирования Azure Stack.

2.  Выберите **Marketplace management** (Управление Marketplace) и щелкните **Add from Azure** (Добавить из Azure).

3. Введите `Forti` в поле поиска и дважды щелкните его, затем выберите **Скачать**, чтобы получить последние доступные версии следующих элементов: 
    - виртуальной машины Fortinet FortiGate-VM для Azure BYOL;
    - развертывания одной виртуальной машины (BYOL) FortiGate NGFW.

    ![FortiGate Fortinet в Azure Stack](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Подождите, пока элементы Marketplace не получат состояние **Скачано**. Для скачивания элементов может потребоваться несколько минут.

    ![FortiGate Fortinet в Azure Stack](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Дополнительная информация

[Настройка VPN-шлюза для Azure Stack с использованием виртуального сетевого модуля FortiGate](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Инструкции по подключению двух виртуальных сетей через пиринг](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Установка подключения между виртуальными сетями в Azure Stack с помощью NVA Fortinet FortiGate](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
