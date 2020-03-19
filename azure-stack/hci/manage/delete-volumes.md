---
title: Удаление томов в Azure Stack HCI
description: Сведения о том, как удалять тома в Azure Stack HCI с помощью Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025980"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Удаление томов в Azure Stack HCI

> Область применения: Windows Server 2019

В этой статье приведены инструкции по удалению томов в кластере [Локальных дисковых пространств](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) с помощью Windows Admin Center.

Посмотрите краткое видео о том, как удалить том.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Удаление тома с помощью Windows Admin Center

1. В Windows Admin Center подключитесь к кластеру Локальных дисковых пространств и выберите элемент **Тома** на панели **Инструменты**.
2. На странице **Тома** выберите вкладку **Inventory** (Ресурсы) и щелкните том, который решили удалить.
3. В верхней части страницы сведений о томе выберите команду **Удалить**.
4. В диалоговом окне подтверждения установите флажок для подтверждения того, что вы хотите удалить том. Затем выберите команду **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по другим важным задачам управления хранилищем вы найдете в следующих статьях:

- [Планирование томов в Локальных дисковых пространствах](/windows-server/storage/storage-spaces/plan-volumes)
- [Создание томов в Локальных дисковых пространствах](/windows-server/storage/storage-spaces/create-volumes)
- [Расширение томов в Локальных дисковых пространствах](/windows-server/storage/storage-spaces/resize-volumes)