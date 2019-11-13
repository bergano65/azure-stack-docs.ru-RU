---
title: Скачивание элементов Marketplace из Azure и публикация в Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как скачать элементы Marketplace из Azure и опубликовать их в Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 095744322937a34dffd680b886fd4b06ca65d7d6
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618275"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>Скачивание существующих элементов Marketplace из Azure и их публикация в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Как оператор облака вы можете скачивать элементы из Azure Marketplace и делать их доступными в Azure Stack. Элементы, которые вы можете выбрать, находятся в рекомендуемом списке элементов Azure Marketplace, которые предварительно протестированы и поддерживаются в Azure Stack. В этот список часто добавляются дополнительные элементы, поэтому его следует периодически проверять.

Существует два сценария для подключения к Azure Marketplace.

- **Сценарий с подключением** — это сценарий, в котором требуется, чтобы ваша среда Azure Stack была подключена к Интернету. Для поиска и скачивания элементов используйте портал Azure Stack.
- **Сценарий без подключения или с частичным подключением** — вам потребуется доступ к Интернету через средство синдикации Marketplace для скачивания элементов Marketplace. Затем вы переносите скачанные элементы в свою отключенную установку Azure Stack. В этом сценарии используется PowerShell.

Полный список элементов marketplace, которые можно скачать, приведен в разделе [Элементы Azure Marketplace, доступные для Azure Stack](azure-stack-marketplace-azure-items.md). Список последних добавленных, удаленных и обновленных элементов Azure Stack Marketplace приведен в статье [Изменения в Azure Stack Marketplace](azure-stack-marketplace-changes.md).

## <a name="connected-scenario"></a>Сценарий с подключением

Если у Azure Stack есть подключение к Интернету, для скачивания элементов marketplace можно использовать портал администратора.

### <a name="prerequisites"></a>Предварительные требования

