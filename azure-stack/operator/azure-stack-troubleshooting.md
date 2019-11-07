---
title: Устранение неполадок, связанных с Microsoft Azure Stack | Документация Майкрософт
description: Устранение неполадок, связанных с Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4c04eafab93da233859b5b67571b70899b081b95
ms.sourcegitcommit: c583f19d15d81baa25dd49738d53d8fc01463bef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659233"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Устранение неполадок, связанных с Microsoft Azure Stack

В этом документе содержатся сведения об устранении неполадок с интегрированными средами Azure Stack. Чтобы получить справку по Пакету средств разработки Azure Stack, откройте раздел по [устранению неполадок с ASDK](../asdk/asdk-troubleshooting.md) или обратитесь к экспертам на [форуме MSDN, посвященному Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

Эти разделы содержат ссылки на документацию, в которой рассматриваются распространенные вопросы, отправленные в службу поддержки пользователей Майкрософт (CSS).

### <a name="purchase-considerations"></a>Вопросы, связанные с приобретением

* [Как приобрести](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Общие сведения о хранилище Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Обновления и диагностика

* [Использование средств диагностики в Azure Stack](azure-stack-diagnostics.md)
* [Проверка состояния системы Azure Stack](azure-stack-diagnostic-test.md)
* [Частота выпуска пакетов обновления](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Поддерживаемые операционные системы и размеры для гостевых виртуальных машин

* [Поддерживаемые операционные системы на виртуальной машине для Azure Stack](azure-stack-supported-os.md)
* [Поддерживаемые размеры виртуальных машин в Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Элементы Azure Marketplace, доступные для Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Управление емкостями

#### <a name="memory"></a>Память

Чтобы увеличить общую емкость доступной памяти для Azure Stack, можно добавить дополнительный объем памяти. Физический сервер в Azure Stack также называется узлом единицы масштабирования. Все узлы единиц масштабирования, входящие в одну единицу масштабирования, должны иметь [одинаковый объем памяти](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Срок хранения

В параметрах срока хранения оператор облачной среди может указать время (0–9999 дней), в течение которого можно восстановить удаленную учетную запись. По умолчанию срок хранения составляет **0** дней. Если этому параметру задано значение **0**, срок хранения всех удаленных учетных записей мгновенно истекает и они отмечаются для периодической сборки мусора.

* [Настройка срока хранения](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Безопасность, соответствие требованиям и идентификация  

#### <a name="manage-rbac"></a>Управление RBAC

Пользователь в Azure Stack может быть читателем, владельцем или участником каждого экземпляра подписки, группы ресурсов или службы.

* [Управление с помощью RBAC в Azure Stack](azure-stack-manage-permissions.md)

Если встроенные роли для ресурсов Azure не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. С помощью этого руководства и Azure PowerShell вы создадите настраиваемую роль с именем "Запросы в службу поддержки от читателя".

* [Руководство. Создание пользовательской роли для ресурсов Azure с помощью Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Управление потреблением и выставлением счетов в качестве поставщика облачных служб

* [Manage usage and billing as a CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription) (Управление потреблением и выставлением счетов в качестве поставщика облачных служб)
* [Создание подписки CSP или APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Выберите тип учетной записи общих служб, который вы будете использовать для Azure Stack. Типы подписок, для которых можно зарегистрировать мультитенантное развертывание Azure Stack:

* Поставщик облачных решений
* Подписка общих служб партнера

### <a name="get-scale-unit-metrics"></a>Получение метрик единиц масштабирования

Вы можете использовать PowerShell для получения сведений об использовании меток без помощи CSS. Получение сведений об использовании меток: 

1. Создайте сеанс PEP
2. Выполните test-azurestack
3. Выйдите из сеанса PEP
4. Выполните azurestacklog -filterbyrole seedring, вызвав invoke-command
5. Извлеките файл seedring. zip и вы сможете получить отчет о проверке из папки ERCS, из которой вы выполнили test-azurestack

См. сведения о [диагностике в Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems).

## <a name="troubleshoot-virtual-machines"></a>Устранение неполадок с виртуальными машинами
### <a name="default-image-and-gallery-item"></a>Элемент коллекции и образ по умолчанию
Перед развертыванием виртуальных машин в Azure Stack необходимо добавить элемент коллекции и образ Windows Server.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>Я удалил несколько виртуальных машин, но на диске по-прежнему отображаются VHD-файлы.
Это ожидаемое поведение:

* Если вы удалите виртуальную машину, виртуальные жесткие диски останутся. В группе ресурсов диски являются отдельными ресурсами.
* Как только начнется удаление учетной записи хранения, изменения сразу отобразятся в Azure Resource Manager, но содержащиеся в ней диски все еще будут находиться в хранилище до выполнения сборки мусора.

При появлении "потерянных" виртуальных жестких дисков важно знать, являются ли они частью папки удаленной учетной записи хранения. Если учетная запись хранения не была удалена, они по-прежнему находятся в папке.

Вы можете больше узнать о настройке порогового значения периода удержания и освобождении по запросу в статье об [управлении учетными записями хранения](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Устранение неполадок с хранилищем
### <a name="storage-reclamation"></a>Освобождение хранилища
Для отображения освобожденной емкости на портале может понадобиться до 14 часов. Освобождение пространства зависит от различных факторов, включая процент использования файлов внутреннего контейнера в хранилище блочных BLOB-объектов. Поэтому, в зависимости от того, какое количество данных удалено, нет гарантии касательно объема пространства, которое можно освободить, запустив сборщик мусора.

### <a name="azure-storage-explorer-not-working-with-azure-stack"></a>Обозреватель службы хранилища Azure не работает с Azure Stack 
 
При работе с интегрированной системой в сценарии без подключения рекомендуется использовать центр сертификации (ЦС) предприятия. Экспортируйте корневой сертификат в формате Base-64, а затем импортируйте его в Обозреватель службы хранилища Azure. Обязательно удалите косую черту ("/") в конце конечной точки ARM. Дополнительные сведения см. в разделе [Подготовка к подключению к Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se#prepare-for-connecting-to-azure-stack).
 

## <a name="troubleshooting-app-service"></a>Устранение неполадок в Службе приложений
### <a name="create-aadidentityappps1-script-fails"></a>Сбой скрипта Create-AADIdentityApp.ps1

Если скрипт Create-AADIdentityApp.ps1, который необходим для работы Службы приложений, завершается сбоем, проверьте наличие обязательного параметра -AzureStackAdminCredential при запуске этого скрипта. Подробнее см. статью [Предварительные условия для развертывания Службы приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

