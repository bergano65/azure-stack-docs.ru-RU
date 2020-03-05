---
title: Общие сведения об Azure Stack Hub Marketplace
description: Общие сведения об Azure Stack Hub Marketplace и элементах Marketplace.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: eeeda1d2b26ba6087e888ecd8ab847756c8e95fa
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699020"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Общие сведения об Azure Stack Hub Marketplace

Azure Stack Hub Marketplace — это коллекция служб, приложений и ресурсов, настроенных для Azure Stack Hub. Ресурсы включают виртуальные сети, виртуальные машины (ВМ), хранилище и др. Используйте Azure Stack Hub Marketplace, чтобы создавать ресурсы и развертывать новые приложения, а также искать и выбирать элементы, которые вы хотите использовать. Чтобы использовать элемент Marketplace, пользователям необходимо подписаться на предложение, которое предоставит им доступ к элементу.

Оператор Azure Stack Hub выбирает, какие элементы следует добавить (опубликовать) в Azure Stack Hub Marketplace. Опубликовать можно такие элементы, как базы данных, службы приложений и т. д. Публикация сделает их видимыми для всех пользователей. Вы можете публиковать созданные вами элементы или элементы из постоянно пополняемого [списка элементов Azure Marketplace](azure-stack-marketplace-azure-items.md). Элемент, опубликованный в Azure Stack Hub Marketplace, станет видимым для пользователей в течение пяти минут.

> [!CAUTION]  
> Все артефакты элемента коллекции, включая образы и JSON-файлы, доступны без аутентификации после внесения их в Azure Stack Hub Marketplace. Дополнительные сведения о публикации пользовательских элементов Marketplace см. в статье [Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Чтобы открыть Marketplace, на портале администрирования щелкните **+ Создать ресурс**.

![Создание ресурса на портале администрирования Azure Stack Hub](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Элементы Marketplace

Элемент Azure Stack Hub Marketplace — это служба, приложение или ресурс, которые пользователи могут скачать и использовать. Все элементы Azure Stack Hub Marketplace, включая такие административные элементы, как планы и предложения, видны всем вашим пользователям. Эти административные элементы не требуют подписки для просмотра, но пользователи не смогут с ними работать.

У каждого элемента Marketplace есть:

* Шаблон Resource Manager для подготовки ресурсов.
* Метаданные, например строки, значки и другие маркетинговые материалы.
* Форматирование сведений для отображения элемента на портале

Каждый элемент, опубликованный в Azure Stack Hub Marketplace, доступен в формате пакета коллекции Azure (AZPKG-файл). Ресурсы развертывания или среды выполнения (например код, ZIP-файлы с программным обеспечением или образы виртуальных машин) следует добавлять в Azure Stack Hub отдельно, а не в составе элемента Marketplace.

Начиная с версии 1803 и выше, Azure Stack Hub преобразует образы в разреженные файлы при скачивании из Azure или отправке пользовательских образов. Этот процесс увеличивает время добавления изображения, но позволяет сэкономить место и ускорить развертывание этих образов. Преобразование применяется только для новых образов. Существующие образы не изменяются.

## <a name="next-steps"></a>Дальнейшие действия

* [Скачивание существующих элементов Marketplace из Azure и их публикация в Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)  
* [Создание и публикация пользовательского элемента Azure Stack Hub Marketplace](azure-stack-create-and-publish-marketplace-item.md)