Экземпляр развертывания Azure Stack должен быть подключен к Интернету и зарегистрирован [в Azure](azure-stack-registration.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Использование портала для загрузки элементов из marketplace
  
1. Войдите на портал администратора Azure Stack.

2. Проверьте объем свободного места перед скачиванием элементов из marketplace. Позже, когда вы будете выбирать элементы для скачивания, вы можете сравнить размер скачиваемых элементов с доступной емкостью хранилища. Если емкость ограничена, рассмотрите варианты [управления доступным пространством](azure-stack-manage-storage-shares.md#manage-available-space).

    Чтобы просмотреть доступное пространство, в разделе **Управление регионами** выберите регион, который вы хотите изучить, а затем нажмите **Поставщики ресурсов** > **Хранилище**.

    ![Просмотр места для хранения на портале администрирования Azure Stack](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Откройте Azure Stack Marketplace и подключитесь к Azure. Для этого выберите службу **Marketplace management**, а затем щелкните **Marketplace items** (Элементы Marketplace) и выберите **Add from Azure** (Добавить из Azure).

    ![Добавление элементов Marketplace из Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. На портале отображается список элементов, доступных для скачивания из Azure Marketplace. Вы можете отфильтровать продукты по имени, издателю и/или типу продукта. Вы также можете щелкнуть каждый элемент, чтобы просмотреть его описание и дополнительную информацию, включая его размер.

    ![Список элементов Azure Marketplace ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

5. Выберите нужный элемент из списка и щелкните **Скачать**. Время скачивания может различаться.

    ![Скачивание элемента Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image04.png)

    Когда скачивание завершится, вы можете развернуть новый элемент Marketplace от имени оператора или пользователя Azure Stack.

6. Чтобы развернуть скачанный элемент Marketplace, щелкните **+ Создать ресурс**, выберите новый элемент, выполнив поиск в категориях. Выберите элемент, чтобы начать процесс развертывания. Процесс различается для разных элементов marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Сценарий отключенного или частично подключенного режима

Если Azure Stack работает в отключенном режиме, используйте PowerShell и *средство синдикации Marketplace*, чтобы скачать элементы Marketplace на компьютер с подключением к Интернету. Затем их можно передать в среду Azure Stack. В отключенной среде вам не удастся скачать элементы Marketplace через портал Azure Stack.

Средство синдикации marketplace может также использоваться в подключенном сценарии.

Этот сценарий состоит из двух частей:

- **Часть 1**. Скачивание из Azure Marketplace. На компьютере с доступом к Интернету настройте PowerShell, скачайте инструмент синдикации, а затем скачайте элементы из Azure Marketplace.  
- **Часть 2**. Передача и публикация в Azure Stack Marketplace. Переместите файлы, скачанные в среду Azure Stack, импортируйте их в Azure Stack и опубликуйте в Azure Stack Marketplace.  

### <a name="prerequisites"></a>Предварительные требования

- Подключенная к Интернету среда (не обязательно Azure Stack). Вам потребуется подключение, чтобы получить из Azure список продуктов со сведениями о них и скачать все необходимое в локальную среду. Когда эти операции завершатся, для остальной части процедуры подключение к Интернету не требуется. При этом создается каталог элементов, которые были скачаны ранее, чтобы использовать их в отключенной среде.

- USB-носитель или внешний диск для подключения к отключенной среде и переноса всех необходимых артефактов.

- Отключенная среда Azure Stack, которая соответствует следующим условиям:
  - Развертывание Azure Stack должно быть [зарегистрировано в Azure](azure-stack-registration.md).
  - На компьютере с подключением к Интернету необходимо установить модуль **PowerShell для Azure Stack версии 1.2.11** или более поздней. При необходимости см. [инструкции по установке модулей PowerShell для Azure Stack](azure-stack-powershell-install.md).
  - Чтобы включить импорт скачанных элементов marketplace, необходимо настроить среду [​​PowerShell для оператора Azure Stack](azure-stack-powershell-configure-admin.md).
  - Клонируйте репозиторий [средств Azure Stack](https://github.com/Azure/AzureStack-Tools) с сайта GitHub.

- У вас должна быть [учетная запись хранения](azure-stack-manage-storage-accounts.md) в Azure Stack, которая имеет общедоступный контейнер (в виде BLOB-объекта хранилища). Используйте контейнер в качестве временного хранилища для файлов коллекции элементов marketplace. Если вы не знакомы с учетными записями хранения и контейнерами, см. статью [Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью портала Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) в документации Azure.

- Средство синдикации marketplace скачивается во время первой процедуры.

- Вы можете установить [AzCopy](/azure/storage/common/storage-use-azcopy), чтобы ускорить скачивание, но это необязательно.

После регистрации можно проигнорировать следующее сообщение, которое отображается в колонке управления Marketplace, так как оно не имеет отношения к режиму использования без подключения к Интернету.

[![Незарегистрированное сообщение](media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "Незарегистрированное сообщение")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Скачивание элементов marketplace с помощью средства синдикации marketplace

> [!IMPORTANT]
> Обязательно скачивайте средство синдикации Marketplace каждый раз вместе с элементами Marketplace, если используется сценарий без подключения к Интернету. В этот скрипт часто вносятся изменения и для каждого нового скачивания следует использовать самую свежую версию.

1. На компьютере с подключением к Интернету откройте консоль PowerShell в качестве администратора.

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, в PowerShell запустите `Add-AzureRmAccount` без каких-либо параметров. Вам будет предложено ввести учетные данные для входа в Azure и, возможно, выполнить двухфакторную аутентификацию в зависимости от конфигурации учетной записи.

   [!include[Remove Account](../../includes/remove-account.md)]

3. Если у вас несколько подписок, выполните следующую команду, чтобы выбрать подписку, которая использовалась для регистрации:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Скачайте последнюю версию средства синдикации marketplace с помощью следующего скрипта:  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Импортируйте модуль синдикации и запустите средство, выполнив следующие команды. Замените `Destination folder path` на расположение файлов, скачанных из Azure Marketplace.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Обратите внимание, что `Export-AzSOfflineMarketplaceItem` имеет дополнительный флаг `-cloud`, который определяет облачную среду. По умолчанию это флаг **azurecloud**.

6. После запуска средства появится примерно такой экран, как на следующем изображении, с полным списком доступных элементов Marketplace:

   [![Всплывающее окно с элементами Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "Элементы Azure Marketplace")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Если вы еще не установили средства службы хранилища Azure, появится следующее сообщение. Чтобы установить эти средства, обязательно скачайте [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy):

   ![Средства хранилища](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. Выберите элемент, который вам нужно скачать, и запишите его **версию**. Вы можете выбрать несколько образов, удерживая нажатой клавишу **CTRL**. Вы создадите ссылку на *версию* при импорте элемента в следующей процедуре.

   Кроме того, вы можете отфильтровать список образов с помощью параметра **Добавить условия**.

9. Нажмите кнопку **ОК**, прочтите и примите юридические условия.

10. Время скачивания зависит от размера элемента. После завершения скачивания элемент доступен в папке, указанной в скрипте. Скачанный пакет содержит VHD-файл (для виртуальных машин) или ZIP-файл (для расширений виртуальных машин). Она также может содержать пакет коллекции в формате *AZPKG* (это просто ZIP-файл).

11. Если скачивание завершается сбоем, вы можете повторить попытку, повторно запустив следующий командлет PowerShell:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Перед повторным выполнением удалите папку продукта, в которую не удалось выполнить скачивание. Например, если скачивание скрипта в `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` закончилось сбоем, удалите папку `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, а затем повторно запустите командлет.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>Импорт скачанного пакета и его публикация в Azure Stack Marketplace с помощью PowerShell

1. Необходимо локально переместить файлы, [загруженные ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items), чтобы они были доступны в среде Azure Stack. Средство синдикации Marketplace также должно быть доступно в среде Azure Stack, так как оно используется для операции импорта.

   Пример структуры папки приведен на рисунке ниже. В папке `D:\downloadfolder` содержатся все скачанные элементы marketplace. Каждая вложенная папка представляет элемент Marketplace (например, `microsoft.custom-script-linux-arm-2.0.3`) и ее имя соответствует идентификатору продукта. Внутри каждой вложенной папки содержится скачанное содержимое элемента Marketplace.

   [![Структура каталогов для скачивания из Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Структура каталогов для скачивания из Marketplace")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Инструкции в [этой статье](azure-stack-powershell-configure-admin.md) позволяют настроить сеанс PowerShell для оператора Azure Stack.

3. Импортируйте модуль синдикации, а затем запустите средство синдикации marketplace, выполнив следующий скрипт:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Параметр `-origin` позволяет указать папку верхнего уровня, где хранятся все скачанные продукты (например, `"D:\downloadfolder"`).

   Параметр `-AzsCredential` не обязателен. Он используется для обновления маркера доступа, если срок его действия истек. Если параметр `-AzsCredential` не указан и срок действия маркера истекает, появится запрос на ввод учетных данных оператора.

    > [!NOTE]  
    > AD FS поддерживает только интерактивную проверку подлинности с удостоверениями пользователей. Если требуется объект учетных данных, необходимо использовать субъект-службу (SPN). Дополнительные сведения о том, как с помощью Azure Stack и AD FS настроить субъект-службу в качестве службы управления удостоверениями, см. в разделе [Управление субъектом-службой AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. После успешного выполнения скрипта элемент станет доступен в Azure Stack Marketplace.

## <a name="next-steps"></a>Дополнительная информация

- [Добавление пользовательского образа виртуальной машины](azure-stack-add-vm-image.md)
- [Создание и публикация пользовательского элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)
