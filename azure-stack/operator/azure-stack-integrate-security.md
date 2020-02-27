---
title: Интеграция Azure Stack Hub с решениями для мониторинга с помощью перенаправления системного журнала
description: Узнайте, как интегрировать Azure Stack Hub с решениями для мониторинга с помощью перенаправления системного журнала.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 01/10/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 21a4d50c4ebb191e32c8c5a853359880bcdb1392
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509795"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>Интеграция Azure Stack Hub с решениями для мониторинга с помощью перенаправления системного журнала

В этой статье показано, как с помощью системного журнала настроить интеграцию инфраструктуры Azure Stack Hub с внешними решениями безопасности, развернутыми в вашем центре обработки данных. Например, с системой управления информационной безопасностью и событиями безопасности (SIEM). Канал системного журнала передает журналы аудита, оповещений и безопасности от всех компонентов инфраструктуры Azure Stack Hub. Перенаправление системного журнала позволяет наладить интеграцию с решениями для мониторинга безопасности и (или) извлечь все журналы аудита, оповещений и безопасности для длительного хранения.

Начиная с обновления 1809 Azure Stack Hub использует встроенный клиент системного журнала, который можно настроить на выдачу сообщений системного журнала с полезными данными в формате CEF (общий формат событий).

На приведенной ниже схеме показана интеграция Azure Stack Hub с внешней системой SIEM. Есть два шаблона интеграции, которые следует учитывать. Синим цветом выделена инфраструктура Azure Stack Hub, которая содержит виртуальные машины инфраструктуры и узлы Hyper-V. Все записи об аудите, журналы безопасности и оповещения от этих компонентов централизованно собираются и отображаются в системном журнале с полезными данными в формате CEF. Такой шаблон интеграции описан на этой странице документации.
Второй шаблон интеграции, который выделен оранжевым цветом, охватывает контроллеры управления основной платой (BMC), узел жизненного цикла оборудования (HLH), виртуальные машины и (или) виртуальные устройства, на которых выполняется программное обеспечение поставщика оборудования для мониторинга и управления, а также стоечные коммутаторы (TOR). Эти компоненты будут разными у разных поставщиков оборудования. Поэтому документацию по их интеграции с внешней системой SIEM следует получить у партнера, который предоставляет вам это оборудование.

![Схема перенаправления системного журнала](media/azure-stack-integrate-security/azure-stack-hub-syslog-forwarding-diagram_bg.svg)

## <a name="configuring-syslog-forwarding"></a>Настройка перенаправления системного журнала

Клиент системного журнала в Azure Stack Hub поддерживает следующие конфигурации:

1. **Передача системного журнала по протоколу TCP со взаимной аутентификацией между клиентом и сервером и шифрованием TLS 1.2** В этой конфигурации сервер и клиент системного журнала идентифицируют друг друга с помощью сертификатов. Сообщения передаются по зашифрованному каналу TLS 1.2.

2. **Передача системного журнала по протоколу TCP с аутентификацией сервера и шифрованием TLS 1.2**. В этой конфигурации клиент системного журнала идентифицирует сервер с помощью сертификата. Сообщения передаются по зашифрованному каналу TLS 1.2.

3. **Передача системного журнала по протоколу TCP без шифрования**. В этой конфигурации удостоверения клиента и сервера системного журнала не проверяются. Сообщения отправляются в формате открытого текста по протоколу TCP.

4. **Передача системного журнала по протоколу UDP без шифрования**. В этой конфигурации удостоверения клиента и сервера системного журнала не проверяются. Сообщения отправляются в формате открытого текста по протоколу UDP.

> [!IMPORTANT]
> Корпорация Майкрософт настоятельно рекомендует использовать в рабочей среде только протокол TCP с проверкой подлинности и шифрованием (конфигурация 1 или в крайнем случае 2), чтобы предотвратить атаки "злоумышленник в середине" и неавторизованное прослушивание сообщений.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Командлеты для настройки перенаправления системного журнала
Чтобы настроить перенаправление системного журнала, требуется доступ к привилегированной конечной точке. Привилегированная конечная точка теперь включает два командлета PowerShell для настройки перенаправления системного журнала:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <UInt16>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Параметры командлетов

Параметры для командлета *Set-SyslogServer*.

| Параметр | Описание | Тип | Обязательно |
|---------|---------|---------|---------|
|*ServerName* | Полное доменное имя или IP-адрес сервера системного журнала. | Строка | да|
|*ServerPort* | Номер порта, через который сервер системного журнала ожидает передачи данных. | UInt16 | да|
|*NoEncryption*| Принудительная отправка сообщений из клиента системного журнала в формате открытого текста. | Флаг | нет|
|*SkipCertificateCheck*| Отмена проверки сертификата, который предоставляется сервером системного журнала во время первоначального подтверждения TLS. | Флаг | нет|
|*SkipCNCheck*| Отмена проверки общего имени сертификата, который предоставляется сервером системного журнала во время первоначального подтверждения TLS. | Флаг | нет|
|*UseUDP*| Использование UDP в качестве транспортного протокола для системного журнала. |Флаг | нет|
|*Удалить*| Удаление конфигурации сервера из клиента и прекращение перенаправления системного журнала.| Флаг | нет|

