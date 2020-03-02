---
title: Создание пакета расширения OEM
titleSuffix: Azure Stack Hub
description: Узнайте, как создать пакет расширения OEM в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 61e71e891f68118a89f0295528f6aab21507da1c
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625481"
---
# <a name="create-an-oem-package"></a>Создание пакета OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Пакет расширения OEM для Azure Stack Hub — это механизм, с помощью которого связанное с OEM содержимое добавляется в инфраструктуру Azure Stack Hub. Содержимое используется в процессах развертывания и эксплуатации, например при обновлении, расширении и замене полей.

## <a name="creating-the-package"></a>Создание пакета

После создания и проверки пакет расширений OEM можно использовать в VaaS. Прежде чем продолжить, убедитесь, что выполнены действия по [созданию пакета OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Пакет отправляется в Майкрософт вместе с результатами теста VaaS для подписи в рабочем процессе проверки пакета. Ниже подробно описаны инструкции по объединению созданных файлов в один ZIP-файл, который может использовать VaaS.

1. Определите следующее содержимое для пакета:
    - ZIP-файл, вмещающий содержимое пакета.
    - Файл манифеста с именем `oemMetadata.xml`, который должен по содержимому совпадать с файлом `metadata.xml` в корне содержимого пакета.

2. Выберите файлы содержимого и создайте ZIP-файл.

    ![Содержимое ZIP-файла при создании пакета расширения OEM](media/vaas-create-oem-package-1.png) ![Сжимает содержимое элемента при создании пакета расширения OEM](media/vaas-create-oem-package-2.png)

3. Переименуйте полученный файл, чтобы его было легко узнать.

## <a name="verifying-the-contents"></a>Проверка содержимого

Для проверки структуры ZIP-файла убедитесь, что в нем нет вложенных папок. TLD имеет заархивированное содержимое. Ниже приведена допустимая структура пакета:

> [!IMPORTANT]
> Архивация родительской папки вместо содержимого приведет к сбою подписи пакета.

![Правильно заархивированное содержимое пакета при создании пакета расширения OEM](media/vaas-create-oem-package-3.png)

Теперь ZIP-файл можно передать в VaaS, и Майкрософт может подписать его в рамках рабочего процесса проверки пакета.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка пакета OEM](azure-stack-vaas-validate-oem-package.md)
