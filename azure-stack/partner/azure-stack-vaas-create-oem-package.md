---
title: Рекомендации по выполнению проверки Azure Stack Hub
description: В этой статье описываются рекомендации по использованию проверки как услуги (VaaS).
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 647e724b179d994819032859c325bf711cb9d2ee
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143829"
---
# <a name="create-an-oem-package"></a>Создание пакета OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Пакет расширения Azure Stack Hub OEM — это механизм, с помощью которого содержимое от изготовителя оборудования добавляется в инфраструктуру Azure Stack Hub для использования при развертывании, а также в рабочих процессах, таких как обновление, расширение и замена на месте.

## <a name="creating-the-package"></a>Создание пакета

После создания и проверки пакет расширений OEM можно использовать в VaaS.  Прежде чем продолжить, убедитесь, что выполнены действия по [созданию пакета OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Пакет отправляется в Майкрософт вместе с результатами теста VaaS для подписи в рабочем процессе проверки пакета. Ниже подробно описаны инструкции по объединению созданных файлов в один ZIP-файл, который может использовать VaaS.

1. Определите следующее содержимое для пакета:
    - ZIP-файл, содержащий содержимое пакета.
    - Файл манифеста с именем `oemMetadata.xml`, которой должен по содержимому совпадать с файлом metadata.xml в корне содержимого пакета.

2. Выберите файлы содержимого и создайте ZIP-файл из содержимого:

    ![Содержимое ZIP-файла](media/vaas-create-oem-package-1.png)![Сжатие содержимого элемента](media/vaas-create-oem-package-2.png)

3. Переименуйте полученный файл, чтобы его было легко узнать.

## <a name="verifying-the-contents"></a>Проверка содержимого

Для проверки структуры ZIP-файла убедитесь, что в нем нет вложенных папок. TLD имеет заархивированное содержимое. Ниже приведена допустимая структура пакета.
> [!IMPORTANT]
> Архивация родительской папки вместо содержимого приведет к сбою подписи пакета.

![Правильно заархивированное содержимое пакета](media/vaas-create-oem-package-3.png)

Теперь ZIP-файл можно передать в VaaS, и Майкрософт может подписать его в рамках рабочего процесса проверки пакета.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка пакета OEM](azure-stack-vaas-validate-oem-package.md)
