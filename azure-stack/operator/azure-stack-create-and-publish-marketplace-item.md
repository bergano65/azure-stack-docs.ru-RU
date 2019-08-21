---
title: Создание и публикация элемента Marketplace в Azure Stack | Документация Майкрософт
description: Создание и публикация элемента Marketplace в Azure Stack.
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 24fc0f7993001ce95a21e175c84f37d755a5ce6c
ms.sourcegitcommit: ec38ec569ad2193369c438f55e5c190aa5f0efd5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68956597"
---
# <a name="create-and-publish-a-marketplace-item"></a>Создание и публикация элемента Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="create-a-marketplace-item"></a>Создание элемента Marketplace

1. Скачайте [средство Azure Gallery Packager](https://www.aka.ms/azurestackmarketplaceitem) и пример элемента Azure Stack Marketplace.
2. Откройте пример элемента Marketplace и переименуйте папку **SimpleVMTemplate**. Новое имя должно совпадать с именем элемента Marketplace, например **Contoso.TodoList**. Эта папка содержит следующее:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Создайте шаблон Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) или выберите шаблон на портале GitHub. Элемент Marketplace использует этот шаблон для создания ресурса.

    > [!NOTE]  
    > В шаблоне Azure Resource Manager нельзя жестко задавать секреты, такие как ключи продуктов, пароли или персональные данные клиентов. После публикации в коллекции JSON-файлы шаблонов доступны без аутентификации. Храните все секреты в хранилище [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) и обращайтесь к ним из шаблона.

4. Проверьте, можно ли с помощью API-интерфейсов Microsoft Azure Stack развернуть ресурс из этого шаблона.
5. Если шаблон использует образ виртуальной машины, [добавьте этот образ виртуальной машины в Azure Stack](azure-stack-add-vm-image.md).
6. Сохраните шаблон Azure Resource Manager в папке **/Contoso.TodoList/DeploymentTemplates/** .
7. Выберите значки и текст для элемента Marketplace. Значки добавляются в папку **Icons**, а текст — в файл **resources**, расположенный в папке **Strings**. Для значков следуйте соглашению об именовании **мелких**, **обычных**, **крупных** и **огромных** значков. Подробное описание этих размеров см. в разделе [Справочные материалы. Пользовательский интерфейс элемента Marketplace](#reference-marketplace-item-ui).

   > [!NOTE]
   > Для правильной сборки элемента Marketplace нужны значки всех четырех размеров (малый, средний, крупный и широкий).
   >
   >

8. В файле **manifest.json** измените значение параметра **name**, указав имя элемента Marketplace. Кроме того, для параметра **publisher** укажите ваше имя или название компании.
9. В разделе **artefacts** укажите в параметрах **name** и **path** правильные данные для включаемого шаблона Azure Resource Manager.

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Замените **My Marketplace Items** списком категорий, в которых должен отображаться этот элемент Marketplace.

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Остальные параметры, которые вы можете изменить в файле manifest.json, описаны в разделе [Справочные материалы. Файл Manifest.json для элемента Marketplace](#reference-marketplace-item-manifestjson).

12. Чтобы упаковать папки в AZPKG-файл, откройте командную строку и выполните следующую команду:

    ```shell
    AzureGalleryPackager.exe package -m <absolute path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > Должен существовать полный путь к JSON-файлу манифеста, а также выходной пакет. Например, если указан выходной путь C:\MarketPlaceItem\yourpackage.azpkg, должна существовать папка **C:\MarketPlaceItem**.
    >
    >

## <a name="publish-a-marketplace-item"></a>Публикация элемента Marketplace

1. Передайте созданный элемент Marketplace (файл AZPKG) в хранилище больших двоичных объектов Azure с помощью PowerShell или обозревателя службы хранилища Azure. Вы можете использовать локальное хранилище Azure Stack или службу хранилища Azure, которая является временным хранилищем для пакета. Убедитесь, что используемый большой двоичный объект является общедоступным.
2. На клиентской виртуальной машине в среде Microsoft Azure Stack проверьте, настроены ли учетные данные администратора службы для сеанса PowerShell. Инструкции по аутентификации PowerShell в Azure Stack вы можете найти в статье [Развертывание шаблонов в Azure Stack с помощью PowerShell](../user/azure-stack-deploy-template-powershell.md).
3. При использовании [PowerShell 1.3.0](azure-stack-powershell-install.md) или более поздней версии вы можете опубликовать элемент Marketplace в Azure Stack с помощью командлета PowerShell **Add-AzsGalleryItem**. В версиях до PowerShell 1.3.0 вместо командлета **Add-AzsGalleryItem** следует использовать **Add-AzureRMGalleryitem**. Например, при использовании PowerShell 1.3.0 или более поздней версии:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | SubscriptionID |Идентификатор подписки администратора. Его можно получить с помощью PowerShell. Если вы предпочитаете получить его на портале, найдите подписку поставщика и скопируйте идентификатор этой подписки. |
   | GalleryItemUri |URI большого двоичного объекта для пакета коллекции, который был ранее отправлен в хранилище. |
   | Apiversion |Установите значение **2015-04-01**. |

4. Вернитесь на портал. Теперь вы увидите этот элемент Marketplace на портале, войдя как оператор или пользователь. Может пройти несколько минут, прежде чем пакет появится в списке.

5. Поздравляем, ваш элемент Marketplace успешно сохранен в Azure Stack Marketplace. Теперь его можно удалить из хранилища больших двоичных объектов.

    > [!CAUTION]  
    > Все артефакты коллекции по умолчанию и пользовательские артефакты коллекции теперь доступны без проверки подлинности по следующим URL-адресам:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Чтобы удалить элемент Marketplace, используйте командлет **Remove-AzureRMGalleryItem**. Например:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
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
| DisplayName |X |Строка, |Рекомендуется использовать 80 символов. |Портал может неправильно отображать имя элемента, если его длина превышает 80 символов. |
| PublisherDisplayName |X |Строка, |Рекомендуется использовать 30 символов. |Портал может неправильно отображать имя издателя, если его длина превышает 30 символов. |
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

Каждый элемент Marketplace должен снабжаться тегами категорий, которые определяют, где отображается этот элемент в пользовательском интерфейсе портала. Вы можете выбрать любую из существующих категорий Azure Stack (**вычисления**, **данные и хранилище** и т. д.) или создать новую.

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

![Колонка "Создание"](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Колонка информации об элементе Marketplace

![Колонка информации об элементе Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Дополнительная информация

* [Общие сведения об Azure Stack Marketplace](azure-stack-marketplace.md)
* [Скачивание элементов marketplace](azure-stack-download-azure-marketplace-item.md)
