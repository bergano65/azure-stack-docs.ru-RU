---
title: Добавление Commvault в Azure Stack Marketplace | Документация Майкрософт
description: Узнайте, как добавить Commvault в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 540121b1de46eb8b1f3669a441c5aff5d8ff2e33
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73168201"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>Добавление Commvault в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье описано предложение Commvault Live Sync для обновления виртуальной машины восстановления, расположенной в отдельной единице масштабирования Azure Stack. Вы можете скачать и предложить Commvault в качестве решения для резервного копирования и репликации пользователей. 

## <a name="notes-for-commvault"></a>Примечания для Commvault

- Пользователю необходимо установить программное обеспечение резервного копирования и репликации на виртуальной машине в исходной подписке Azure Stack. Azure Site Recovery и Azure Backup могут предлагать автономное расположение для хранения резервных копий и образов восстановления. Перед загрузкой образов программного обеспечения для установки на Azure Stack из следующих расположений для них нужно создать хранилище Служб восстановления в Azure. [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) и [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Возможно, вам понадобятся лицензии для стороннего программного обеспечения (если оно выбрано).
- Вашим пользователям может понадобиться помощник для подключения их источника и целевого объекта через виртуальный сетевой шлюз (VPN) или публичный IP-адрес на узле резервного копирования и репликации.
- Подписка Target Azure Cloud или подписка для Recovery Target Azure Stack.
- Целевая группа ресурсов и учетная запись хранилища BLOB-объектов на Recovery Target Azure Stack.
- Некоторые решения требуют создания виртуальных машин по целевой подписке, которые должны выполнять 24x7x365, чтобы получать изменения от исходного сервера. В разделе [Синхронизация виртуальной машины в Azure Stack с Commvault](../user/azure-stack-network-howto-backup-commvault.md), Commvault Live Sync создает целевые виртуальные машины восстановления во время первоначальной конфигурации и оставляет их в режиме ожидания (не работает, не оплачивается), пока изменения не потребуются во время цикла репликации.


## <a name="get-commvault-for-your-marketplace"></a>Получите Commvault для своего Marketplace

1. Откройте портал администрирования Azure Stack.
2. Выберите **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

    ![Commvault для Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Укажите `commvault`.
4. Выберите **Commvault Trail**. А затем нажмите **Скачать**.


## <a name="next-steps"></a>Дополнительная информация

[Резервное копирование виртуальной машины в Azure Stack с помощью Commvault](../user/azure-stack-network-howto-backup-commvault.md)

[Общие сведения о предложении служб в Azure Stack](service-plan-offer-subscription-overview.md)
