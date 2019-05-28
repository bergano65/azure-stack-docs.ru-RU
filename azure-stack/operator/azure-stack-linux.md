---
title: Добавление образов Linux в Azure Stack
description: Из этой статьи вы узнаете, как добавлять образы Linux в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 40a60c5207494ae70ccdfd051c8a223493b704c5
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991105"
---
# <a name="add-linux-images-to-azure-stack"></a>Добавление образов Linux в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные машины Linux можно развернуть в Azure Stack, добавив образ на базе Linux в Azure Stack Marketplace. Проще всего добавить образ Linux в Azure Stack с помощью управления Marketplace. Эти образы были подготовлены и протестированы на совместимость с Azure Stack.

## <a name="marketplace-management"></a>Управление Marketplace

Чтобы загрузить образы Linux из Azure Marketplace, выполните процедуры, описанные в статье [Скачивание элементов Marketplace из Azure в Azure Stack](azure-stack-download-azure-marketplace-item.md). Выберите образы Linux, которые вы хотите предложить пользователям в Azure Stack.

Эти образы часто обновляются, поэтому чаще посещайте портал управления Marketplace, чтобы обеспечить их актуальное состояние.

## <a name="prepare-your-own-image"></a>Подготовка собственного образа

По возможности скачивайте образы, доступные на портале управления Marketplace. Они будут подготовлены и протестированы для Azure Stack.

### <a name="azure-linux-agent"></a>Агент Linux для Azure

Агент Linux для Azure (обычно это `WALinuxAgent` или `walinuxagent`) использовать обязательно, при этом не все версии агента будут работать с Azure Stack. Версии с 2.2.20 по 2.2.35 не поддерживаются в Azure Stack. Для использования последней версии агента выше 2.2.35 примените исправления 1901 и 1902 или обновите Azure Stack до выпуска 1903 (или последующего). Обратите внимание на то, что в настоящее время [cloud-init](https://cloud-init.io/) не поддерживается в Azure Stack.

| Сборка Azure Stack | Сборка агента Linux для Azure |
| ------------- | ------------- |
| 1.1901.0.99 или предыдущая | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 или последующая |
| 1.1902.2.73  | 2.2.35 или последующая |
| 1.1903.0.35  | 2.2.35 или последующая |
| Не поддерживается | 2.2.21–2.2.34 |

Можно подготовить свой собственный образ Linux с помощью следующих инструкций.

* [Подготовка виртуальной машины на основе CentOS для Azure](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуального жесткого диска Debian для Azure](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуальной машины на основе Red Hat для Azure](azure-stack-redhat-create-upload-vhd.md)
* [Подготовка виртуальной машины SLES или openSUSE для Azure](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Сервер Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Добавление образа в marketplace

Следуйте указаниям по [добавлению образа в marketplace](azure-stack-add-vm-image.md). Убедитесь, что для параметра `OSType` задано значение `Linux`.

После добавления образа в Marketplace создается элемент Marketplace, и пользователи могут развернуть виртуальную машину Linux.

## <a name="next-steps"></a>Дополнительная информация

* [Скачивание элементов Marketplace из Azure в Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Общие сведения об Azure Stack Marketplace](azure-stack-marketplace.md)
