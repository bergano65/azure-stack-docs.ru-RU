---
title: Использование средства проверки Azure Stack | Документация Майкрософт
description: Сведения о том, как собирать файлы журналов для диагностики в Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 04/20/2019
ms.author: mabrigg
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 90a3b35675a8197694d04395f51ef85f7a8fee14
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617967"
---
# <a name="validate-azure-stack-system-state"></a>Проверка состояния системы Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Для оператора Azure Stack очень важна возможность по запросу определять работоспособность и состояние вашей системы. Средство проверки Azure Stack (**Test-AzureStack**) представляет собой командлет PowerShell, который позволяет выполнить в системе ряд тестов для выявления сбоев. Обычно при обращении в службу поддержки клиентов служб Майкрософт (CSS) для устранения проблем вам будут предлагать запустить это средство из [привилегированной конечной точки (PEP)](azure-stack-privileged-endpoint.md). Получив сведения о работоспособности и состоянии всей системы, служба поддержки клиентов переходит к сбору и анализу подробных журналов по тем областям, где произошла ошибка, и в сотрудничестве с вами устраняет проблему.

## <a name="running-the-validation-tool-and-accessing-results"></a>Запуск средства проверки и доступ к результатам

Как уже упоминалось ранее, средство проверки запускается из привилегированной конечной точки. Каждый тест возвращает состояние **успеха или сбоя** в окне PowerShell. Кроме того, создается подробный отчет в формате HTML, который можно получить позднее при [сборе журналов](azure-stack-diagnostics.md). Ниже приводится описание полного процесса проверочного тестирования. 

1. Получите доступ к привилегированной конечной точке. Выполните следующие команды, чтобы создать сеанс подключения к привилегированной конечной точке:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Для доступа к привилегированной конечной точке на главном компьютере ASDK укажите значение AzS-ERCS01 для параметра -ComputerName.

