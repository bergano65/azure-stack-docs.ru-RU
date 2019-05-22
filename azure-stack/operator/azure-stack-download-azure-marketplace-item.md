---
title: Скачивание элементов Marketplace из Azure | Документация Майкрософт
description: Оператор облака может скачивать элементы marketplace из Azure в свое развертывание Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 5b35e69a5308589223d9b5987dd3de2e8bb49cc7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985460"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Скачивание элементов Marketplace из Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Как оператор облака вы можете скачивать элементы из Azure Marketplace и делать их доступными в Azure Stack. Элементы, которые вы можете выбрать, находятся в рекомендуемом списке элементов Azure Marketplace, которые предварительно протестированы и поддерживаются в Azure Stack. В этот список часто добавляются дополнительные элементы, поэтому его следует периодически проверять. 

Существует два сценария для подключения к Azure Marketplace. 

- **Сценарий с подключением** — это сценарий, в котором требуется, чтобы ваша среда Azure Stack была подключена к Интернету. Для поиска и скачивания элементов используйте портал Azure Stack. 
- **Сценарий без подключения или с частичным подключением** — в этом сценарии требуется доступ к Интернету с использованием средства синдикации Marketplace для скачивания элементов Marketplace. Затем вы переносите скачанные элементы в свою отключенную установку Azure Stack. В этом сценарии используется PowerShell.

Список элементов marketplace, которые можно скачать, см. в статье [Элементы Azure Marketplace, доступные для Azure Stack](azure-stack-marketplace-azure-items.md).

## <a name="connected-scenario"></a>Сценарий с подключением

Если у Azure Stack есть подключение к Интернету, для скачивания элементов marketplace можно использовать портал администратора.

### <a name="prerequisites"></a>Предварительные требования

Экземпляр развертывания Azure Stack должен быть подключен к Интернету и зарегистрирован [в Azure](azure-stack-registration.md ).

### <a name="use-the-portal-to-download-marketplace-items"></a>Использование портала для загрузки элементов из marketplace
  
1. Войдите на портал администратора Azure Stack.

