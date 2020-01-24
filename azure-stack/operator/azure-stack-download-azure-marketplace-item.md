---
title: Скачивание элементов Marketplace из Azure и публикация в Azure Stack Hub | Документация Майкрософт
description: Из этой статьи вы узнаете, как скачать элементы Marketplace из Azure и опубликовать их в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 80cf9d192be07f951ee959c7a83419bb16bd2bbb
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022947"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Скачивание элементов Marketplace в Azure Stack Hub 

Как оператор облака вы можете скачивать элементы из Marketplace в Azure Stack Hub и делать их доступными для всех пользователей в среде Azure Stack Hub. Элементы, которые вы можете выбрать, находятся в рекомендуемом списке элементов Azure Marketplace, которые предварительно протестированы и поддерживаются в Azure Stack Hub. В этот список часто добавляются дополнительные элементы, поэтому его следует периодически проверять.

Существует два сценария для скачивания продуктов из Marketplace.

- **Сценарий с подключением**. При этом необходимо, чтобы среда Azure Stack Hub была подключена к Интернету. Для поиска и скачивания элементов используется портал администрирования Azure Stack Hub.
- **Сценарий без подключения или с частичным подключением**. Для скачивания элементов Marketplace вам потребуется доступ к Интернету через средство синдикации Marketplace. Затем вы переносите скачанные элементы в свою отключенную установку Azure Stack Hub. В этом сценарии используется PowerShell.

Полный список элементов marketplace для Azure Stack Hub, которые можно скачать, приведен в [этом разделе](azure-stack-marketplace-azure-items.md). Список последних добавленных, удаленных и обновленных элементов Azure Stack Hub Marketplace приведен в статье об [изменениях в Azure Stack Hub Marketplace](azure-stack-marketplace-changes.md).

> [!NOTE]
> Каталоги будут разными в зависимости от того, к какому облаку подключена система Azure Stack Hub. Облачная среда определяется подпиской Azure, которую вы используете для регистрации Azure Stack Hub.

## <a name="connected-scenario"></a>Сценарий с подключением

Если в среде Azure Stack Hub есть подключение к Интернету, для скачивания элементов Marketplace можно использовать портал администрирования.

### <a name="prerequisites"></a>предварительные требования

Экземпляр развертывания Azure Stack Hub должен быть подключен к Интернету и зарегистрирован в Azure.

### <a name="use-the-portal-to-download-marketplace-items"></a>Использование портала для загрузки элементов из marketplace

1. Войдите на портал администрирования Azure Stack Hub.

