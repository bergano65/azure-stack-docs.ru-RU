---
title: Устранение неполадок ASDK | Документация Майкрософт
description: Узнайте, как устранять неполадки Пакета средств разработки Azure Stack (ASDK).
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
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: c8db19ff7bf8d7ccdb406617cbcf75dce3770522
ms.sourcegitcommit: c583f19d15d81baa25dd49738d53d8fc01463bef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659220"
---
# <a name="troubleshoot-the-asdk"></a>Устранение неполадок ASDK
В этой статье приведены общие сведения об устранении неполадок Пакета средств разработки Azure Stack (ASDK). Справочную информацию по интегрированным системам Azure Stack см. в статье [Устранение неполадок, связанных с Microsoft Azure Stack](../operator/azure-stack-troubleshooting.md). 

Так как ASDK является средой для оценки, служба поддержки корпорации Майкрософт не предоставляет для нее поддержку. Если возникла проблема, которая не описана в этой статье, посетите [форум MSDN по Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack), чтобы получить помощь от экспертов. 


## <a name="deployment"></a>Развертывание
### <a name="deployment-failure"></a>Сбой развертывания
Если во время установки возникнет сбой, можно использовать параметр -rerun для скрипта развертывания, чтобы перезапустить развертывание с этапа, завершившегося ошибкой. Например:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>В конце развертывания сеанс PowerShell все еще открыт и выходные данные не отображаются
Это поведение, скорее всего, является результатом поведения по умолчанию командного окна PowerShell (если оно выбрано). Развертывание ASDK завершилось успешно, но сценарий был приостановлен при выборе окна. Чтобы узнать, завершилась ли установка, выполните поиск по слову "select" в строке заголовка командного окна. Нажмите клавишу ESC, чтобы отменить выбор, после чего должно отобразиться сообщение о завершении.

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Параметр ошибки osProfile для проверки шаблона не разрешен

Если при проверке шаблона появляется ошибка с сообщением о том, что параметр "osProfile" не разрешен, убедитесь, что вы используете правильные версии API для следующих компонентов:

- [Среда выполнения приложений](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Сеть](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Чтобы скопировать виртуальный жесткий диск из Azure в Azure Stack, используйте [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Обратитесь к поставщику, чтобы устранить проблемы с самим образом. Дополнительные сведения о требованиях WALinuxAgent для Azure Stack см. в статье [об агенте Azure LinuX](../operator/azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Происходит сбой развертывания из-за отсутствия доступа к внешним ресурсам
При сбое развертывания на этапах, где требуется внешний доступ, будет возвращаться исключение, как в следующем примере:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Если эта ошибка возникает, проверьте, выполнены ли все минимальные требования к сети, просмотрев [документацию по сетевому трафику развертывания](../operator/deployment-networking.md). Средство проверки сети также доступно для партнеров в составе набора средств партнеров.

Другие сбои при развертывании обычно возникают из-за проблем с подключением к ресурсам в Интернете.

Чтобы проверить возможность подключения к ресурсам в Интернете, можно выполнить следующие шаги.

1. Откройте PowerShell.
2. Войдите на виртуальную машину WAS01 или любую из виртуальных машин ERCS с помощью командлета Enter-PSSession.
3. Выполните следующий командлет: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Если эта команда не выполняется, убедитесь, что коммутатор TOR и другие сетевые устройства настроены для [разрешения сетевого трафика](../operator/azure-stack-network.md).


## <a name="virtual-machines"></a>Виртуальные машины
### <a name="default-image-and-gallery-item"></a>Элемент коллекции и образ по умолчанию
Перед развертыванием виртуальных машин в Azure Stack необходимо добавить элемент коллекции и образ Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>После перезапуска узла Azure Stack некоторые виртуальные машины не запускаются автоматически
После перезагрузки узла можно заметить, что службы Azure Stack становятся доступны не сразу. Это обусловлено тем, что [виртуальным машинам инфраструктуры](asdk-architecture.md#virtual-machine-roles) и поставщикам ресурсов Azure Stack нужно немного времени для проверки согласованности, но в конечном итоге они будут запущены автоматически.

Вы также можете заметить, что клиентские виртуальные машины не запускаются автоматически после перезагрузки узла ASDK. Чтобы перевести их в рабочее состояние, выполните несколько действий вручную:

1.  На узле ASDK из меню "Пуск" запустите **диспетчер отказоустойчивости кластеров**.
2.  Выберите кластер **S-Cluster.azurestack.local**.
3.  Выберите **Роли**.
4.  Клиентские виртуальные машины отобразятся в состоянии *Сохранено*. После запуска всех виртуальных машин инфраструктуры щелкните правой кнопкой мыши виртуальные машины клиента и выберите **Запустить**, чтобы возобновить работу виртуальной машины.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Некоторые виртуальные машины удалены, но на диске по-прежнему присутствуют VHD-файлы 
Это ожидаемое поведение:

* Если вы удалите виртуальную машину, то ее виртуальные жесткие диски останутся. В группе ресурсов диски являются отдельными ресурсами.
* Как только начнется удаление учетной записи хранения, изменения сразу отобразятся в Azure Resource Manager, но содержащиеся в ней диски все еще будут находиться в хранилище до выполнения сборки мусора.

При появлении "потерянных" виртуальных жестких дисков важно знать, являются ли они частью папки удаленной учетной записи хранения. Если учетная запись хранения не была удалена, эти VHD-файлы и должны оставаться в ее папке.

Вы можете больше узнать о настройке порогового значения периода удержания и освобождении по запросу в статье об [управлении учетными записями хранения](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Хранилище
### <a name="storage-reclamation"></a>Освобождение хранилища
Для отображения освобожденной емкости на портале может понадобиться до 14 часов. Освобождение пространства зависит от различных факторов, включая процент использования файлов внутреннего контейнера в хранилище блочных BLOB-объектов. Поэтому, в зависимости от того, какое количество данных удалено, нет гарантий относительно того, сколько пространства можно освободить, запустив сборщик мусора.

## <a name="next-steps"></a>Дополнительная информация
[Посетите форум технической поддержки Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
