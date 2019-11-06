---
title: Создание и публикация элемента Marketplace в Azure Stack | Документация Майкрософт
description: Узнайте, как создать и опубликовать элемент Marketplace в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: d03049fd6dea5f5d7a10a61a25639cb1de3d67ad
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73057781"
---
# <a name="create-and-publish-a-custom-azure-stack-marketplace-item"></a>Создание и публикация пользовательского элемента Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Каждый элемент, опубликованный в Azure Stack Marketplace, доступен в формате пакета коллекции Azure (AZPKG-файл). Средство *Azure Gallery Packager* позволяет создать пользовательский пакет коллекции Azure, который можно отправить в Azure Stack Marketplace для последующего скачивания пользователями. В процессе развертывания используется шаблон Azure Resource Manager.

## <a name="marketplace-items"></a>Элементы Marketplace

Примеры в этой статье показывают, как создать в Marketplace одно предложение виртуальной машины типа Windows или Linux.

## <a name="create-a-marketplace-item"></a>Создание элемента Marketplace

> [!IMPORTANT]
> Прежде чем создавать элемент Marketplace для виртуальной машины, отправьте пользовательский образ виртуальной машины на портал Azure Stack по инструкциям из статьи в разделе [Добавление образа виртуальной машины в Azure Stack](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal). Затем по инструкциям в этой статье упакуйте образ и передайте созданный AZPKG-файл в Azure Stack Marketplace.

Чтобы создать пользовательский элемент Marketplace, выполните следующие действия.

1. Скачайте средство [Azure Gallery Packager](https://aka.ms/azsmarketplaceitem) и пример пакета коллекции Azure Stack. В комплект для скачивания входят пользовательские шаблоны виртуальных машин. Извлеките ZIP-файл и переименуйте папку **SimpleVMTemplate** , чтобы ее имя совпадало с именем элемента, который будет отображаться на портале Azure Stack.

2. Создайте шаблон Azure Resource Manager или выберите готовый пример шаблона для Windows или Linux. Эти примеры шаблонов размещены в ZIP-файле средства, который вы скачали на шаге 1. Вы можете изменить текстовые значения в полях шаблона либо скачать уже настроенный шаблон с сайта GitHub. Дополнительную информацию о шаблонах Resource Manager см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates).

