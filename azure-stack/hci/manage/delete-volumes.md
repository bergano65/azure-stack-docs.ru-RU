---
title: Удаление томов в Azure Stack HCI
description: Сведения о том, как удалять тома в Azure Stack HCI с помощью Windows Admin Center и PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/17/2020
ms.openlocfilehash: cf556a9b6c130907e8607d8e5b9436b71756a3d4
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511901"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Удаление томов в Azure Stack HCI

> Область применения: Windows Server 2019

В этой статье приведены инструкции по удалению томов в кластере [Локальных дисковых пространств](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) с помощью Windows Admin Center.

Посмотрите краткое видео о том, как удалить том с помощью Windows Admin Center.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Удаление тома с помощью Windows Admin Center

1. В Windows Admin Center подключитесь к кластеру Локальных дисковых пространств и выберите элемент **Тома** на панели **Инструменты**.
2. На странице **Тома** выберите вкладку **Inventory** (Ресурсы) и щелкните том, который решили удалить.
3. В верхней части страницы сведений о томе выберите команду **Удалить**.
4. В диалоговом окне подтверждения установите флажок для подтверждения того, что вы хотите удалить том. Затем выберите команду **Удалить**.

## <a name="delete-volumes-using-powershell"></a>Удаление томов с помощью PowerShell

Для удаления томов в хранилище "Локальные дисковые пространства", используйте командлет **Remove-VirtualDisk**. Этот командлет используется для удаления объекта **VirtualDisk** и освобождения использованного пространства для пула носителей, который предоставляет объект **VirtualDisk**.

Сначала запустите PowerShell на компьютере управления и выполните командлет **Get-VirtualDisk** с параметром **CimSession**, который является именем узла сервера или кластера Локальных дисковых пространств, например *clustername.microsoft.com*. 

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

В результате будет возвращен список возможных значений для параметра **-FriendlyName**, которые соответствуют именам томов в кластере.

### <a name="example"></a>Пример

Чтобы удалить зеркальный том с именем *Volume1*, выполните следующую команду в PowerShell:

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

Вам будет предложено подтвердить свое намерение выполнить действие и стереть все данные, содержащиеся в томе. Выберите "Да" или "Нет".

   > [!WARNING]
   > Это действие отменить невозможно. В этом примере показано безвозвратное удаление объекта тома **VirtualDisk**.

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по другим важным задачам управления хранилищем вы найдете в следующих статьях:

- [Планирование томов в Локальных дисковых пространствах](../concepts/plan-volumes.md)
- [Создание томов в Локальных дисковых пространствах](create-volumes.md)
- [Расширение томов в Локальных дисковых пространствах](extend-volumes.md)