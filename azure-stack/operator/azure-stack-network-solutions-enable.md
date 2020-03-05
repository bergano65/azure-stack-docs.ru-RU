---
title: Предложение сетевого решения для Azure Stack Hub с помощью Fortinet FortiGate
description: Сведения об активации в Azure Stack Hub сетевого решения с помощью Fortinet FortiGate
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 11104bc3cfd52b6e725420ad5fd71416c12a70a8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698544"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Предложение сетевого решения для Azure Stack Hub с помощью Fortinet FortiGate

В Azure Stack Hub Marketplace можно добавить брандмауэр следующего поколения FortiGate. FortiGate позволяет пользователям создавать сетевые решения, такие как виртуальная частная сеть для Azure Stack Hub и пиринг виртуальных сетей. Виртуальный сетевой модуль (NVA) используется для управления передачей сетевого трафика из сети периметра к другим сетям и подсетям. 

Дополнительные сведения о FortiGate в Azure Marketplace см. на странице [FortiGate Next-Generation Firewall — Single VM](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm) (Брандмауэр следующего поколения FortiGate для одной виртуальной машины).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Скачивание необходимых элементов Marketplace для Azure Stack Hub

1.  Откройте портал администрирования Azure Stack Hub.

2.  Выберите **Marketplace management** (Управление Marketplace) и щелкните **Add from Azure** (Добавить из Azure).

3. Введите `Forti` в поле поиска и дважды щелкните его, затем выберите **Скачать**, чтобы получить последние доступные версии следующих элементов: 
    - виртуальной машины Fortinet FortiGate-VM для Azure BYOL;
    - развертывания одной виртуальной машины (BYOL) FortiGate NGFW.

    ![FortiGate Fortinet в Azure Stack Hub](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Подождите, пока элементы Marketplace не получат состояние **Скачано**. Для скачивания элементов может потребоваться несколько минут.

    ![FortiGate Fortinet в Azure Stack Hub](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Дальнейшие действия

[Настройка VPN-шлюза для Azure Stack Hub с использованием виртуального сетевого модуля FortiGate](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Инструкции по подключению двух виртуальных сетей через пиринг](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Установка подключения между виртуальными сетями в Azure Stack с помощью NVA Fortinet FortiGate](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
