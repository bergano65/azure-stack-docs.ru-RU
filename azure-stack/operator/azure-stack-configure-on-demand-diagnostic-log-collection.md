---
title: Сбор журналов диагностики Azure Stack Hub по запросу
description: Сведения о том, как выполнять сбор журналов диагностики по запросу в Azure Stack Hub с помощью функции справки и поддержки или привилегированной конечной точки (PEP).
author: justinha
ms.topic: article
ms.date: 01/16/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: a0f905a0f6238a0303cacb71e5864ac05b223595
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701553"
---
# <a name="collect-azure-stack-hub-diagnostic-logs-on-demand"></a>Сбор журналов диагностики Azure Stack Hub по запросу

В процессе устранения неполадок службе поддержки пользователей Майкрософт (CSS) может потребоваться проанализировать журналы диагностики. Начиная с выпуска 1907, операторы Azure Stack Hub могут передать журналы диагностики в контейнер больших двоичных объектов в Azure с помощью раздела **Справка и поддержка**. Мы советуем использовать **справку и поддержку** взамен предыдущего метода (использование PowerShell), так как этот подход проще. Если портал недоступен, операторы могут получить журналы с помощью командлета **Get-AzureStackLog** через привилегированную конечную точку (PEP), как в предыдущих выпусках. В этой статье описаны оба способа сбора журналов диагностики по запросу.

>[!Note]
>В качестве альтернативы сбору журналов по запросу можно упростить процесс устранения неполадок, включив [автоматический сбор журналов диагностики](azure-stack-configure-automatic-diagnostic-log-collection.md). Если необходимо исследовать состояние работоспособности системы, журналы могут автоматически передаваться для анализа с помощью CSS. 

## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Использование справки и поддержки для сбора журналов диагностики по запросу

Чтобы устранить проблему, служба поддержки пользователей может попросить оператора Azure Stack Hub собрать журналы диагностики по запросу за определенный период времени на прошедшей неделе. В этом случае служба поддержки пользователей предоставит оператору подписанный URL-адрес для передачи коллекции журналов. Чтобы настроить сбор журналов по запросу с помощью подписанного URL-адреса от службы поддержки пользователей, выполните следующие действия.

1. Откройте раздел **Справка и поддержка** и щелкните **Collect logs now** (Собрать журналы сейчас). 
1. Выберите скользящее окно продолжительностью в 1–4 часа за последние семь дней. 
1. Выберите местный часовой пояс.
1. Введите подписанный URL-адрес, предоставленный службой поддержки пользователей.

   ![Снимок экрана сбора журналов по запросу](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Если включен автоматический сбор журналов диагностики, раздел **Справка и поддержка** показывает, когда идет сбор журналов. Если вы щелкнете **Collect logs now** (Собрать журналы сейчас), чтобы получить журналы за определенный период времени, во время автоматического сбора журналов, то сбор по запросу начнется после завершения автоматического сбора журналов. 

## <a name="use-the-privileged-endpoint-pep-to-collect-diagnostic-logs"></a>Сбор журналов диагностики с помощью привилегированной конечной точки (PEP)

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->



### <a name="run-get-azurestacklog-on-azure-stack-hub-integrated-systems"></a>Выполнение командлета Get-AzureStackLog в интегрированных системах Azure Stack Hub

Чтобы выполнить командлет Get-AzureStackLog в интегрированной системе, необходимо иметь доступ к привилегированной конечной точке (PEP). Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов в интегрированной системе:

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
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

* Соберите журналы из развертываний клиентов, в которых выполняются самостоятельно управляемые кластеры Kubernetes (обработчик AKS) в Azure Stack. Журналы Kubernetes должны храниться в учетной записи хранения клиента в формате, который позволяет применять к ним диапазон времени сбора. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Пример:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
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

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Рекомендации по настройке параметров для ASDK и интегрированных систем

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


