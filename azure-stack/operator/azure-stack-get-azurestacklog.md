---
title: Сбор журналов диагностики с помощью привилегированной конечной точки (PEP)
description: Сведения о том, как выполнять сбор журналов диагностики по запросу в Azure Stack Hub с помощью портала администрирования или скрипта PowerShell.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: df5a98e8526181a84d8b214fbdf82eb1dba00088
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520653"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Отправка журналов диагностики Azure Stack Hub с помощью привилегированной конечной точки

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->


Чтобы выполнить командлет Get-AzureStackLog в интегрированной системе, необходимо иметь доступ к привилегированной конечной точке. Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов. Если вы отменяете выполняемый сбор журналов для запуска нового сбора журналов, подождите 5 минут и введите `Remove-PSSession -Session $session`.


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Примеры

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

* Соберите журналы из развертываний клиентов, в которых выполняются самостоятельно управляемые кластеры Kubernetes (обработчик AKS) в Azure Stack. Журналы Kubernetes должны храниться в учетной записи хранения клиента в формате, который позволяет применять к ним диапазон времени сбора. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Пример:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Соберите журналы для поставщиков дополнительных ресурсов. Ниже приведен общий синтаксис.
 
  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Чтобы начать сбор журналов для Центра Интернета вещей, выполните эту команду: 

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider IotHub
  ```
 
  Чтобы начать сбор журналов для Центров событий, выполните эту команду:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider eventhub
  ```
 
  Чтобы начать сбор журналов для Azure Stack Edge, выполните эту команду:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvide databoxedge
  ```

* Соберите журналы и сохраните их в указанном контейнере больших двоичных объектов хранилища Azure. Ниже приведен общий синтаксис данной операции.

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Пример:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Эта процедура удобна для передачи журналов. Даже если общий ресурс SMB недоступен и нет подключения к Интернету, вы можете создать учетную запись хранения больших двоичных объектов в Azure Stack Hub, чтобы передать в нее журналы, а затем извлечь их с помощью клиента.  

  Чтобы создать маркер SAS учетной записи хранения, требуются следующие разрешения:

  * доступ к службе хранилища BLOB-объектов;
  * доступ к типу ресурса контейнера.

  Чтобы создать значение универсального кода ресурса SAS, которое будет использоваться для параметра `-OutputSasUri`, выполните следующие шаги.

  1. Создайте учетную запись хранения, выполните шаги [из этой статьи](/azure/storage/common/storage-quickstart-create-account).
  2. Откройте экземпляр Обозревателя службы хранилища Azure.
  3. Подключитесь к учетной записи хранения, созданной во время выполнения шага 1.
  4. Перейдите в **Контейнеры больших двоичных объектов** в **службе хранилища**.
  5. Выберите **Создать контейнер**.
  6. Щелкните правой кнопкой мыши новый контейнер, а затем щелкните **Get Shared Access Signature** (Получить подписанный URL-адрес).
  7. Выберите допустимое **Время начала** и **Время окончания**, зависящее от требований.
  8. Чтобы получить необходимые разрешения, выберите **Чтение**, **Запись** и **Список**.
  9. Нажмите кнопку **создания**.
  10. Вы получите подписанный URL-адрес. Скопируйте из него URL-адреса и внесите ее в параметр `-OutputSasUri`.

### <a name="parameter-considerations"></a>Рекомендации по настройке параметров 

* Параметры **OutputSharePath** и **OutputShareCredential** используются для сохранения журналов в указанном пользователем расположении.

* Параметры **FromDate** и **ToDate** можно использовать для сбора журналов за конкретный период времени. Если эти параметры не указаны, по умолчанию журналы собираются за последние четыре часа.

* Чтобы фильтровать журналы по имени компьютера, используйте параметр **FilterByNode**. Пример:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Чтобы фильтровать журналы по типу, используйте параметр **FilterByLogType**. Доступна фильтрация по файлу, общему ресурсу или по событию Windows. Пример:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Задать время ожидания для сбора журналов можно с помощью параметра **TimeOutInMinutes**. По умолчанию для него задано значение 150 (2,5 часа).
* Сбор журналов файлов дампа отключен по умолчанию. Чтобы включить его, используйте параметр-переключатель **IncludeDumpFile**.
* Сейчас можно использовать параметр **FilterByRole**, чтобы отфильтровать сбор журналов по следующим ролям.

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |Службы вычислений                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Домен                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Память                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |Шлюз                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations-on-diagnostic-logs"></a>Дополнительные замечания о журналах диагностики

* Выполнение команды займет некоторое время в зависимости от того, по какой роли (ролям) собираются журналы. К ключевым факторам также относится промежуток времени, указанный для сбора журналов, а также количество узлов в среде Azure Stack Hub.
* После завершения сбора журналов проверьте новую папку, созданную в расположении, которое задано в параметре **OutputSharePath**, указанном в команде.
* Журналы каждой роли хранятся в отдельных ZIP-файлах. В зависимости от размера собранных журналов роли могут быть разделены на несколько ZIP-файлов. Если необходимо распаковать все файлы такой роли в одну папку, используйте инструмент, поддерживающий массовую распаковку. Выберите все ZIP-файлы для роли и щелкните **extract here** (Извлечь сюда). Все файлы журналов для этой роли будут распакованы в одну объединенную папку.
* Файл **Get-AzureStackLog_Output.log** также создается в папке, содержащей ZIP-файлы журналов. Этот файл представляет собой журнал выходных данных команды, который можно использовать для устранения неполадок во время сбора журналов. Иногда в файле журнала содержатся записи `PS>TerminatingError`, которые можно безопасно игнорировать, если ожидаемые файлы журналов не отсутствуют после выполнения сбора журналов.
* Для изучения конкретного сбоя могут понадобиться журналы из нескольких компонентов.

  * Журналы системы и событий для всех виртуальных машин инфраструктуры собираются в роль **VirtualMachines**.
  * Журналы системы и событий для всех узлов собираются в роль **BareMetal**.
  * Журналы событий отказоустойчивого кластера и Hyper-V собираются в роль **Storage**.
  * Журналы ACS собираются в роли **Storage** и **ACS**.

> [!NOTE]
> К собранным журналам применяются ограничения размера и возраста, так как очень важно обеспечить эффективное использование дискового пространства и не переполнять его журналами. Но иногда при диагностике проблемы могут понадобиться журналы, которые уже удалены из-за этих ограничений. Поэтому мы **настоятельно рекомендуем** переносить журналы во внешнее дисковое пространство (учетная запись хранения в Azure, дополнительное локальное устройство хранения данных и т. д.) каждые 8–12 часов и хранить их там 1–3 месяца в зависимости от ваших требований. Кроме того, такое место хранения должно быть зашифровано.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Командлет **Invoke-AzureStackOnDemandLog** можно использовать для создания журналов по требованию для определенных ролей (см. список в конце раздела). Журналы, созданные с помощью этого командлета, отсутствуют в комплекте журналов, который вы получаете по умолчанию при выполнении командлета **Get-AzureStackLog**. Мы рекомендуем собирать эти журналы только по запросу службы технической поддержки Майкрософт.

Сейчас можно использовать параметр `-FilterByRole`, чтобы отфильтровать сбор журналов по следующим ролям:

* OEM
* NC
* SLB
* Шлюз

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Пример журналов диагностики, собираемых по требованию

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>Как работает сбор журналов диагностики с помощью PEP

Средства диагностики Azure Stack Hub помогают сделать сбор журналов простым и эффективным. На следующей схеме показано, как работают средства диагностики.

![Схема рабочего процесса средств диагностики Azure Stack Hub](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Сборщик трассировки

Сборщиком трассировки включен по умолчанию и постоянно работает в фоновом режиме для сбора всех журналов трассировки событий Windows (ETW) из служб компонентов Azure Stack Hub. Журналы трассировки событий Windows хранятся в общей локальной папке в течение пяти дней. По достижении этого ограничения самые старые файлы удаляются и создаются новые. Максимальный размер файла по умолчанию составляет 200 МБ. Проверка размера выполняется каждые 2 минуты, и если размер текущего файла превышает 200 МБ, он сохраняется и создается новый. Кроме того, есть ограничение в 8 ГБ для общего размера файлов, создаваемых во время сеанса событий.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog;

Командлет PowerShell Get-AzureStackLog можно использовать для сбора журналов из всех компонентов в среде Azure Stack Hub. Она сохраняет их в ZIP-файлы в расположении, определяемом пользователем. Если сотрудникам службы технической поддержки Azure Stack Hub понадобятся журналы для устранения проблемы, они могут попросить вас выполнить командлет Get-AzureStackLog.

> [!CAUTION]
> Эти файлы журналов могут содержать личные сведения. Учитывайте это, прежде чем открыто публиковать какие-либо файлы журналов.

Ниже приведены несколько примеров собираемых типов журналов:

* **журналы развертывания Azure Stack Hub**;
* **журналы событий Windows**;
* **журналы Panther**.
* **журналы кластера**;
* **журналы диагностики хранилища**;
* **журналы трассировки событий Windows**.

Эти файлы собираются и сохраняются в файловом ресурсе сборщиком трассировки. Затем при необходимости их можно будет собрать с помощью командлета Get-AzureStackLog.