2.  Проверьте объем свободного места перед скачиванием элементов из marketplace. Позже, когда вы будете выбирать элементы для скачивания, вы можете сравнить размер скачиваемых элементов с доступной емкостью хранилища. Если емкость ограничена, рассмотрите варианты [управления доступным пространством](azure-stack-manage-storage-shares.md#manage-available-space). 

    Чтобы просмотреть доступное пространство, в разделе **Управление регионами** выберите регион, который вы хотите изучить, а затем нажмите **Поставщики ресурсов** > **Хранилище**.

    ![Проверка дискового пространства](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Откройте Azure Stack Marketplace и подключитесь к Azure. Для этого выберите **Элементы Marketplace**, а затем нажмите **Добавить из Azure**.

    ![Добавление из Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    На портале отображается список элементов, доступных для скачивания из Azure Marketplace. Вы можете отфильтровать продукты по имени, издателю и/или типу продукта. Вы также можете щелкнуть каждый элемент, чтобы просмотреть его описание и дополнительную информацию, включая его размер.

    ![Список в Marketplace](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Выберите нужный элемент из списка и щелкните **Скачать**. Время скачивания может различаться.

    ![Сообщение о загрузке](media/azure-stack-download-azure-marketplace-item/image04.png)

    После завершения скачивания можно развернуть новый элемент marketplace от имени оператора или пользователя Azure Stack.

5. Чтобы развернуть скачанный элемент Marketplace, щелкните **+ Создать ресурс**, выберите новый элемент, выполнив поиск в категориях. Выберите элемент, чтобы начать процесс развертывания. Процесс различается для разных элементов marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Сценарий отключенного или частично подключенного режима

При развертывании Azure Stack в отключенном режиме используйте PowerShell и *средство синдикации marketplace*, чтобы скачать элементы marketplace на компьютер с подключением к Интернету. Затем их можно передать в среду Azure Stack. В отключенной среде вы не можете скачивать элементы Marketplace, используя портал Azure Stack. 

Средство синдикации marketplace может также использоваться в подключенном сценарии. 

Этот сценарий состоит из двух частей:
- **Часть 1**. Скачивание из Azure Marketplace. На компьютере с доступом к Интернету настройте PowerShell, скачайте средство синдикации, а затем скачайте элементы из Azure Marketplace.  
- **Часть 2**. Передача и публикация в Azure Stack Marketplace. Переместите файлы, скачанные в среду Azure Stack, импортируйте их в Azure Stack, а затем опубликуйте их в Azure Stack Marketplace.  


### <a name="prerequisites"></a>Предварительные требования
- Развертывание Azure Stack должно быть [зарегистрировано в Azure](azure-stack-registration.md ).  

- На компьютере с подключением к Интернету необходимо установить модуль **PowerShell для Azure Stack версии 1.2.11** или более поздней. При необходимости см. [инструкции по установке модулей PowerShell для Azure Stack](azure-stack-powershell-install.md).  

- Чтобы включить импорт скачанных элементов marketplace, необходимо настроить среду [​​PowerShell для оператора Azure Stack](azure-stack-powershell-configure-admin.md).  

- У вас должна быть [учетная запись хранения](azure-stack-manage-storage-accounts.md) в Azure Stack, которая имеет общедоступный контейнер (в виде BLOB-объекта хранилища). Используйте контейнер в качестве временного хранилища для файлов коллекции элементов marketplace. Если вы не знакомы с учетными записями хранения и контейнерами, см. статью [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) в документации Azure.

- Средство синдикации marketplace скачивается во время первой процедуры. 

- Вы можете установить [AzCopy]((/azure/storage/common/storage-use-azcopy) для оптимизации производительности скачивания, но это не обязательно.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Скачивание элементов marketplace с помощью средства синдикации marketplace

1. На компьютере с подключением к Интернету откройте консоль PowerShell в качестве администратора.

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, в PowerShell запустите `Add-AzureRmAccount` без каких-либо параметров. Вам будет предложено ввести учетные данные для входа в Azure. Также может потребоваться выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

3. Если у вас несколько подписок, выполните следующую команду, чтобы выбрать подписку, которая использовалась для регистрации:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
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
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Импортируйте модуль синдикации и запустите средство, выполнив следующие команды. Замените `Destination folder path` на расположение для хранения файлов, скачанных из Azure Marketplace.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

   Обратите внимание, что `Export-AzSOfflineMarketplaceItem` имеет дополнительный флаг `-cloud`, который определяет облачную среду. По умолчанию это флаг **azurecloud**.

6. После запуска средства появится следующий экран со списком доступных элементов Marketplace:

   [![Всплывающее окно с элементами Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace items")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Выберите элемент, который необходимо скачать, и запишите его *версию*. Вы можете выбрать несколько образов, удерживая нажатой клавишу *CTRL*. Вы создадите ссылку на *версию* при импорте элемента в следующей процедуре. 
   
   Кроме того, вы можете отфильтровать список образов с помощью параметра **Добавить условия**.

8. Нажмите кнопку **ОК**, прочтите и примите юридические условия. 

9. Время скачивания зависит от размера элемента. После завершения скачивания элемент доступен в папке, указанной в скрипте. Скачанный пакет содержит VHD-файл (для виртуальных машин) или ZIP-файл (для расширений виртуальных машин). Она также может содержать пакет коллекции в формате *AZPKG* (это просто ZIP-файл).

10. Если скачивание завершается сбоем, вы можете повторить попытку, повторно запустив следующий командлет PowerShell:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Перед повторным выполнением удалите папку продукта, в которую не удалось выполнить скачивание. Например, если скачивание скрипта в `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` закончилось сбоем, удалите папку `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, а затем повторно запустите командлет.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Импорт скачанного пакета и его публикация в Azure Stack Marketplace (версия 1811 и выше)

1. Необходимо локально переместить файлы, [загруженные ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items), чтобы они были доступны в среде Azure Stack. Средство синдикации marketplace также должно быть доступно в среде Azure Stack, так как оно используется для выполнения операции импорта.

   Пример структуры папки приведен на рисунке ниже. В папке `D:\downloadfolder` содержатся все скачанные элементы marketplace. Каждая подпапка — это элемент marketplace (например, `microsoft.custom-script-linux-arm-2.0.3`), который назван по идентификатору продукта. Внутри каждой подпапки содержится загруженное содержимое элемента marketplace.

   [![Структура скачанных каталогов Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace download directory structure")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Инструкции в [этой статье](azure-stack-powershell-configure-admin.md) позволяют настроить сеанс PowerShell для оператора Azure Stack. 

3. Импортируйте модуль синдикации, а затем запустите средство синдикации marketplace, выполнив следующий скрипт:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Параметр `-origin` позволяет указать папку верхнего уровня, содержащую все скачанные файлы продуктов (например, `"D:\downloadfolder"`).

   Параметр `-AzsCredential` не обязателен. Он используется для обновления маркера доступа, если его срок действия истек. Если параметр `-AzsCredential` не указан и срок действия маркера истекает, появится запрос на ввод учетных данных оператора.

    > [!Note]  
    > AD FS поддерживает только интерактивную проверку подлинности с удостоверениями пользователей. Если требуется объект учетных данных, вам необходимо использовать субъект-службу (SPN). Дополнительные сведения о том, как с помощью Azure Stack и AD FS настроить субъект-службу в качестве службы управления удостоверениями, см. в разделе [Управление субъектом-службой для AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Элемент должен быть доступен в Azure Stack Marketplace после успешного выполнения скрипта.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Импорт скачанного пакета и его публикация в Azure Stack Marketplace (версия 1809 и выше)

1. Файлы образов виртуальных машин или шаблонов решений, которые вы [скачали ранее](#use-the-marketplace-syndication-tool-to-download-marketplace-items), должны быть локально доступны для вашей среды Azure Stack.  

2. Используя портал администратора, отправьте пакет элементов Marketplace (файл AZPKG) и образ виртуального жесткого диска в хранилище BLOB-объектов Azure Stack. Загрузка пакета и файлов диска делают их доступными в Azure Stack, поэтому вы сможете позже опубликовать элемент в Azure Stack Marketplace.

   Для загрузки требуется наличие учетной записи хранения с общедоступным контейнером (см. предварительные условия для этого сценария).  
   1. На портале администрирования Azure Stack перейдите в раздел **Все службы** и затем в категории **Данные+хранилище** выберите **Учетные записи хранения**.  
   
   2. Выберите учетную запись хранения из своей подписки, а затем в колонке **службы BLOB-объектов** выберите **Контейнеры**.  
      [![Служба BLOB-объектов](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Выберите контейнер, который вы хотите использовать, а затем нажмите кнопку **Отправить**, чтобы открыть панель **Отправить BLOB-объект**.  
      [![Контейнер](media/azure-stack-download-azure-marketplace-item/container.png "Container")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. На панели "Отправить BLOB-объект" перейдите к пакету и файлам диска, которые вы хотите загрузить в хранилище, а затем нажмите кнопку **Передать**. [![Передача](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Upload")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Переданные файлы отображаются в области контейнера. Выберите файл, а затем скопируйте URL-адрес из панели **Свойства BLOB-объекта**. Вы будете использовать этот URL-адрес на следующем шаге при импорте элементов marketplace в Azure Stack.  На следующем изображении контейнером является *blob-test-storage*, а файлом — *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  URL-адрес файла — *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      [![Свойства больших двоичных объектов](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob properties")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Импортируйте образ VHD в Azure Stack с помощью командлета **Add-AzsPlatformimage**. При использовании этого командлета замените значения *publisher*, *offer* и других параметров значениями для импортируемого образа. 

   Вы можете получить значения *publisher*, *offer* и *sku* образа из текстового файла, который скачивается вместе с файлом AZPKG. Текстовый файл хранится в целевом расположении. Значение *версии* — это версия, указанная при скачивании элемента из Azure в предыдущей процедуре. 
 
   В следующем примере скрипта используются значения для виртуальной машины Windows Server 2016 Datacenter Server Core. Значение *-Osuri* — это пример пути к хранилищу BLOB-объектов элемента.

   Вместо этого скрипта можно использовать [процедуру, описанную в этой статье](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal), для импорта образа VHD с помощью портала Azure.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **О шаблонах решений**. Некоторые шаблоны могут содержать небольшой VHD-файл размером 3 МБ с именем **fixed3.vhd**. Вам не нужно импортировать этот файл в Azure Stack. Fixed3.vhd.  Этот файл входит в состав некоторых шаблонов решений, чтобы были выполнены требования к публикации в Azure Marketplace.

   Просмотрите описание шаблонов, скачайте, а затем импортируйте дополнительные компоненты, например виртуальные жесткие диски, которые требуются для работы с шаблоном решения.  
   
   **О расширениях**. При работе с расширениями образов виртуальных машин используйте следующие параметры:
   - *Издатель*
   - *Тип*
   - *Версия*  

   Не используйте для расширений параметр *Offer*.   


4.  Используя PowerShell, опубликуйте элемент marketplace в Azure Stack с помощью командлета **Add-AzsGalleryItem**. Например:   
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```
5. После публикации элемента коллекции его можно использовать. Чтобы убедиться, что элемент коллекции опубликован, перейдите в раздел **Все службы**, а затем в категории **Общее** выберите **Marketplace**.  Если вы загружаете шаблон решения, обязательно добавьте любой зависимый образ VHD для этого шаблона решения.  
  [![Просмотр Marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "View Marketplace")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

В выпуске Azure Stack PowerShell 1.3.0 вы можете добавлять расширения виртуальной машины. Например: 

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Дополнительная информация

[Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)