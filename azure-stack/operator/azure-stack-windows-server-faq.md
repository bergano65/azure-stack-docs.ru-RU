---
title: Azure Stack Hub Marketplace — часто задаваемые вопросы
titleSuffix: Azure Stack Hub
description: Список статей в Azure Stack Hub Marketplace с вопросами и ответами по Windows Server.
author: sethmanheim
ms.topic: article
ms.date: 03/19/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 95719c6b0651932ab41cef5321db06b77eb4fc63
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069451"
---
# <a name="azure-stack-hub-marketplace-faq"></a>Azure Stack Hub Marketplace — часто задаваемые вопросы

В этой статье вы найдете ответы на распространенные вопросы об элементах Marketplace в [Azure Stack Hub Marketplace](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Элементы Marketplace

### <a name="who-should-i-contact-for-support-issues-with-azure-stack-hub-marketplace-items"></a>К кому обращаться при возникновении проблем, связанных с элементами Marketplace в Azure Stack Hub?

Рекомендации по поддержке Azure Marketplace также распространяются на элементы Azure Stack Hub Marketplace. Издатели несут ответственность за предоставление технической поддержки для своих продуктов в Azure Stack Hub Marketplace. Дополнительные сведения о рекомендациях по поддержке для элементов Azure Marketplace см. в [этом разделе](/azure/marketplace/marketplace-faq-publisher-guide#customer-support) статьи с вопросами и ответами об Azure Marketplace.

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Как обновить систему до нового образа Windows?

Во-первых, определите, есть ли шаблоны Azure Resource Manager, которые требуют определенной версии. Если такие шаблоны есть, обновите их или сохраните предыдущие версии образов. Лучше всего использовать параметр **version: latest**.

Во-вторых, если любой из масштабируемых наборов виртуальных машин ссылается на конкретную версию, оцените вероятность масштабирования этого набора в будущем и на основе этой оценки решите, не лучше ли сохранить старые версии. Если ни одно из этих условий не применимо, удалите старые образы из Azure Stack Hub Marketplace, прежде чем скачивать новые. Если для их скачивания ранее применялись средства управления Marketplace, используйте их и для удаления. Теперь скачайте более новую версию.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Какие варианты лицензирования существуют для образов Marketplace Windows Server в Azure Stack Hub?

Корпорация Майкрософт предлагает две версии образов Windows Server в Azure Stack Hub Marketplace. В среде Azure Stack Hub можно использовать только одну версию этого образа.  

- **Оплата по мере использования (PAYG)** . В таких образах используются единицы измерения полной цены для Windows.
   Этот вариант предназначен для: клиентов с Соглашением Enterprise (EA), которые используют *модель выставления счетов за потребление*; поставщиков облачных решений, которые не хотят использовать лицензирование SPLA.
- **С использованием собственной лицензии (BYOL)** . Эти образы выполняют базовые единицы измерения.
   Этот вариант предназначен для: клиентов EA с лицензией Windows Server; поставщиков облачных решений с лицензированием SPLA.

Преимущество гибридного использования Azure (AHUB) в Azure Stack Hub не поддерживается. Клиенты, которые приобрели лицензию по модели "Производительность", должны использовать образ BYOL. При тестировании с помощью Пакета средств разработки Azure Stack (ASDK) можно использовать любой из этих вариантов.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Что делать, если я скачаю неправильную версию для своих клиентов или пользователей?

Сначала удалите неправильную версию с помощью колонки управления Marketplace. Дождитесь завершения (то есть уведомлений о завершении, не обращая внимания на состояние в колонке **управления Marketplace**). После этого скачайте правильную версию.

Если вы скачали обе версии образа, клиенты увидят только последнюю версию в Azure Stack Hub Marketplace.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Что делать, если мой пользователь ошибочно установил флажок "У меня есть лицензия" в предыдущих сборках Windows, но у него нет лицензии?

Можно изменить атрибут модели лицензии, чтобы перейти с использования собственной лицензии (BYOL) на оплату по мере использования (PAYG), выполнив следующий сценарий.

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Вы можете проверить тип лицензии, выполнив следующие команды. Если в модели лицензии указано **Windows_Server**, то плата будет взиматься по цене BYOL. В противном случае плата будет взиматься за единицу измерения Windows по модели PAYG.

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Что делать, если я использую старый образ и мой пользователь забыл установить флажок "У меня есть лицензия", либо мы используем собственные образы и имеем право в рамках Соглашения Enterprise?

Можно изменить атрибут модели лицензии, чтобы перейти на модель BYOL, выполнив следующие команды.

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Что будет с другими виртуальными машинами, которые используют Windows Server, например SQL Server или Machine Learning Server?

В этих образах применяется параметр **licenseType**, поэтому они оплачиваются по модели PAYG. Вы можете настраивать этот параметр (см. ответ на предыдущий вопрос). Это относится только к программному обеспечению Windows Server, но не к многоуровневым продуктам. Например, для SQL придется использовать собственную лицензию. Лицензирование с оплатой по мере использования не применяется к многоуровневым программным продуктам.

Обратите внимание на то, что свойство **licenseType** для образов SQL Server из Azure Stack Hub Marketplace можно изменить только в том случае, если используется версия XX.X.20190410 или более поздняя. Если вы используете более раннюю версию образа SQL Server из Azure Stack Hub Marketplace, вы не сможете изменить атрибут **licenseType**, и вам придется повторно развернуть систему с помощью последних образов SQL Server из Azure Stack Hub Marketplace.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>У меня есть Соглашение Enterprise (EA), и я буду использовать лицензию EA для Windows Server. Как можно убедиться, что образы оплачиваются правильно?

Вы можете добавить **licenseType: Windows_Server** в шаблоне Azure Resource Manager. Этот параметр нужно добавлять в каждый блок ресурсов виртуальной машины.

## <a name="activation"></a>Активация

Чтобы активировать виртуальную машину Windows Server в Azure Stack Hub, должны выполняться следующие условия.

- Изготовитель оборудования указал правильное значение маркера BIOS на каждой хост-системе в Azure Stack Hub.
- В Windows Server 2012 R2 и Windows Server 2016 используется [автоматическая активация виртуальной машины](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Служба управления ключами (KMS) и другие службы активации не поддерживаются в Azure Stack Hub.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>Как убедиться, что моя виртуальная машина активирована?

Выполните следующую команду в командной строке с повышенными привилегиями:

```shell
slmgr /dlv
```

Если активация выполнена правильно, вы увидите сообщение об этом и имя узла в выходных данных `slmgr`. Не доверяйте водяным знакам на экране, так как они могут быть устаревшими или относиться к другой виртуальной машине, размещенной за вашей.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>В виртуальной машине не настроено использование AVMA. Как это исправить?

Выполните следующую команду в командной строке с повышенными привилегиями:

```shell
slmgr /ipk <AVMA key>
```

В статье [об автоматической активации виртуальной машины](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) вы найдете сведения о ключах для использования с образом.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Я создаю собственные образы Windows Server. Как мне гарантировать для них использование AVMA?

Рекомендуем выполнить команду `slmgr /ipk` с подходящим ключом перед выполнением команды `sysprep`. Вы также можете поместить ключ AVMA в любой файл установки Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>Я пытаюсь использовать созданный в Azure образ Windows Server 2016, но он не активируется и не использует активацию KMS.

Выполните команду `slmgr /ipk`. В образах Azure не всегда правильно применяется резервный механизм AVMA, но они успешно пройдут активацию, если установят связь с системой Azure KMS. Мы рекомендуем проверить, настроено ли для этих виртуальных машин использование AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>После выполнения всех инструкций виртуальные машины по-прежнему не могут активироваться.

Обратитесь к поставщику оборудования и убедитесь, что установлены правильные маркеры BIOS.

### <a name="what-about-earlier-versions-of-windows-server"></a>Как насчет более ранних версий Windows Server?

[Автоматическая активация виртуальной машины](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) не поддерживается для более ранних версий Windows Server. Такие виртуальные машины следует активировать вручную.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях:

- [Общие сведения об Azure Stack Hub Marketplace](azure-stack-marketplace.md)
- [Скачивание элементов Marketplace из Azure в Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
