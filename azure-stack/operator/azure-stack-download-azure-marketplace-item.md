---
title: Скачивание элементов marketplace из Azure и их публикация в Azure Stack Hub
description: Из этой статьи вы узнаете, как скачать элементы Marketplace из Azure и опубликовать их в Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: a4882ca540b2a72d77195ee12a5d5ae0be87931d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700023"
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

### <a name="prerequisites"></a>Предварительные требования

Экземпляр развертывания Azure Stack Hub должен быть подключен к Интернету и зарегистрирован в Azure.

### <a name="use-the-portal-to-download-marketplace-items"></a>Использование портала для загрузки элементов из marketplace

1. Войдите на портал администрирования Azure Stack Hub.

2. Проверьте объем свободного места перед скачиванием элементов из marketplace. Позже, когда вы будете выбирать элементы для скачивания, вы можете сравнить размер скачиваемых элементов с доступной емкостью хранилища. Если емкость ограничена, рассмотрите варианты [управления доступным пространством](azure-stack-manage-storage-shares.md#manage-available-space).

   Чтобы просмотреть доступное пространство, в разделе **Управление регионами** выберите регион, который вы хотите изучить, а затем нажмите **Поставщики ресурсов** > **Хранилище**.

   ![Просмотр места для хранения на портале администрирования Azure Stack Hub](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Откройте Azure Stack Hub Marketplace и подключитесь к Azure. Для этого выберите службу **Marketplace management**, а затем щелкните **Marketplace items** (Элементы Marketplace) и выберите **Add from Azure** (Добавить из Azure).

   ![Добавление элементов Marketplace из Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Для элемента в каждой строке также отображается текущая доступная версия. Если доступно несколько версий элемента Marketplace, в столбце **Версия** отображается значение**Несколько**. Вы можете щелкнуть каждый элемент, чтобы просмотреть его описание и дополнительную информацию, включая его размер скачивания:

   ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Если для версии элемента отображается значение **Несколько**, вы можете щелкнуть этот элемент, а затем выбрать конкретную версию в раскрывающемся меню выбора версии:

   ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Выберите нужный элемент из списка и щелкните **Скачать**. Время скачивания будет разным в зависимости от сетевого подключения. Когда скачивание завершится, вы можете развернуть новый элемент Marketplace от имени оператора или пользователя Azure Stack Hub.

7. Чтобы развернуть скачанный элемент Marketplace, щелкните **+ Создать ресурс**, выберите новый элемент, выполнив поиск в категориях. Теперь выберите элемент, чтобы начать процесс развертывания. Процесс различается для разных элементов marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Сценарий отключенного или частично подключенного режима

Если Azure Stack Hub не имеет подключения к Интернету или это подключение ограничено, примените PowerShell и *средство синдикации Marketplace*, чтобы скачать элементы Marketplace на компьютер с подключением к Интернету. Затем передайте их в среду Azure Stack Hub. В среде без подключения вам не удастся скачать элементы Marketplace через портал Azure Stack Hub.

Средство синдикации marketplace может также использоваться в подключенном сценарии.

Этот сценарий состоит из двух частей:

- **Часть 1**. Скачайте элементы из Marketplace. На компьютере с доступом к Интернету настройте PowerShell, скачайте инструмент синдикации, а затем скачайте элементы из Azure Marketplace.
- **Часть 2**. Передайте их в Azure Stack Hub Marketplace и опубликуйте. Переместите файлы, скачанные в среду Azure Stack Hub, и опубликуйте их в Azure Stack Hub Marketplace.

### <a name="prerequisites"></a>Предварительные требования

- Подключенная к Интернету среда (не обязательно Azure Stack Hub). Вам потребуется подключение, чтобы получить из Azure список продуктов со сведениями о них и скачать все необходимое в локальную среду. После выполнения этих операций вам не потребуется подключение к Интернету для остальной части процедуры. При этом создается каталог элементов, которые были скачаны ранее, чтобы использовать их в отключенной среде.

- Съемные носители для подключения к отключенной среде и переноса всех необходимых артефактов.

- Отключенная среда Azure Stack Hub, которая соответствует следующим условиям:

  - Развертывание Azure Stack Hub зарегистрировано в Azure.

  - На компьютере с подключением к Интернету необходимо установить модуль **PowerShell для Azure Stack Hub версии 1.2.11** или более поздней. При необходимости см. [инструкции по установке модулей PowerShell для Azure Stack Hub](azure-stack-powershell-install.md).

  - Чтобы включить импорт скачанных элементов marketplace, необходимо настроить среду [​​PowerShell для оператора Azure Stack Hub](azure-stack-powershell-configure-admin.md).

- Скачайте модуль Azs.Syndication.Admin из коллекции PowerShell с помощью следующей команды.
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```

После регистрации Azure Stack следующее сообщение, которое отображается в колонке управления Marketplace, можно проигнорировать, так как оно не имеет отношения к варианту использования без подключения к Интернету.

![Управление Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Скачивание элементов marketplace с помощью средства синдикации marketplace

> [!IMPORTANT]
> Обязательно скачивайте средство синдикации Marketplace каждый раз вместе с элементами Marketplace, если используется сценарий без подключения к Интернету. В это средство часто вносятся изменения, и для каждого нового скачивания следует использовать последнюю версию.

1. На компьютере с подключением к Интернету откройте консоль PowerShell в качестве администратора.

2. Войдите в соответствующий клиент каталога Azure Cloud и Azure AD, используя учетную запись Azure, которую вы указали при регистрации Azure Stack Hub. Чтобы добавить учетную запись, выполните в PowerShell команду **Add-AzureRmAccount**. 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   Вам будет предложено ввести учетные данные для входа в Azure и, возможно, выполнить двухфакторную аутентификацию, если это настроено для учетной записи.

   > [!NOTE]
   > Если истек срок сеанса, изменен пароль или вы хотите переключиться на другую учетную запись, перед входом с помощью **Add-AzureRmAccount** выполните следующий командлет: **Процесс Remove-AzureRmAccount-Scope**.

3. Если у вас несколько подписок, выполните следующую команду, чтобы выбрать подписку, которая использовалась для регистрации:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   ```

4. Скачайте последнюю версию средства синдикации Marketplace, если вы не сделали этого при выполнении предварительных требований.

   ```powershell
   Install-Module -Name Azs.Syndication.Admin
   ```

5. Чтобы выбрать элементы Marketplace для скачивания, например образы виртуальных машин, расширения или шаблоны решений, выполните следующую команду. 

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Отобразится таблица со списком всех регистраций Azure Stack, доступных в выбранной подписке. Выберите регистрацию, соответствующую среде Azure Stack, для которой вы скачиваете элементы Marketplace, и щелкните **ОК**.

     ![Выбор регистрации Azure Stack](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Теперь вы увидите вторую таблицу со списком всех элементов Marketplace, доступных для скачивания. Выберите элемент, который вам нужно скачать, и запишите его **версию**. Вы можете выбрать несколько образов, удерживая нажатой клавишу **CTRL**.
     ![Выбор регистрации Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Кроме того, вы можете отфильтровать список образов с помощью параметра **Добавить условия**.
   ![Выбор регистрации Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Выбрав требуемое, щелкните "ОК".

6. Идентификаторы элементов Marketplace, выбранных для скачивания, хранятся в переменной `$products`. Используйте следующую команду, чтобы скачать выбранные элементы. Замените путь к целевой папке путем к папке для хранения файлов, скачанных в Azure Marketplace.

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Время скачивания зависит от размера элемента. После завершения скачивания элемент доступен в папке, указанной в скрипте. Скачанный пакет содержит VHD-файл (для виртуальных машин) или ZIP-файл (для расширений виртуальных машин и поставщиков ресурсов). Он также может содержать пакет коллекции с расширением *.azpkg* (ZIP-файл).

8. Если скачивание завершается сбоем, вы можете повторить попытку, повторно запустив следующий командлет PowerShell:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Кроме того, следует экспортировать модуль **Azs.Syndication.Admin** локально, чтобы его можно было скопировать на компьютер, с которого вы импортируете элементы Marketplace в Azure Stack Hub.

   > [!NOTE]
   > Целевая папка для экспорта этого модуля должна отличаться от папки, в которую экспортированы элементы Marketplace.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Импорт скачанного пакета и его публикация в Azure Stack Hub Marketplace с помощью PowerShell

1. Вам нужно переместить [скачанные ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items) локально файлы на компьютер, подключенный к среде Azure Stack Hub. Средство синдикации Marketplace также должно быть доступно в среде Azure Stack Hub, так как оно используется для операции импорта.

   Пример структуры папки приведен на рисунке ниже. В папке **D:\downloadfolder** содержатся все скачанные элементы marketplace. Каждая вложенная папка представляет собой элемент Marketplace (например, **microsoft.custom-script-linux-arm-2.0.3**) и ее имя соответствует идентификатору продукта. Внутри каждой вложенной папки содержится скачанное содержимое элемента Marketplace.

   ![Структура каталогов для скачивания из Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Инструкции в [этой статье](azure-stack-powershell-configure-admin.md) позволяют настроить сеанс PowerShell для оператора Azure Stack Hub.

3. Войдите в Azure Stack Hub с удостоверением, у которого есть доступ владельца к подписке поставщика по умолчанию.

4. Импортируйте модуль синдикации, а затем запустите средство синдикации marketplace, выполнив следующий скрипт:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. После выполнения скрипта элементы Marketplace станут доступными в Azure Stack Hub Marketplace.