Параметры для командлета *Set-SyslogClient*.

| Параметр | Описание | Тип |
|---------|---------| ---------|
| *pfxBinary* | Содержимое переданного в Byte[] PFX-файла с сертификатом, используемым клиентом в качестве удостоверения для проверки подлинности на сервере системного журнала.  | Byte[] |
| *CertPassword* |  Пароль для импорта закрытого ключа, связанного с PFX-файлом. | SecureString |
|*RemoveCertificate* | Удаление сертификата из клиента. | Флаг|
| *OutputSeverity* | Уровень ведения журнала выходных данных. Возможные значения **Default** (По умолчанию) и **Verbose** (Подробно). Значение "По умолчанию" включает следующие уровни серьезности: "предупреждение", "критические ошибки"и "ошибка". Значение Verbose включает в себя все уровни серьезности — "подробная информация", "информация", "предупреждение", "критические ошибки" и "ошибка".  | Строка |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Настройка перенаправления системного журнала с протоколом TCP, взаимной проверкой подлинности и шифрованием TLS 1.2

В этой конфигурации клиент системного журнала в Azure Stack Hub передает сообщения на сервер системного журнала по протоколу TCP с шифрованием TLS 1.2. При первоначальном подтверждении клиент проверяет, что сервер предоставил действительный доверенный сертификат. Клиент также предоставляет серверу сертификат для подтверждения своей идентификации. Такая конфигурация является наиболее безопасной, так как обеспечивает идентификацию как клиента, так и сервера, а все сообщения передаются по зашифрованному каналу.

> [!IMPORTANT]
> Корпорация Майкрософт настоятельно рекомендует использовать для рабочей среды только эту конфигурацию. 

Чтобы настроить перенаправление системного журнала по протоколу TCP со взаимной проверкой подлинности и шифрованием TLS 1.2, выполните эти два командлета в сеансе связи с привилегированной конечной точкой:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Для сертификата клиента должен быть указан тот же корневой сертификат, который был указан во время развертывания Azure Stack Hub. Также он должен содержать закрытый ключ.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Настройка перенаправления системного журнала по протоколу TCP с проверкой подлинности сервера и шифрованием TLS 1.2

В этой конфигурации клиент системного журнала в Azure Stack Hub передает сообщения на сервер системного журнала по протоколу TCP с шифрованием TLS 1.2. При первоначальном подтверждении клиент проверяет подлинность и доверие предоставленного сервером сертификата. Такая конфигурация не позволяет клиенту отправить сообщения ненадежным получателям.
Протокол TCP с проверкой подлинности и шифрованием используется как конфигурация по умолчанию, так как корпорация Майкрософт считает такой уровень безопасности минимально допустимым для рабочих сред.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Если вы хотите только протестировать интеграцию клиента Azure Stack Hub с сервером системного журнала, используя самозаверяющий и (или) не доверенный сертификат, укажите следующие флаги для пропуска проверки сервера, которую клиент выполняет при первоначальном подтверждении.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Корпорация Майкрософт не рекомендует использовать флаг -SkipCertificateCheck для рабочих сред. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Настройка перенаправления системного журнала по протоколу TCP без шифрования

В этой конфигурации клиент системного журнала в Azure Stack Hub передает сообщения на сервер системного журнала по протоколу TCP без шифрования. Теперь клиент не будет проверять подлинность сервера и предоставлять серверу сведения для собственной идентификации.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Корпорация Майкрософт настоятельно рекомендует не использовать эту конфигурацию для рабочей среды.


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Настройка перенаправления системного журнала по протоколу UDP без шифрования

В этой конфигурации клиент системного журнала в Azure Stack Hub передает сообщения на сервер системного журнала по протоколу UDP без шифрования. Теперь клиент не будет проверять подлинность сервера и предоставлять серверу сведения для собственной идентификации.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Настройка протокола UDP без шифрования будет самой простой, но этот вариант не обеспечивает защиту от атак "злоумышленник в середине" и (или) от неавторизованного прослушивания сообщений.

> [!IMPORTANT]
> Корпорация Майкрософт настоятельно рекомендует не использовать эту конфигурацию для рабочей среды.


## <a name="removing-syslog-forwarding-configuration"></a>Удаление конфигурации перенаправления системного журнала

Чтобы полностью удалить конфигурацию сервера системного журнала и прекратить перенаправление системного журнала, выполните следующие действия.

**Удаление из клиента конфигурации сервера системного журнала**

```powershell  
Set-SyslogServer -Remove
```

