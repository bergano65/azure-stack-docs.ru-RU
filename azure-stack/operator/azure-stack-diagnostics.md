---
title: Диагностика в Azure Stack
description: Как собирать файлы журналов для диагностики в Azure Stack
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 98ad556bf1b0b5f0297cb7964cd9911a50145496
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691760"
---
# <a name="azure-stack-diagnostics-tools"></a>Средства диагностики Azure Stack

Azure Stack — это большая коллекция компонентов, взаимодействующих между собой. Все эти компоненты создают свои собственные уникальные журналы. Это может усложнить диагностику проблем, особенно в случае с ошибками, поступающими из нескольких взаимодействующих компонентов Azure Stack.

Наши средства диагностики обеспечивают простоту и эффективность механизма сбора журналов. На следующей схеме показана работа средств сбора журналов в Azure Stack.

![Инструменты диагностики Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Сборщик трассировки

Сборщиком трассировки включен по умолчанию и постоянно работает в фоновом режиме для сбора всех журналов трассировки событий Windows (ETW) из служб компонентов Azure Stack. Журналы трассировки событий Windows хранятся в общей локальной папке в течение пяти дней. По достижении этого ограничения самые старые файлы удаляются и создаются новые. Максимальный размер файла по умолчанию составляет 200 МБ. Проверка размера выполняется каждые 2 минуты, и если размер текущего файла превышает 200 МБ, он сохраняется и создается новый. Кроме того, есть ограничение до 8 ГБ для общего размера файлов, создаваемых во время сеанса событий.

## <a name="log-collection-tool"></a>Средство сбора журналов

Командлет PowerShell **Get-AzureStackLog** можно использовать для сбора журналов из всех компонентов в среде Azure Stack. Она сохраняет их в ZIP-файлы в расположении, определяемом пользователем. Если сотрудникам службы технической поддержки Azure Stack понадобятся журналы для устранения проблемы, они могут попросить вас запустить это средство.

> [!CAUTION]
> Эти файлы журналов могут содержать личные сведения. Учитывайте это, прежде чем открыто публиковать какие-либо файлы журналов.

Ниже приведены несколько примеров собираемых типов журналов:

* **журналы развертывания Azure Stack**;
* **журналы событий Windows**;
* **журналы Panther**.
* **журналы кластера**;
* **журналы диагностики хранилища**;
* **журналы трассировки событий Windows**.

Эти файлы собираются и сохраняются в файловом ресурсе сборщиком трассировки. Затем при необходимости их можно будет собрать с помощью командлета PowerShell **Get-AzureStackLog**.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Выполнение командлета Get-AzureStackLog в интегрированных системах Azure Stack

Чтобы запустить средство сбора журналов в интегрированной системе, необходимо иметь доступ к привилегированной конечной точке (PEP). Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов в интегрированной системе:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Выполнение команды Get-AzureStackLog в системе Пакета средств разработки Azure Stack (ASDK)

Выполните следующие действия для запуска командлета `Get-AzureStackLog` на главном компьютере ASDK.

1. Войдите на узел управления с именем **AzureStack\CloudAdmin**.
2. Откройте новое окно PowerShell от имени администратора.
3. Выполните командлет PowerShell **Get-AzureStackLog**.

#### <a name="examples"></a>Примеры

* Соберите все журналы для всех ролей:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Соберите журналы из ролей VirtualMachines и BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за последние 8 часов:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за период времени между последними 8 и 2 часами:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Соберите журналы и сохраните их в указанном контейнере больших двоичных объектов хранилища Azure. Ниже приведен общий синтаксис данной операции.

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Например:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Эта процедура полезна, если служба поддержки Майкрософт просит вас отправить журналы по вашему запросу. Даже если виртуальной машине ERCS недоступен общий ресурс SMB и она не имеет подключения к Интернету, вы можете создать учетную запись для хранения больших двоичных объектов в Azure Stack и перенести журналы, а затем извлечь их с помощью клиента и отправить корпорации Майкрософт.  

  Чтобы создать маркер SAS учетной записи хранения, требуются следующие разрешения:

  * доступ к службе хранилища BLOB-объектов;
  * доступ к типу ресурса контейнера.

  Чтобы создать значение универсального кода ресурса SAS, которое будет использоваться для параметра `-OutputSasUri`, выполните следующие шаги.

  1. Чтобы создать учетную запись хранения, выполните шаги [из этой инструкции](/azure/storage/common/storage-quickstart-create-account).
  2. Откройте экземпляр Обозревателя службы хранилища Azure.
  3. Подключитесь к учетной записи хранения, созданной во время выполнения шага 1.
  4. Перейдите в **Контейнеры больших двоичных объектов** в **службе хранилища**.
  5. Выберите **Создать контейнер**.
  6. Щелкните правой кнопкой мыши новый контейнер, а затем щелкните **Get Shared Access Signature** (Получить подписанный URL-адрес).
  7. Выберите допустимое **Время начала** и **Время окончания**, зависящее от требований.
  8. Чтобы получить необходимые разрешения, выберите **Чтение**, **Запись** и **Список**.
  9. Нажмите кнопку **Создать**.
  10. Будет получен подписанный URL-адрес. Скопируйте часть URL-адреса и укажите ее в параметре `-OutputSasUri`.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Рекомендации по настройке параметров для ASDK и интегрированных систем

* Параметры **OutputSharePath** и **OutputShareCredential** используются для сохранения журналов в указанном пользователем расположении.

* Параметры **FromDate** и **ToDate** можно использовать для сбора журналов за конкретный период времени. Если эти параметры не указаны, по умолчанию журналы собираются за последние четыре часа.

* Чтобы фильтровать журналы по имени компьютера, используйте параметр **FilterByNode**. Например:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Чтобы фильтровать журналы по типу, используйте параметр **FilterByLogType**. Доступна фильтрация по файлу, общему ресурсу или по событию Windows. Например:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Задать время ожидания для сбора журналов можно с помощью параметра **TimeOutInMinutes**. По умолчанию для него задано значение 150 (2,5 часа).
* Сбор журналов файлов дампа отключен по умолчанию. Чтобы включить его, используйте параметр-переключатель **IncludeDumpFile**.
* Сейчас можно использовать параметр **FilterByRole**, чтобы отфильтровать сбор журналов по следующим ролям.

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |Службы вычислений                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Домен                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Хранилище|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |Шлюз                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |CA                    |HRP                            |OboService                     |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>Дополнительные замечания

* Выполнение команды займет некоторое время в зависимости от того, по какой роли (ролям) собираются журналы. К ключевым факторам также относится промежуток времени, указанный для сбора журналов, а также количество узлов в среде Azure Stack.
* После завершения сбора журналов проверьте новую папку, созданную в расположении, которое задано в параметре **OutputSharePath**, указанном в команде.
* Журналы каждой роли хранятся в отдельных ZIP-файлах. В зависимости от размера собранных журналов роли могут быть разделены на несколько ZIP-файлов. Если необходимо распаковать все файлы такой роли в одну папку, используйте средство, которое может распаковать в пакетном режиме (например, 7zip). Выберите все ZIP-файлы для роли и щелкните **extract here** (Извлечь сюда). После этого все файлы журналов для этой роли будут распакованы в одну объединенную папку.
* Файл **Get-AzureStackLog_Output.log** также создается в папке, содержащей ZIP-файлы журналов. Этот файл представляет собой журнал выходных данных команды, который можно использовать для устранения неполадок во время сбора журналов. Иногда в файле журнала содержатся записи `PS>TerminatingError`, которые можно безопасно игнорировать, если ожидаемые файлы журналов не отсутствуют после выполнения сбора журналов.
* Для изучения конкретного сбоя могут понадобиться журналы из нескольких компонентов.

  * Журналы системы и событий для всех виртуальных машин инфраструктуры собираются в роль **VirtualMachines**.
  * Журналы системы и событий для всех узлов собираются в роль **BareMetal**.
  * Журналы событий отказоустойчивого кластера и Hyper-V собираются в роль **Storage**.
  * Журналы ACS собираются в роли **Storage** и **ACS**.

> [!NOTE]
> К собранным журналам применяются ограничения размера и возраста, так как очень важно обеспечить эффективное использование дискового пространства и не переполнять его журналами. Тем не менее при диагностике проблемы иногда могут понадобиться журналы, которые уже удалены из-за этих ограничений. Поэтому **настоятельно рекомендуем** разгружать журналы во внешнее дисковое пространство (учетная запись хранения в Azure, дополнительное локальное устройство хранения данных и т. д.) каждые 8–12 часов и хранить их там 1–3 месяца в зависимости от ваших требований. Кроме того, это хранилище должно быть зашифровано.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Командлет **Invoke-AzureStackOnDemandLog** можно использовать для создания журналов по требованию для определенных ролей (см. список в конце раздела). Журналы, созданные с помощью этого командлета, отсутствуют в комплекте журналов, который вы получаете по умолчанию при выполнении командлета **Get-AzureStackLog**. Кроме этого, данные журналы рекомендуется собирать только в том случае, когда вы получили запрос от службы технической поддержки Майкрософт.

Сейчас можно использовать параметр `-FilterByRole`, чтобы отфильтровать сбор журналов по следующим ролям:

* OEM
* NC
* SLB
* Шлюз

#### <a name="example-of-collecting-on-demand-logs"></a>Пример сбора журналов по требованию

```powershell
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>Дополнительная информация

[Microsoft Azure Stack troubleshooting (Устранение неполадок, связанных с Microsoft Azure Stack)](azure-stack-troubleshooting.md)