3. Пакет коллекции должен иметь такую структуру:

   ![Снимок экрана: структура пакета коллекции](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   Шаблоны развертывания имеют следующую структуру файлов:

   ![Снимок экрана: структура шаблонов развертывания](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. Замените следующие выделенные значения (обозначенные числами) в шаблоне Manifest.json значением, которое вы указали при [отправке пользовательского образа](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal).

   > [!NOTE]  
   > В шаблоне Azure Resource Manager нельзя жестко задавать секреты, такие как ключи продуктов, пароли или персональные данные клиентов. После публикации в коллекции JSON-файлы шаблонов доступны без аутентификации. Храните все секреты в хранилище [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) и обращайтесь к ним из шаблона.

   Ниже показан пример файла Manifest.json.

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "uiDefinition": {
          "path": "UIDefinition.json" (7)
          },
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "name": "<Template name>",
             "type": "Template",
             "path": "DeploymentTemplates\\<Template name>.json", (8)
             "isDefault": true
          }
       ],
       "categories":[ (9)
          "Custom",
          "<Template name>"
          ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (10)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    В следующем списке описаны пронумерованные значения в примере шаблона выше.

    - (1) — имя предложения.
    - (2) — имя издателя, без пробелов.
    - (3) — версия этого шаблона, без пробелов.
    - (4) — имя, которое увидят клиенты.
    - (5) — имя издателя, которое увидят клиенты.
    - (6) — официальное наименование издателя.
    - (7) — путь, по которому хранится файл **UIDefinition.json**.  
    - (8) — путь и имя основного JSON-файла шаблона.
    - (9) — имена категорий, в которых отображается этот шаблон.
    - (10) — путь и имя для каждого значка.

5. Для всех полей, в которых упоминается **ms-resource**, необходимо изменить соответствующие значения в файле **strings/resources.json**:

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

    ![Отображение пакета](media/azure-stack-create-and-publish-marketplace-item/pkg1.png) ![Отображение пакета](media/azure-stack-create-and-publish-marketplace-item/pkg2.png)

6. Проверьте, можно ли с помощью [API-интерфейсов Azure Stack](../user/azure-stack-profiles-azure-resource-manager-versions.md) развернуть ресурс из этого шаблона.

7. Если шаблон использует образ виртуальной машины, [добавьте этот образ виртуальной машины в Azure Stack](azure-stack-add-vm-image.md).

8. Сохраните шаблон Azure Resource Manager в папке **/Contoso.TodoList/DeploymentTemplates/** .

9. Выберите значки и текст для элемента Marketplace. Значки добавляются в папку **Icons**, а текст — в файл **resources**, расположенный в папке **Strings**. Для значков следуйте соглашению об именовании **мелких**, **обычных**, **крупных** и **огромных** значков. Подробное описание этих размеров см. в разделе [Справочные материалы. Пользовательский интерфейс элемента Marketplace](#reference-marketplace-item-ui).

    > [!NOTE]
    > Для правильной сборки элемента Marketplace нужны значки всех четырех размеров (малый, средний, крупный и широкий).

10. Остальные параметры, которые вы можете изменить в файле manifest.json, описаны в разделе [Справочные материалы. Файл Manifest.json для элемента Marketplace](#reference-marketplace-item-manifestjson).

11. Завершив изменение файлов, преобразуйте их в AZPKG-файл. Преобразование выполняется с помощью средства **AzureGallery.exe** и скачанного ранее примера пакета коллекции. Выполните следующую команду:

    ```shell
    .\AzureGallery.exe package –m c:\<path>\<gallery package name>\manifest.json –o c:\Temp
    ```

    > [!NOTE]
    > Для выходных данных можно выбрать любой путь, даже не обязательно на диске C:. Но пути к JSON-файлу манифеста и выходному пакету должны существовать в системе. Например, если путь к выходному файлу имеет значение `C:\<path>\galleryPackageName.azpkg`, то должна существовать папка `C:\<path>`.
    >
    >

## <a name="publish-a-marketplace-item"></a>Публикация элемента Marketplace

1. Передайте созданный элемент Marketplace (файл AZPKG) в хранилище больших двоичных объектов Azure с помощью PowerShell или обозревателя службы хранилища Azure. Вы можете использовать локальное хранилище Azure Stack или службу хранилища Azure, которая является временным хранилищем для пакета. Убедитесь, что используемый большой двоичный объект является общедоступным.

2. Чтобы импортировать пакет коллекции в Azure Stack, сначала выполните удаленное подключение (RDP) к клиентской виртуальной машине, чтобы скопировать файл, который вы только что создали в Azure Stack.

3. Добавьте контекст.

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Выполните следующий скрипт, чтобы импортировать ресурс в коллекцию:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg –Verbose
    ```

5. Убедитесь, что у вас есть допустимая учетная запись хранения, в которой можно сохранить этот элемент. Значение `GalleryItemURI` можно получить на портале администрирования Azure Stack. Выберите **Учетная запись хранения —> Свойства большого двоичного объекта > URL** с расширением .azpkg. Учетная запись хранения используется непродложительно, только для публикации в Marketplace.

   После того, как вы подготовите пакет коллекции и отправите его командой **Add-AzsGalleryItem**, пользовательская виртуальная машина появится в Marketplace и в представлении **Создание ресурсов**. Обратите внимание, что пользовательский пакет коллекции не отображается в разделе **управления Marketplace**.

   [![Отправленный пользовательский элемент Marketplace](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Отправленный пользовательский элемент Marketplace")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. После успешной публикации элемента в Marketplace можно удалить содержимое из учетной записи хранения.

   > [!CAUTION]  
   > Все артефакты коллекции по умолчанию и пользовательские артефакты коллекции теперь доступны без проверки подлинности по следующим URL-адресам:  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. Чтобы удалить элемент Marketplace, используйте командлет **Remove-AzureRMGalleryItem**. Например:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > После удаления элемента пользовательский интерфейс Marketplace может отобразить ошибку. Чтобы устранить эту ошибку, щелкните **Параметры** на портале. Затем щелкните **Отменить изменения** в разделе **Настройка портала**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Справочные материалы. Файл manifest.json для элемента Marketplace

### <a name="identity-information"></a>Сведения об удостоверении

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| ИМЯ |X |Строка, |[A–Z, a–z, 0–9] + | |
| ИЗДАТЕЛЬ |X |Строка, |[A–Z, a–z, 0–9] + | |
| Version (версия) |X |Строка, |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Метаданные

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка, |Рекомендуется использовать 80 символов. |Портал может неправильно отображать имя элемента, если его длина превышает 80 символов. |
| PublisherDisplayName |X |Строка, |Рекомендуется использовать 30 символов. |Портал может неправильно отображать имя издателя, если его длина превышает 30 символов. |
| PublisherLegalName |X |Строка, |Не более 256 символов | |
| Сводка |X |Строка, |60–100 знаков. | |
| LongSummary |X |Строка, |140–256 знаков. |Пока не применяется в Azure Stack. |
| ОПИСАНИЕ |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |От 500 до 5000 символов. | |

### <a name="images"></a>Образы

В Marketplace используются следующие значки:

| ИМЯ | Ширина | Высота: | Примечания |
| --- | --- | --- | --- |
| Широкий |255 пикселей |115 пикселей |Обязательный |
| большой |115 пикселей |115 пикселей |Обязательный |
| Средний |90 пикселей |90 пикселей |Обязательный |
| Малый |40 пикселей |40 пикселей |Обязательный |
| Снимок экрана |533 пикселя |324 пикселя |Обязательный |

### <a name="categories"></a>Категории

Каждый элемент Marketplace должен снабжаться тегами категорий, которые определяют, где отображается этот элемент в пользовательском интерфейсе портала. Вы можете выбрать любую из имеющихся категорий Azure Stack (**Вычисления**, **Данные + хранилище** и т. д.) или создать новую.

### <a name="links"></a>Ссылки

Каждый элемент Marketplace может содержать разные ссылки на дополнительное содержимое. Ссылки указываются в виде списка имен и универсальных кодов ресурса (URI).

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка, |Длина не должна превышать 64 символов. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Дополнительные свойства

Помимо приведенных выше метаданных авторы Marketplace могут предоставить пользовательские данные в формате "ключ-значение", как показано далее.

| ИМЯ | Обязательно | type | Ограничения | ОПИСАНИЕ |
| --- | --- | --- | --- | --- |
| DisplayName |X |Строка, |Длина не должна превышать 25 символов. | |
| Значение |X |Строка, |Длина не должна превышать 30 символов. | |

### <a name="html-sanitization"></a>Очистка HTML

Для всех полей, поддерживающих HTML, [допускаются следующие элементы и атрибуты](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Справочные материалы. Пользовательский интерфейс элемента Marketplace

Для элемента Marketplace на портале Azure Stack используются следующие значки и текстовые данные.

### <a name="create-blade"></a>Колонка "Создание"

![Колонка "Создание" — элементы Marketplace для Azure Stack](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Колонка информации об элементе Marketplace

![Колонка информации об элементе Marketplace для Azure Stack](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения об Azure Stack Marketplace](azure-stack-marketplace.md)
- [Скачивание элементов marketplace](azure-stack-download-azure-marketplace-item.md)
- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates)
