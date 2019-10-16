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
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: b3540727b1868c700e43e2865848a71635e8003d
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173103"
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

## <a name="get-scale-unit-metrics"></a>Получение метрик единиц масштабирования

Вы можете использовать PowerShell для получения сведений об использовании меток без помощи CSS. Получение сведений об использовании меток: 

1. Создайте сеанс PEP
2. Выполните test-azurestack
3. Выйдите из сеанса PEP
4. Выполните azurestacklog -filterbyrole seedring, вызвав invoke-command
5. Извлеките файл seedring. zip и вы сможете получить отчет о проверке из папки ERCS, из которой вы выполнили test-azurestack

См. сведения о [диагностике в Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems).

## <a name="troubleshoot-deployment"></a>Устранение неполадок с развертыванием 
### <a name="general-deployment-failure"></a>Общий сбой развертывания
Если во время установки возникнет сбой, можно использовать параметр -rerun для скрипта развертывания, чтобы перезапустить развертывание с этапа, завершившегося ошибкой.  

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Параметр ошибки osProfile для проверки шаблона не разрешен

Если при проверке шаблона появляется ошибка с сообщением о том, что параметр "osProfile" не разрешен, убедитесь, что вы используете правильные версии API для следующих компонентов:

- [Среда выполнения приложений](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Сеть](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Чтобы скопировать виртуальный жесткий диск из Azure в Azure Stack, используйте [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Обратитесь к поставщику, чтобы устранить проблемы с самим образом. Дополнительные сведения о требованиях WALinuxAgent для Azure Stack см. в статье [об агенте Azure LinuX](azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Происходит сбой развертывания из-за отсутствия доступа к внешним ресурсам
При сбое развертывания на этапах, где требуется внешний доступ, будет возвращаться исключение, как в следующем примере:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Если эта ошибка возникает, проверьте, выполнены ли все минимальные требования к сети, просмотрев [документацию по сетевому трафику развертывания](deployment-networking.md). Средство проверки сети также доступно для партнеров в составе набора средств партнеров.

Другие сбои при развертывании обычно возникают из-за проблем с подключением к ресурсам в Интернете.

Чтобы проверить возможность подключения к ресурсам в Интернете, можно выполнить следующие шаги.

1. Откройте PowerShell.
2. Войдите на виртуальную машину WAS01 или любую из виртуальных машин ERCS с помощью командлета Enter-PSSession.
3. Выполните следующий командлет: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Если эта команда не выполняется, убедитесь, что коммутатор TOR и другие сетевые устройства настроены для [разрешения сетевого трафика](azure-stack-network.md).

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

## <a name="troubleshooting-app-service"></a>Устранение неполадок в Службе приложений
### <a name="create-aadidentityappps1-script-fails"></a>Сбой скрипта Create-AADIdentityApp.ps1

Если скрипт Create-AADIdentityApp.ps1, который необходим для работы Службы приложений, завершается сбоем, проверьте наличие обязательного параметра -AzureStackAdminCredential при запуске этого скрипта. Подробнее см. статью [Предварительные условия для развертывания Службы приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

