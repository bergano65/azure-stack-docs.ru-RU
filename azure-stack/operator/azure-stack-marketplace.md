---
title: Обзор Azure Stack Marketplace | Документация Майкрософт
description: Обзор Azure Stack Marketplace и элементов Marketplace.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: da8fdf20446e5ed60271e236f44939d439118b4b
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534094"
---
# <a name="azure-stack-marketplace-overview"></a>Общие сведения об Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack Marketplace — это коллекция служб, приложений и ресурсов, настроенных для Azure Stack. Ресурсы включают виртуальные сети, виртуальные машины (ВМ), хранилище и др. Используйте Azure Stack Marketplace, чтобы создавать ресурсы и развертывать новые приложения, а также искать и выбирать элементы, которые вы хотите использовать. Чтобы использовать элемент Marketplace, пользователям необходимо подписаться на предложение, которое предоставит им доступ к элементу.

Как оператор Azure Stack вы определяете, какие элементы следует добавить (опубликовать) в Azure Stack Marketplace. Опубликовать можно такие элементы, как базы данных, службы приложений и т. д. Публикация сделает их видимыми для всех пользователей. Вы можете публиковать созданные вами элементы или элементы из постоянно пополняемого [списка элементов Azure Marketplace](azure-stack-marketplace-azure-items.md). После публикации элемента в Azure Stack Marketplace он станет видимым для пользователей в течение пяти минут.

> [!CAUTION]  
> Все артефакты элемента коллекции, включая образы и JSON-файлы, доступны без аутентификации после внесения их в Azure Stack Marketplace. Дополнительные сведения о публикации пользовательских элементов Marketplace см. в статье [Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Чтобы открыть Marketplace, на портале администрирования щелкните **+ Создать ресурс**.

![Создание ресурса на портале администрирования Azure Stack](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Элементы Marketplace

Элемент Azure Stack Marketplace — это служба, приложение или ресурс, которые пользователи могут скачать и использовать. Все элементы Azure Stack Marketplace, включая такие административные элементы, как планы и предложения, видны всем вашим пользователям. Эти административные элементы не требуют подписки для просмотра, но пользователи не смогут с ними работать.

У каждого элемента Marketplace есть:

* Шаблон Resource Manager для подготовки ресурсов.
* Метаданные, например строки, значки и другие маркетинговые материалы.
* Форматирование сведений для отображения элемента на портале

Каждый элемент, опубликованный в Azure Stack Marketplace, доступен в формате пакета коллекции Azure (AZPKG-файл). Ресурсы развертывания или среды выполнения (например код, ZIP-файлы с программным обеспечением или образы ВМ) следует добавлять в Azure Stack отдельно, а не в составе элемента Marketplace.

Начиная с версии 1803 и выше, Azure Stack преобразует изображения в разреженные файлы при скачивании из Azure или отправке пользовательских образов. Этот процесс увеличивает время добавления изображения, но позволяет сэкономить место и ускорить развертывание этих образов. Преобразование применяется только для новых образов. Существующие образы не изменяются.

## <a name="next-steps"></a>Дополнительная информация

* [Скачивание существующих элементов Marketplace из Azure и их публикация в Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [Создание и публикация пользовательского элемента Azure Stack Marketplace](azure-stack-create-and-publish-marketplace-item.md)
