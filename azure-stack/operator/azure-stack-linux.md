---
title: Добавление образов Linux в Azure Stack Marketplace | Документация Майкрософт
description: Из этой статьи вы узнаете, как добавлять образы Linux в Azure Stack Marketplace.
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: d7723dcdd755a926990ee52e96c3b75694651520
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277207"
---
# <a name="add-linux-images-to-azure-stack-marketplace"></a>Добавление образов Linux в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные машины Linux можно развернуть в Azure Stack, добавив образ на базе Linux в Azure Stack Marketplace. Проще всего добавить образ Linux в Azure Stack с помощью управления Marketplace. Эти образы были подготовлены и протестированы на совместимость с Azure Stack.

## <a name="marketplace-management"></a>Управление Marketplace

Сведения о том, как скачать образы Linux из Azure Marketplace, см. в статье [Скачивание элементов Marketplace из Azure и их публикация в Azure Stack](azure-stack-download-azure-marketplace-item.md). Выберите образы Linux, которые вы хотите предложить пользователям в Azure Stack.

Эти образы часто обновляются, поэтому чаще посещайте портал управления Marketplace, чтобы обеспечить их актуальное состояние.

## <a name="prepare-your-own-image"></a>Подготовка собственного образа

По возможности скачивайте образы, доступные на портале управления Marketplace. Они будут подготовлены и протестированы для Azure Stack.

### <a name="azure-linux-agent"></a>Агент Linux для Azure

Агент Linux для Azure (обычно называется **WALinuxAgent** или **walinuxagent**) использовать обязательно, при этом не все версии агента будут работать с Azure Stack. Версии с 2.2.20 по 2.2.35 не поддерживаются в Azure Stack. Для использования последней версии агента выше 2.2.35 примените исправления 1901 и 1902 или обновите Azure Stack до выпуска 1903 (или последующего). Обратите внимание на то, что сейчас [cloud-init](https://cloud-init.io/) не поддерживается в Azure Stack.

| Сборка Azure Stack | Сборка агента Linux для Azure |
| ------------- | ------------- |
| 1.1901.0.99 или предыдущая | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 или последующая |
| 1.1902.2.73  | 2.2.35 или последующая |
| 1.1903.0.35  | 2.2.35 или последующая |
| Выпуски после 1903 | 2.2.35 или последующая |
| Не поддерживается | 2.2.21–2.2.34 |

Можно подготовить свой собственный образ Linux с помощью следующих инструкций.

* [Подготовка виртуальной машины на основе CentOS для Azure](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуального жесткого диска Debian для Azure](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [Подготовка виртуальной машины SLES или openSUSE для Azure](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Сервер Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-marketplace"></a>Добавление образа в Marketplace

Следуйте указаниям по [добавлению образа в marketplace](azure-stack-add-vm-image.md). Убедитесь, что для параметра `OSType` задано значение `Linux`.

После добавления образа в Marketplace создается элемент Marketplace, и пользователи могут развернуть виртуальную машину Linux.

## <a name="next-steps"></a>Дополнительная информация

* [Скачивание элементов Marketplace из Azure в Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Общие сведения об Azure Stack Marketplace](azure-stack-marketplace.md)
