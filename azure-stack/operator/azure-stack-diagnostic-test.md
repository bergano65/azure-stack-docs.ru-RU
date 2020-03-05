---
title: Использование средства проверки Azure Stack Hub для оценки состояния системы
description: Из этой статьи вы узнаете, как использовать средство проверки Azure Stack Hub для оценки состояния системы.
author: justinha
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 1cfae74381121534fea8a49dca4d048e749bc1e6
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700006"
---
# <a name="validate-azure-stack-hub-system-state"></a>Проверка состояния системы Azure Stack Hub

Для оператора Azure Stack Hub очень важна возможность по запросу определять работоспособность и состояние системы. Средство проверки Azure Stack Hub (**Test-AzureStack**) представляет собой командлет PowerShell, который позволяет выполнить в системе ряд тестов для выявления сбоев. Обычно при обращении в службу поддержки клиентов служб Майкрософт (CSS) для устранения проблем вам будут предлагать запустить это средство из [привилегированной конечной точки (PEP)](azure-stack-privileged-endpoint.md). Получив сведения о работоспособности и состоянии всей системы, служба поддержки клиентов переходит к сбору и анализу подробных журналов по тем областям, где произошла ошибка, и при вашем участии устраняет проблему.

## <a name="running-the-validation-tool-and-accessing-results"></a>Запуск средства проверки и доступ к результатам

Как уже упоминалось выше, средство проверки запускается из привилегированной конечной точки. Каждый тест возвращает состояние **успеха или сбоя** в окне PowerShell. Ниже приводится описание полного процесса проверочного тестирования.

1. Установите доверие. В интегрированной системе выполните указанную ниже команду из сеанса Windows PowerShell с повышенными правами, чтобы добавить PEP в качестве доверенного узла на защищенную виртуальную машину, работающую на узле жизненного цикла оборудования, или на рабочую станцию с привилегированным доступом.

   ```powershell
   winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
   ```

   Если вы используете Пакет средств разработки Azure Stack Hub (ASDK), войдите на узел комплекта разработки.

1. Войдите на привилегированную конечную точку. Выполните следующие команды, чтобы создать сеанс подключения к привилегированной конечной точке:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Чтобы обратиться к привилегированной конечной точке на компьютере с Пакетом средств разработки Azure Stack (ASDK), укажите значение AzS-ERCS01 для параметра -ComputerName.