**Удаление из клиента сертификата клиента**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Проверка настройки системного журнала

Прием событий начнется автоматически, когда клиент системного журнала успешно подключиться к серверу системного журнала. Если вы не видите принятых событий, проверьте конфигурацию клиента системного журнала, выполнив следующие командлеты.

**Проверка конфигурации сервера системного журнала в клиенте**

```powershell  
Get-SyslogServer
```

**Проверка настроек сертификата в клиенте системного журнала**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Схема сообщений системного журнала

Перенаправление системного журнала в инфраструктуре Azure Stack Hub позволяет отправлять сообщения в формате CEF.
Каждое сообщение системного журнала имеет следующую структуру:

```Syslog
<Time> <Host> <CEF payload>
```

Полезные данные CEF имеют описанную ниже структуру, но сопоставление конкретных полей зависит от типа сообщения (событие Windows, созданное оповещение, закрытое оповещение).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack Hub
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Сопоставление CEF для событий привилегированной конечной точки

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Таблица событий для привилегированной конечной точки.

| Событие | Идентификатор события привилегированной конечной точки. | Имя задачи привилегированной конечной точки | Severity |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

Таблица уровней серьезности для привилегированной конечной точки.

| Severity | Level | Числовое значение |
|----------|-------| ----------------|
|0|Не определено.|Значение: 0. Обозначает журналы всех уровней.|
|10|Critical|Значение: 1. Обозначает журналы критических оповещений.|
|8|Error| Значение: 2. Обозначает журналы ошибок.|
|5|Предупреждение|Значение: 3. Обозначает журналы предупреждений.|
|2|Сведения|Значение: 4. Обозначает журналы для информационных сообщений.|
|0|Подробный|Значение: 5. Обозначает журналы всех уровней.|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Сопоставление CEF для событий конечной точки восстановления

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Таблица событий для конечной точки восстановления.

| Событие | Идентификатор события конечной точки восстановления | Имя задачи конечной точки восстановления | Severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Уровень серьезности для конечной точки восстановления

| Severity | Level | Числовое значение |
|----------|-------| ----------------|
|0|Не определено.|Значение: 0. Обозначает журналы всех уровней.|
|10|Critical|Значение: 1. Обозначает журналы критических оповещений.|
|8|Error| Значение: 2. Обозначает журналы ошибок.|
|5|Предупреждение|Значение: 3. Обозначает журналы предупреждений.|
|2|Сведения|Значение: 4. Обозначает журналы для информационных сообщений.|
|0|Подробный|Значение: 5. Обозначает журналы всех уровней.|

### <a name="cef-mapping-for-windows-events"></a>Сопоставление полей CEF для событий Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Таблица уровней серьезности для событий Windows

| Уровень серьезности CEF | Уровень события Windows | Числовое значение |
|--------------------|---------------------| ----------------|
|0|Не определено.|Значение: 0. Обозначает журналы всех уровней.|
|10|Critical|Значение: 1. Обозначает журналы критических оповещений.|
|8|Error| Значение: 2. Обозначает журналы ошибок.|
|5|Предупреждение|Значение: 3. Обозначает журналы предупреждений.|
|2|Сведения|Значение: 4. Обозначает журналы для информационных сообщений.|
|0|Подробный|Значение: 5. Обозначает журналы всех уровней.|

Таблица пользовательских расширений для событий Windows в Azure Stack Hub:

| Имя пользовательского расширения | Пример события Windows | 
|-----------------------|---------|
|MasChannel | Система|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| Параметры групповой политики для пользователя были успешно обработаны. Не обнаружено изменений с момента последней успешной обработки групповой политики.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Аудит выполнен успешно|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |сведения|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<ИД безопасности Windows\> |
|MasTask |0|
|MasTaskCategory| Создание процесса|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Сопоставление полей CEF для созданных оповещений

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Таблица уровней серьезности для оповещений

| Severity | Level |
|----------|-------|
|0|Не определено.|
|10|Critical|
|5|Предупреждение|

Таблица пользовательских расширений для оповещений, созданных в Azure Stack Hub:

| Имя пользовательского расширения | Пример | 
|-----------------------|---------|
|MasEventDescription|Описание. Создана учетная запись пользователя \<TestUser\> для \<TestDomain\>. Это может означать угрозу безопасности. Исправление: Обратитесь в службу поддержки. Для устранения этой проблемы потребуется помощь службы поддержки клиентов. Не пытайтесь устранять эту проблему самостоятельно. Прежде чем отправить запрос в службу поддержки, запустите процесс сбора файлов журнала по рекомендациям из статьи https://aka.ms/azurestacklogfiles.

### <a name="cef-mapping-for-alerts-closed"></a>Сопоставление полей CEF для закрытых оповещений

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Ниже приводится пример сообщения системного журнала с полезными данными в формате CEF.
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Дальнейшие действия

[Политика обслуживания](azure-stack-servicing-policy.md)
