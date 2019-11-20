---
title: Устранение неполадок проверки как услуги Azure Stack | Документация Майкрософт
description: Устранение неполадок проверки как услуги для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2dfa55af61627a82f869c7e222dc29997b07a6e3
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955840"
---
# <a name="troubleshoot-validation-as-a-service"></a>Проверка как услуга: устранение неполадок

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ниже приведены распространенные проблемы, не связанные с выпусками программного обеспечения, и способы их решения.

## <a name="local-agent"></a>Локальный агент

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>На портале отображается локальный агент в режиме отладки

Скорее всего это происходит, так как агенту не удается отправить пакеты пульса в службу из-за нестабильного сетевого подключения. Пакет пульса отправляется каждые пять минут. Если служба не получает пакет пульса на протяжении 15 минут, служба считает, что агент неактивный и больше не будет выполнять на нем тестирование. Просмотрите сообщение об ошибке в файле *Agenthost.log*, расположенном в каталоге, где был запущен агент.

> [!Note]
> Все тесты, которые уже выполняются на агенте, продолжат работу, но в случае если пакет пульса не будет восстановлен до окончания этого теста, агент не сможет обновить состояние теста или передать журналы. Тест всегда будет отображаться в состоянии **Выполняется** и его понадобится отменить.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Процесс агента на компьютере был завершен во время выполнения теста. Основные принципы

Если процесс агента завершается некорректно, например при перезапуске компьютера, он завершен (использование клавиш CTRL+C в окне агента считается нормальным завершением работы), то потом тест, запущенный для него, будет и дальше отображаться с состоянием **Выполняется**. Если агент перезапускается, он обновит тест до состояния **Отменен**. Если агент не перезапускается, то тест отображается с состоянием **Выполняется** и его нужно отменить вручную.

> [!Note]
> Тесты в рабочем процессе запланированы последовательно выполняться. Тесты с состоянием **Ожидание** не будут выполняться, пока не выполнятся тесты с состоянием **Выполняется** в рамках того же рабочего процесса.

## <a name="vm-images"></a>Образы виртуальных машин

### <a name="handle-slow-network-connectivity"></a>Обработка медленного сетевого подключения

Вы можете скачать образ PIR в общую папку вашего локального центра обработки данных. Затем можно проверить образ.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Скачивание образа PIR в локальную общую папку при медленной передаче трафика

1. Скачайте AzCopy из [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Извлеките AzCopy.zip и перейдите в каталог с файлом AzCopy.exe.

3. Откройте Windows PowerShell из командной строки с повышенными привилегиями. Выполните следующие команды:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare — локальный путь или путь к общей папке.

#### <a name="verifying-pir-image-file-hash-value"></a>Проверка значения хэша файла образа PIR

Вы можете использовать командлет **Get-HashFile**, чтобы получить значение хэша для скачанных файлов образа общедоступного репозитория образов, чтобы проверить их целостность.

| Имя файла | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105.vhd | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest.vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>При отправке образа виртуальной машины в сценарий `VaaSPreReq` возникает ошибка

Сначала проверьте, что среда находится в работоспособном состоянии:

1. Из динамического административного представления/поля перехода убедитесь, что вы можете успешно войти на портал администрирования, используя учетные данные администратора.
1. Убедитесь в отсутствии оповещений или предупреждений.

Если среда находится в работоспособном состоянии, вручную отправьте 5 образов виртуальных машин, необходимых для тестовых запусков VaaS.

1. Войдите на портал администрирования в качестве администратора служб. URL-адрес портала администрирования можно найти в хранилище ECE или файле сведения о метке. Инструкции см. в разделе [Параметры среды](azure-stack-vaas-parameters.md#environment-parameters).
1. Выберите **Больше служб** > **Поставщики ресурсов** > **Вычисления** > **Образы виртуальных машин**.
1. Нажмите кнопку **+ Добавить** вверху колонки **Образы виртуальных машин**.
1. Измените или проверьте значения следующих полей для первого образа виртуальной машины:
    > [!IMPORTANT]
    > Не все значения по умолчанию являются правильными для существующего элемента Marketplace.

    | Поле  | Значение  |
    |---------|---------|
    | ИЗДАТЕЛЬ | MicrosoftWindowsServer |
    | ПРЕДЛОЖЕНИЕ | WindowsServer |
    | тип ОС; | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version (версия) | 1.0.0 |
    | URI BLOB-объекта для диска ОС | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Нажмите кнопку **Создать**.
1. Повторите процедуру для оставшихся образов виртуальных машин.

Ниже приведены свойства всех 5 образов виртуальных машин:

| ИЗДАТЕЛЬ  | ПРЕДЛОЖЕНИЕ  | тип ОС; | SKU | Version (версия) | URI BLOB-объекта для диска ОС |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/OpenLogic-CentOS-69-20180105.vhd |
| credativ | Debian | Linux | 8 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Debian8_latest.vhd |

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите [заметки о выпуске для проверки как услуги](azure-stack-vaas-release-notes.md), чтобы узнать об изменениях в последних выпусках.