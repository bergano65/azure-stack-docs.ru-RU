---
title: Добавление Commvault в Azure Stack Hub Marketplace | Документация Майкрософт
description: Узнайте, как добавить Commvault в Azure Stack Hub Marketplace.
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
ms.openlocfilehash: 9338a8d8b088f17c9f150e21959f78306108303f
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882170"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>Добавление Commvault в Azure Stack Hub Marketplace

В этой статье описано предложение Commvault Live Sync для обновления виртуальной машины восстановления, расположенной в отдельной единице масштабирования Azure Stack Hub. Вы можете скачать и предложить Commvault в качестве решения для резервного копирования и репликации пользователей. 

## <a name="notes-for-commvault"></a>Примечания для Commvault

- Пользователю необходимо установить программное обеспечение резервного копирования и репликации на виртуальной машине в исходной подписке Azure Stack Hub. Azure Site Recovery и Azure Backup могут предлагать автономное расположение для хранения резервных копий и образов восстановления. Перед загрузкой образов программного обеспечения для установки на Azure Stack Hub из следующих расположений для них нужно создать хранилище Служб восстановления в Azure. [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) и [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Возможно, вам понадобятся лицензии для стороннего программного обеспечения (если оно выбрано).
- Вашим пользователям может понадобиться помощник для подключения их источника и целевого объекта через виртуальный сетевой шлюз (VPN) или публичный IP-адрес на узле резервного копирования и репликации.
- Целевая подписка Azure Cloud или подписка для цели восстановления Azure Stack Hub.
- Целевая группа ресурсов и учетная запись хранилища BLOB-объектов на цели восстановления Azure Stack Hub.
- Некоторые решения требуют создания виртуальных машин по целевой подписке, которые должны выполнять 24x7x365, чтобы получать изменения от исходного сервера. В разделе [Резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault](../user/azure-stack-network-howto-backup-commvault.md), Commvault Live Sync создает целевые виртуальные машины восстановления во время первоначальной конфигурации и оставляет их в режиме ожидания (не работает, не оплачивается), пока во время цикла репликации не потребуются изменения.


## <a name="get-commvault-for-your-marketplace"></a>Получите Commvault для своего Marketplace

1. Откройте портал администрирования Azure Stack Hub.
2. Выберите **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

    ![Commvault для Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Введите `commvault`.
4. Выберите **Commvault Trial**. А затем нажмите **Скачать**.


## <a name="next-steps"></a>Дальнейшие действия

[Резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault](../user/azure-stack-network-howto-backup-commvault.md)

[Общие сведения о предложении служб в Azure Stack Hub](service-plan-offer-subscription-overview.md)
