---
title: Развертывание базовых шаблонов в Azure Stack Hub
description: Узнайте, как развертывать базовые шаблоны с помощью Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 28775cb3c279e2976a32a63fc21a8797bba6eb38
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705089"
---
# <a name="deploy-foundational-patterns-overview"></a>Общие сведения о развертывании базовых шаблонов


Каждый из этих шаблонов содержит рекомендации, шаблоны Azure Resource Manager и руководства. Объединив эти шаблоны со сторонними приложениями, можно создавать предложения, которые пока не поддерживает Azure Stack. Например, операторы часто сталкиваются с трудностями при настройке виртуальной частной сети (VPN) для одного экземпляра Azure Stack Hub. Гораздо реже создается VPN-сеть, охватывающая две или более сред. У операторов могут возникнуть проблемы при попытке создать для Azure Stack Hub подсистему балансировки нагрузки, чтобы управлять рабочими нагрузками. Приведенные ниже рекомендации помогут ускорить развертывание и освободить рабочие нагрузки, готовые к выполнению в рабочей среде.

## <a name="networking"></a>Сеть

С помощью шаблонов работы в сети вы получите инструкции по созданию пиринга между виртуальными сетями с использованием Azure Stack Hub. Пиринг между виртуальными сетями позволяет установить подключение между двумя виртуальными сетями таким образом, чтобы они функционировали как одна сеть. Подключение "сеть — сеть" между виртуальными сетями реализуется с помощью службы удаленного доступа и маршрутизации (RRAS). RRAS позволяет виртуальным машинам Windows работать в качестве маршрутизаторов. С помощью этих скриптов вы можете развертывать две виртуальные сети между группами ресурсов в одной группе ресурсов Azure Stack Hub, между подписками и между двумя экземплярами Azure Stack Hub. Скрипты можно развернуть в Azure Stack Hub и глобальной службе Azure. 

В каждой статье рассматриваются общие вопросы, такие как: 
- Масштабирование
- Пропускная способность
- Безопасность
- Непрерывность бизнес-процессов

|  Пиринг между виртуальными сетями  |  Виртуальная частная сеть  |  Подсистема балансировки нагрузки  |
| --- | --- | --- |
| ![Пиринг между виртуальными сетями с использованием виртуальных машин](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Пиринг между виртуальными сетями с использованием виртуальных машин](azure-stack-network-howto-vnet-peering.md) | ![Настройка VPN для локальной среды](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Настройка VPN для локальной среды](azure-stack-network-howto-vnet-to-onprem.md) | ![Подсистема балансировки нагрузки от F5](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[Подсистема балансировки нагрузки от F5](network-howto-f5.md) |
| ![Пиринг между виртуальными сетями с использованием FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Пиринг между виртуальными сетями с использованием FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Виртуальная частная сеть](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Подключение между виртуальными сетями](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![Создание VPN-туннеля (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Создание VPN-туннеля (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Настройка нескольких VPN типа "сеть — сеть"](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Настройка нескольких VPN типа "сеть — сеть"](network-howto-vpn-tunnel.md) | |
|  | ![Создание VPN-туннеля (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Создание VPN-туннеля (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Память

Шаблоны хранилища помогут расширить возможности хранения данных с помощью Azure Stack Hub. Хранилище Azure Stack Hub ограничено. Подключитесь к ресурсам в существующем центре обработки данных. Получите инструкции по созданию виртуальной машины Windows в Azure Stack Hub для подключения к внешнему целевому объекту iSCSI. Вы можете узнать, как включить ключевые функции, такие как Multipath I/O (MPIO), для оптимизации производительности и подключения между виртуальной машиной и внешним накопителем.

| Хранилище iSCSI | Расширение хранилища |
| --- | --- | --- |
| ![Подключение к хранилищу iSCSI](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Подключение к хранилищу iSCSI](azure-stack-network-howto-iscsi-storage.md) | ![Расширение центра обработки данных](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Расширение центра обработки данных](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Резервное копирование

Шаблоны резервного копирования и аварийного восстановления позволяют копировать все ресурсы в подписке в Azure или другой экземпляр Azure Stack Hub. Эти шаблоны требуют синхронизации Commvault в режиме реального времени для репликации данных, хранящихся на виртуальных машинах, в другую среду. Здесь вы можете найти скрипты, позволяющие создать учетную запись хранения и учетную запись хранилища резервных копий для отправки данных. С помощью репликатора модуля подписки Azure можно оркестрировать репликацию ресурсов, а также настроить обработчик для различных ресурсов. 



|  Резервное копирование  |  Копировать  |
| --- | --- | --- |
| ![Резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[Резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault](azure-stack-network-howto-backup-commvault.md) | ![Копирование ресурсов подписки](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Копирование ресурсов подписки](azure-stack-network-howto-backup-replicator.md) |
|  | ![Резервное копирование учетных записей хранения в Azure Stack Hub](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[Резервное копирование учетных записей хранения в Azure Stack Hub](azure-stack-network-howto-backup-storage.md)  |

## <a name="github-samples"></a>Примеры GitHub

Вы можете найти шаблоны в репозитории [шаблонов интеллектуальных границ Azure](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) на сайте GitHub.

## <a name="next-steps"></a>Дальнейшие действия

[Документация по гибридным шаблонам и решениям Azure](https://docs.microsoft.com/azure-stack/hybrid/)