2. Подключившись в привилегированной конечной точке, выполните следующую команду: 

   ```powershell
   Test-AzureStack
   ```

   Воспользуйтесь разделами [Рекомендации по настройке параметров](azure-stack-diagnostic-test.md#parameter-considerations) и [Примеры использования](azure-stack-diagnostic-test.md#use-case-examples), чтобы получить дополнительные сведения.

3. Если любой из тестов вернет состояние **FAIL** (Сбой), выполните такую команду:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Этот командлет собирает журналы, созданные командлетом Test-AzureStack. Дополнительные сведения о журналах диагностики см. в статье [Средства диагностики Azure Stack](azure-stack-diagnostics.md). Нет необходимости собирать журналы или обращаться в службу поддержки пользователей, если тест возвращает состояние **WARN** (ПРЕДУПРЕЖДЕНИЕ).

4. Если вы запускаете средство проверки по просьбе службы поддержки пользователей, нужно будет передать сотруднику службы поддержки собранные журналы для продолжения работы по устранению проблемы.

## <a name="tests-available"></a>Доступные тесты

Средство проверки позволяет выполнить ряд тестов на системном уровне и проверить базовые облачные сценарии, чтобы составить представление о текущем состоянии системы и наличии проблем.

### <a name="cloud-infrastructure-tests"></a>Тесты облачной инфраструктуры

Эти тесты на уровне инфраструктуры выполняются без существенного влияния на работу системы и предоставляют сведения о различных системных компонентах и функциях. Сейчас тесты сгруппированы в следующие категории:

| Категория тестов                                        | Аргумент для параметров -Include (Включить) и -Ignore (Игнорировать) |
| :--------------------------------------------------- | :-------------------------------- |
| Сводка по ACS для Azure Stack                              | AzsAcsSummary                     |
| Сводка по Azure Active Directory в Azure Stack                 | AzsAdSummary                      |
| Сводка по оповещениям Azure Stack                            | AzsAlertSummary                   |
| Сводка о сбое приложения Azure Stack                | AzsApplicationCrashSummary        |
| Сводка по доступу к общему ресурсу резервных копий Azure Stack       | AzsBackupShareAccessibility       |
| Сводка по BMC для Azure Stack                              | AzsStampBMCSummary                |
| Сводка по инфраструктуре, размещающей облако Azure Stack     | AzsHostingInfraSummary            |
| Использование инфраструктуры, размещающей облако Azure Stack | AzsHostingInfraUtilization        |
| Сводка по плоскости управления Azure Stack                    | AzsControlPlane                   |
| Сводка по Защитнику Azure Stack                         | AzsDefenderSummary                |
| Сводка по встроенному ПО для инфраструктуры размещения Azure Stack  | AzsHostingInfraFWSummary          |
| Производительность инфраструктуры Azure Stack                  | AzsInfraCapacity                  |
| Производительность инфраструктуры Azure Stack               | AzsInfraPerformance               |
| Сводка по роли инфраструктуры Azure Stack              | AzsInfraRoleSummary               |
| Сводка по API и порталу Azure Stack                   | AzsPortalAPISummary               |
| События виртуальной машины для единицы масштабирования Azure Stack                     | AzsScaleUnitEvents                |
| Ресурсы виртуальной машины для единицы масштабирования Azure Stack                  | AzsScaleUnitResources             |
| Сценарии Azure Stack                                | AzsScenarios                      |
| Сводка по проверке SDN для Azure Stack                   | AzsSDNValidation                  |
| Сводка по роли Service Fabric для Azure Stack              | AzsSFRoleSummary                  |
| Плоскость данных хранилища Azure Stack                       | AzsStorageDataPlane               |
| Сводка по службам хранилища Azure Stack                 | AzsStorageSvcsSummary             |
| Сводка по хранилищу SQL для Azure Stack                        | AzsStoreSummary                   |
| Сводка по обновлению Azure Stack                           | AzsInfraUpdateSummary             |
| Сводка по размещению виртуальных машин для Azure Stack                     | AzsVmPlacement                    |

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

- Параметры **Include** (Включить) и **Ignore** (Пропустить) можно применять для включения или исключения определенных категорий тестов. Дополнительные сведения, которые можно использовать с этими аргументами, приведены в следующем разделе.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- В одном из тестов облачных сценариев выполняется развертывание виртуальной машины клиента. Чтобы отключить его, укажите параметр **DoNotDeployTenantVm**.

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

Имя администратора облака следует указывать в формате имени участника-пользователя: serviceadmin@contoso.onmicrosoft.com (Azure AD). При появлении запроса введите пароль учетной записи администратора облака.

### <a name="groups"></a>Группы

Чтобы улучшить эффективность операции, был добавлен параметр **Group**, который используется для одновременного выполнения нескольких категорий тестов. На данный момент определены 3 группы: **Default**, **UpdateReadiness** и **SecretRotationReadiness**.

- **По умолчанию**: Это следует рассматривать как стандартный запуск **Test-AzureStack**. Если другие группы не выбраны, эта группа выполняется по умолчанию.
- **UpdateReadiness**. Чтобы увидеть, можно ли обновить метку, выполните проверку. Во время выполнения группы **UpdateReadiness** предупреждения будут отображаться в виде ошибок выходных данных консоли, и их следует рассматривать в качестве препятствий обновления. К группе **UpdateReadiness** принадлежат следующие категории.

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**. Чтобы увидеть, можно ли установить метку, содержащую смены секретов, выполните проверку. Во время выполнения группы **SecretRotationReadiness** предупреждения будут отображаться в виде ошибок выходных данных консоли, и их следует рассматривать в качестве препятствий смены секретов. К группе SecretRotationReadiness принадлежат следующие категории.

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

В приведенном ниже примере мы выполняем **Test-AzureStack**, чтобы проверить готовность системы перед установкой обновления или исправления с помощью **Group**. Прежде чем устанавливать обновление или исправление, следует запустить **Test-AzureStack** для тестирования состояния Azure Stack.

```powershell
Test-AzureStack -Group UpdateReadiness
```

Тем не менее, если выполняемая версия Azure Stack ниже, чем 1811, то для выполнения **Test-AzureStack** следует использовать следующие команды PowerShell.

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Запуск средства проверки для тестирования параметров резервного копирования инфраструктуры

*Перед* настройкой резервного копирования инфраструктуры вы можете протестировать путь к общему ресурсу резервных копий и учетные данные с помощью теста **AzsBackupShareAccessibility**: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
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



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о средствах диагностики Azure Stack и ведении журналов проблем см. в статье [Средства диагностики Azure Stack](azure-stack-diagnostics.md).

Дополнительные сведения об устранении неполадок см. в статье [Устранение неполадок, связанных с Microsoft Azure Stack](azure-stack-troubleshooting.md).