2. Проверьте объем свободного места перед скачиванием элементов из marketplace. Позже, когда вы будете выбирать элементы для скачивания, вы можете сравнить размер скачиваемых элементов с доступной емкостью хранилища. Если емкость ограничена, рассмотрите возможности для  [управления доступным пространством](azure-stack-manage-storage-shares.md#manage-available-space).

   Чтобы просмотреть доступное пространство, в разделе  **Region management** (Управление регионами) выберите регион, который вы хотите изучить, а затем щелкните  **Поставщики ресурсов** > **Хранилище**.

   ![Просмотр места для хранения на портале администрирования Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Откройте Azure Stack Hub Marketplace и подключитесь к Azure. Для этого выберите службу  **управления Marketplace** , а затем щелкните  **Элементы Marketplace** и выберите  **Add from Azure** (Добавить из Azure).

   ![Добавление элементов Marketplace из Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Для элемента в каждой строке также отображается текущая доступная версия. Если доступно несколько версий элемента Marketplace, в столбце **Версия** отображается значение**Несколько**. Вы можете щелкнуть каждый элемент, чтобы просмотреть его описание и дополнительную информацию, включая его размер скачивания:

   ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Если для версии элемента отображается значение **Несколько**, вы можете щелкнуть этот элемент, а затем выбрать конкретную версию в раскрывающемся меню выбора версии:

   ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Выберите нужный элемент и щелкните  **Скачать**. Время скачивания будет разным в зависимости от сетевого подключения. Когда скачивание завершится, вы можете развернуть новый элемент Marketplace от имени оператора или пользователя Azure Stack Hub.

7. Чтобы развернуть скачанный элемент, щелкните  **+ Создать ресурс** и выберите категорию для нового элемента Marketplace. Теперь выберите элемент, чтобы начать процесс развертывания. Процесс различается для разных элементов marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Сценарий отключенного или частично подключенного режима

Если Azure Stack Hub не имеет подключения к Интернету или это подключение ограничено, примените PowerShell и  *средство синдикации Marketplace*, чтобы скачать элементы Marketplace на компьютер с подключением к Интернету. Затем передайте их в среду Azure Stack Hub. В среде без подключения вам не удастся скачать элементы Marketplace через портал Azure Stack Hub.

Средство синдикации marketplace может также использоваться в подключенном сценарии.

Этот сценарий состоит из двух частей:

- **Часть 1**. Скачайте элементы из Marketplace. На компьютере с доступом к Интернету настройте PowerShell, скачайте инструмент синдикации, а затем скачайте элементы из Azure Marketplace.
- **Часть 2**. Передайте их в Azure Stack Hub Marketplace и опубликуйте. Переместите файлы, скачанные в среду Azure Stack Hub, импортируйте их в Azure Stack Hub и опубликуйте в Azure Stack Hub Marketplace.

### <a name="prerequisites"></a>предварительные требования

- Подключенная к Интернету среда (не обязательно Azure Stack Hub). Вам потребуется подключение, чтобы получить из Azure список продуктов со сведениями о них и скачать все необходимое в локальную среду. После выполнения этих операций вам не потребуется подключение к Интернету для остальной части процедуры. При этом создается каталог элементов, которые были скачаны ранее, чтобы использовать их в отключенной среде.

- Съемные носители для подключения к отключенной среде и переноса всех необходимых артефактов.

- Отключенная среда Azure Stack Hub, которая соответствует следующим условиям:

  - Развертывание Azure Stack Hub зарегистрировано в Azure.

  - На компьютере с подключением к Интернету установлен  **модуль PowerShell для Azure Stack Hub версии 1.2.11**  или более поздней. При необходимости см.  [инструкции по установке модулей PowerShell для Azure Stack Hub](azure-stack-powershell-install.md).

  - Чтобы обеспечить импорт скачанных элементов Marketplace, необходимо настроить  [среду ​​PowerShell для оператора Azure Stack Hub](azure-stack-powershell-configure-admin.md) .

  - Клонируйте репозиторий  [средств Azure Stack Hub](https://github.com/Azure/AzureStack-Tools) с сайта GitHub.

- У вас должна быть  [учетная запись хранения](azure-stack-manage-storage-accounts.md) в Azure Stack Hub с общедоступным контейнером (большой двоичный объект хранилища). Используйте контейнер в качестве временного хранилища для файлов коллекции элементов marketplace. Если вы не знакомы с учетными записями хранения и контейнерами, см. статью  [Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью портала Azure](/azure/storage/blobs/storage-quickstart-blobs-portal) в документации по Azure.

- Средство синдикации marketplace скачивается во время первой процедуры.

- Вы можете установить [AzCopy](/azure/storage/common/storage-use-azcopy), чтобы ускорить скачивание, но это необязательно.

После регистрации можно проигнорировать следующее сообщение, которое отображается в колонке управления Marketplace, так как оно не имеет отношения к режиму использования без подключения к Интернету.

![Управление Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Скачивание элементов marketplace с помощью средства синдикации marketplace

> [!IMPORTANT]
> Обязательно скачивайте средство синдикации Marketplace каждый раз вместе с элементами Marketplace, если используется сценарий без подключения к Интернету. В этот скрипт часто вносятся изменения и для каждого нового скачивания следует использовать самую свежую версию.

1. На компьютере с подключением к Интернету откройте консоль PowerShell в качестве администратора.

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack Hub. Чтобы добавить учетную запись, в PowerShell выполните команду **Add-AzureRmAccount** без параметров. Вам будет предложено ввести учетные данные для входа в Azure и, возможно, выполнить двухфакторную аутентификацию, если это настроено для учетной записи.

   > [!NOTE]
   > Если истек срок сеанса, изменен пароль или вы хотите переключиться на другую учетную запись, перед входом с помощью **Add-AzureRmAccount** выполните следующий командлет: **Remove-AzureRmAccount-Scope Process**.

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

5. Импортируйте модуль синдикации и запустите средство, выполнив следующую команду.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. Чтобы экспортировать элементы Marketplace, например образы виртуальных машин, расширения или шаблоны решений, выполните приведенную ниже команду. Замените путь к папке назначения реальным расположением для хранения файлов, скачанных из Azure Marketplace.

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Чтобы экспортировать поставщики ресурсов или службы PaaS, выполните следующую команду.

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   Параметр `-azCopyDownloadThreads` не обязателен. Его следует использовать только в сети с низкой пропускной способностью и загрузки категории "Премиум". Этот параметр позволяет задать количество одновременных операций в AzCopy. В сети с низкой пропускной способностью можно задать меньшее количество, чтобы избежать сбоев из-за конкуренции за ресурсы. Дополнительные сведения можно найти в [этой статье Azure](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start).

   Параметр `-azureContext` также является необязательным. Если контекст Azure не указан, командлет будет использовать контекст Azure по умолчанию.

7. После запуска средства появится примерно такой экран, как на следующем изображении, с полным списком доступных элементов Marketplace:

   ![Элементы Marketplace](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Если доступно несколько версий элемента Marketplace, в столбце  **Версия** отображается значение  **Multiple versions** (Несколько версий). Если для элемента отображается значение  **Multiple versions** (Несколько версий), вы можете щелкнуть этот элемент и выбрать конкретную версию в окне выбора версии.

9. Выберите элемент, который вам нужно скачать, и запишите его  **версию**. Вы можете выбрать несколько образов, удерживая нажатой клавишу  **CTRL** . Вам нужно будет указать  *версию* при импорте элемента в следующей процедуре.

    Кроме того, вы можете отфильтровать список образов с помощью параметра  **Добавить условия** .

10. Если вы еще не установили средства службы хранилища Azure, появится следующее сообщение. Чтобы установить эти средства, обязательно скачайте  [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy).

    ![Средства хранилища](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Щелкните  **ОК**, прочтите и примите юридические условия.

12. Время скачивания зависит от размера элемента. После завершения скачивания элемент доступен в папке, указанной в скрипте. Скачанный пакет содержит VHD-файл (для виртуальных машин) или ZIP-файл (для расширений виртуальных машин и поставщиков ресурсов). Он также может содержать пакет коллекции с расширением  *AZPKG* (это обычный ZIP-файл).

13. Если скачивание завершается сбоем, вы можете повторить попытку, повторно запустив следующий командлет PowerShell:

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Перед повторным выполнением удалите папку продукта, в которую не удалось выполнить скачивание. Например, если скрипт загрузки завершается сбоем при скачивании в **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1**, удалите папку **D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1**  и повторно выполните командлет.

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Импорт скачанного пакета и его публикация в Azure Stack Hub Marketplace с помощью PowerShell

1. Необходимо переместить  [скачанные ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items) файлы в локальной среде так, чтобы они были доступны среде Azure Stack Hub. Средство синдикации Marketplace также должно быть доступно в среде Azure Stack Hub, так как оно используется для операции импорта.

   На следующем рисунке показан пример структуры папки. **D:\downloadfolder** содержит все скачанные элементы Marketplace. Каждая вложенная папка представляет собой элемент Marketplace (например,  **microsoft.custom-script-linux-arm-2.0.3**) и ее имя соответствует идентификатору продукта. Внутри каждой вложенной папки содержится скачанное содержимое элемента Marketplace.

   ![Структура каталогов для скачивания из Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Следуйте инструкциям из  [этой статьи](azure-stack-powershell-configure-admin.md) , чтобы настроить сеанс PowerShell для оператора Azure Stack Hub.

3. Импортируйте модуль синдикации, а затем запустите средство синдикации marketplace, выполнив следующий скрипт:

   ```powershell
   $credential = Get-Credential -Message "Enter the Azure Stack Hub operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Параметр `-origin` позволяет указать папку верхнего уровня, где хранятся все скачанные продукты (например,  `"D:\downloadfolder"`).

   Параметр `-AzsCredential` является необязательным. Он используется для обновления маркера доступа, если срок его действия истек. Если параметр `-AzsCredential` не указан и срок действия маркера истекает, появляется запрос на ввод учетных данных оператора.

   > [!NOTE]
   > AD FS поддерживает только интерактивную проверку подлинности с удостоверениями пользователей. Если требуется объект учетных данных, необходимо использовать субъект-службу (SPN). Дополнительные сведения о том, как с помощью Azure Stack Hub и AD FS настроить субъект-службу в качестве службы управления удостоверениями, см. в разделе  [Управление субъектом-службой AD FS](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. После успешного выполнения скрипта элемент станет доступен в Azure Stack Hub Marketplace.