1. После подключения к привилегированной конечной точке выполните следующую команду:

   ```powershell
   Test-AzureStack
   ```

   Подробные сведения см. в разделах [Рекомендации по настройке параметров](azure-stack-diagnostic-test.md#parameter-considerations) и [Примеры использования](azure-stack-diagnostic-test.md#use-case-examples).

1. Если любой из тестов вернет состояние **FAIL** (Сбой), выполните команду `Get-AzureStackLog`. Инструкции для интегрированной системы см. в статье о [запуске Get-AzureStackLog в интегрированных системах Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs), а инструкции для ASDK — в статье о [запуске Get-AzureStackLog в системе с ASDK](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   Этот командлет собирает журналы, созданные командлетом Test-AzureStack. Если тест возвратит результат **WARN** (Предупреждение), мы рекомендуем не собирать журналы, а сразу связаться со службой поддержки.

1. Если вы запускаете средство проверки по просьбе службы поддержки пользователей, нужно будет передать сотруднику службы поддержки собранные журналы для продолжения работы по устранению проблемы.

## <a name="tests-available"></a>Доступные тесты

Средство проверки позволяет выполнить ряд тестов на системном уровне и проверить базовые облачные сценарии, которые дадут вам представление о текущем состоянии системы и позволят устранить возникшие проблемы.

### <a name="cloud-infrastructure-tests"></a>Тесты облачной инфраструктуры

Эти тесты на уровне инфраструктуры выполняются без существенного влияния на работу системы и предоставляют сведения о различных системных компонентах и функциях. Сейчас тесты сгруппированы в следующие категории:

| Категория тестов                                        | Аргумент для параметров -Include (Включить) и -Ignore (Игнорировать) |
| :--------------------------------------------------- | :-------------------------------- |
| Сводка по ACS для Azure Stack Hub                              | AzsAcsSummary                     |
| Сводка по Azure Active Directory в Azure Stack Hub                 | AzsAdSummary                      |
| Сводка по оповещениям Azure Stack Hub                            | AzsAlertSummary                   |
| Сводка о сбое приложения Azure Stack Hub                | AzsApplicationCrashSummary        |
| Сводка по доступу к общему ресурсу резервных копий Azure Stack Hub       | AzsBackupShareAccessibility       |
| Сводка по BMC для Azure Stack Hub                              | AzsStampBMCSummary                |
| Сводка по инфраструктуре, размещающей облако Azure Stack Hub     | AzsHostingInfraSummary            |
| Использование инфраструктуры, размещающей облако Azure Stack Hub | AzsHostingInfraUtilization        |
| Сводка по уровню управления Azure Stack Hub                    | AzsControlPlane                   |
| Сводка по Защитнику Azure Stack Hub                         | AzsDefenderSummary                |
| Сводка по встроенному ПО для инфраструктуры размещения Azure Stack Hub  | AzsHostingInfraFWSummary          |
| Емкость инфраструктуры Azure Stack Hub                  | AzsInfraCapacity                  |
| Производительность инфраструктуры Azure Stack Hub               | AzsInfraPerformance               |
| Сводка по роли инфраструктуры Azure Stack Hub              | AzsInfraRoleSummary               |
| Сетевая инфраструктура Azure Stack Hub                            | AzsNetworkInfra                   |
| Сводка по API и порталу Azure Stack Hub                   | AzsPortalAPISummary               |
| События виртуальной машины для единицы масштабирования Azure Stack Hub                     | AzsScaleUnitEvents                |
| Ресурсы виртуальной машины для единицы масштабирования Azure Stack Hub                  | AzsScaleUnitResources             |
| Сценарии Azure Stack Hub                                | AzsScenarios                      |
| Сводка по проверке SDN для Azure Stack Hub                   | AzsSDNValidation                  |
| Сводка по роли Service Fabric для Azure Stack Hub              | AzsSFRoleSummary                  |
| Плоскость данных хранилища Azure Stack Hub                       | AzsStorageDataPlane               |
| Сводка по службам хранилища Azure Stack Hub                 | AzsStorageSvcsSummary             |
| Сводка по хранилищу SQL для Azure Stack Hub                        | AzsStoreSummary                   |
| Сводка по обновлению Azure Stack Hub                           | AzsInfraUpdateSummary             |
| Сводка по размещению виртуальных машин для Azure Stack Hub                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Тесты облачных сценариев

Помимо вышеперечисленных тестов инфраструктуры, вы можете запускать тесты облачных сценариев для проверки функциональности разных компонентов инфраструктуры. Для выполнения этих тестов требуются учетные данные администратора облака, так как они включают развертывание ресурсов.

> [!NOTE]
> В данный момент вы не можете запускать сценарии в облаке с помощью учетных данных службы федерации Active Directory (AD FS).

Средство проверки умеет проверять следующие облачные сценарии:
- создание группы ресурсов;
- создание плана;
- создание предложения;
- создание учетной записи хранения;
- создание виртуальной машины;
- операции с хранилищем BLOB-объектов;
- операции с хранилищем очередей;
- операции с хранилищем таблиц.

## <a name="parameter-considerations"></a>Рекомендации по настройке параметров

- Параметр **List** (Список) позволяет отобразить все доступные категории тестов.

- Параметры **Include** (Включить) и **Ignore** (Пропустить) можно применять для включения или исключения определенных категорий тестов. Дополнительные сведения об этих аргументах см. в следующем разделе.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- В рамках теста облачных сценариев выполняется развертывание виртуальной машины клиента. Его можно отключить, указав параметр **DoNotDeployTenantVm**.

- Параметр **ServiceAdminCredential** является обязательным для выполнения тестов облачных сценариев, как описано в разделе [Примеры использования](azure-stack-diagnostic-test.md#use-case-examples).

- Параметры **BackupSharePath** и **BackupShareCredential** используются при тестировании параметров резервного копирования инфраструктуры, как показано в разделе [Примеры использования](azure-stack-diagnostic-test.md#use-case-examples).

- **DetailedResults** можно использовать, чтобы для каждого теста получить сведения о предупреждении/сбое/прохождении. Если значение не указано, **Test-AzureStack** возвращает значение **$true**, если не было сбоев, и **$false**, если они были.
- **TimeoutSeconds** может быть использован, чтобы установить время завершения для каждой группы.

- Средство проверки также поддерживает стандартные параметры PowerShell: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable и OutVariable. См. дополнительные сведения об [общих параметрах](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Примеры использования

### <a name="run-validation-without-cloud-scenarios"></a>Запуск проверки без облачных сценариев

Запустите средство проверки без параметра **ServiceAdminCredential**, чтобы пропустить тесты облачных сценариев: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Запуск проверки с облачными сценариями

Если средство проверки запущено с параметром **ServiceAdminCredentials**, по умолчанию оно выполняет тесты облачных сценариев: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Если вы хотите выполнить ТОЛЬКО тесты облачных сценариев без остальных тестов, укажите параметр **Include**: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Имя администратора облака следует указывать в формате имени участника-пользователя: serviceadmin@contoso.onmicrosoft.com (Azure AD). Введите пароль учетной записи администратора облака в ответ на соответствующий запрос.

### <a name="groups"></a>Группы

Чтобы улучшить эффективность операции, был добавлен параметр **Group**, который используется для одновременного выполнения нескольких категорий тестов. На данный момент определены 3 группы: **Default** (По умолчанию), **UpdateReadiness** (Готовность к обновлению) и **SecretRotationReadiness** (Готовность к ротации секретов).

- **По умолчанию**: Это следует рассматривать как стандартный запуск **Test-AzureStack**. Если другие группы не выбраны, эта группа выполняется по умолчанию.
- **UpdateReadiness**. Проверка возможности обновить экземпляр Azure Stack Hub. При выполнении группы **UpdateReadiness** могут отображаться предупреждения в виде ошибок в выходных данных консоли, которые следует рассматривать как условия, препятствующие обновлению. Начиная с версии Azure Stack Hub 1910, следующие категории входят в группу **UpdateReadiness**:

  - **AzsInfraFileValidation**;
  - **AzsActionPlanStatus**;
  - **AzsStampBMCSummary**.

- **SecretRotationReadiness**. Эта проверка помогает узнать, позволяет ли текущее состояние экземпляра Azure Stack Hub выполнять ротацию секретов. При выполнении группы **SecretRotationReadiness** могут отображаться предупреждения в виде ошибок в выходных данных консоли, которые следует рассматривать как условия, препятствующие ротации секретов. К группе SecretRotationReadiness принадлежат следующие категории.

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Пример параметра группы

В приведенном ниже примере мы выполняем **Test-AzureStack**, чтобы проверить готовность системы перед установкой обновления или исправления с помощью **Group**. Прежде чем устанавливать обновление или исправление, запустите **Test-AzureStack** для проверки состояния Azure Stack Hub.

```powershell
Test-AzureStack -Group UpdateReadiness
```

Если используется версия Azure Stack Hub ниже, чем 1811, для выполнения **Test-AzureStack** используйте следующие команды PowerShell:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Запуск средства проверки для тестирования параметров резервного копирования инфраструктуры

*Перед* настройкой резервного копирования инфраструктуры вы можете протестировать путь к общему ресурсу резервных копий и учетные данные с помощью теста **AzsBackupShareAccessibility**:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

*После* настройки резервного копирования вы можете выполнить команду **AzsBackupShareAccessibility** для проверки доступа к общему ресурсу из ERCS.

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Чтобы проверить новые учетные данные для настроенного общего ресурса резервных копий, выполните следующую команду: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Запуск средства проверки для тестирования сетевой инфраструктуры

Этот тест проверяет подключения сетевой инфраструктуры, выполняя обход программно-определяемой сети (SDN) Azure Stack Hub. Он демонстрирует подключение с общедоступного виртуального IP-адреса к настроенным DNS-серверам пересылки, NTP-серверам и конечным точкам аутентификации. Сюда входит подключение к Azure при использовании Azure AD в качестве поставщика удостоверений или к федеративному серверу при использовании AD FS в качестве поставщика удостоверений.

Добавьте параметр debug, чтобы получить подробные выходные данные команды.

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о средствах диагностики Azure Stack Hub и ведении журналов проблем см. в разделе [Сбор журналов диагностики с помощью привилегированной конечной точки (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

Дополнительные сведения об устранении неполадок с Microsoft Azure Stack Hub см. в [этой статье](azure-stack-troubleshooting.md).
