---
title: Смена секретов
titleSuffix: Azure Stack Hub
description: Сведения о смене секретов в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: da9ed84f325612aa9b6261daed63a3eba7684f73
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815108"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Узнайте о том, как менять секреты в Azure Stack Hub

*Эти инструкции применяются только к интегрированным системам Azure Stack Hub 1803 и выше. Не пытайтесь сменить секреты в Azure Stack Hub до версии 1802.*

Секреты обеспечивают безопасный обмен данными между службами и ресурсами инфраструктуры Azure Stack Hub.

## <a name="rotate-secrets-overview"></a>Обзор смены секретов

1. Подготовьте сертификаты, которые будут использоваться для смены секретов.
2. См. [требования к сертификатам инфраструктуры открытых ключей](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs) в Azure Stack Hub.
3. [Откройте привилегированную конечную точку](azure-stack-privileged-endpoint.md) и запустите **Test-azurestack**, чтобы проверить все условия.  
4. Изучите [предварительные действия для смены секретов](#pre-steps-for-secret-rotation).
5. [Проверка сертификатов PKI Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Убедитесь, что в пароле нет специальных символов, таких как `*` или `)`.
6. Убедитесь, что для PFX-файлов используется шифрование **TripleDES-SHA1**. Если у вас возникнут проблемы, см. руководство по [устранению распространенных неполадок с сертификатами PKI в Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption).
7. Подготовьте структуру папок.  Пример можно найти в разделе [Смена внешних секретов](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets).
8. [Начните смену секретов](#use-powershell-to-rotate-secrets).

## <a name="rotate-secrets"></a>Смена секретов

Azure Stack Hub использует разные секреты, чтобы обеспечивать безопасный обмен данными между службами и ресурсами инфраструктуры Azure Stack Hub.

- **Внутренние секреты**

    Все сертификаты, пароли, защищенные строки и ключи, используемые инфраструктурой Azure Stack Hub без вмешательства оператора Azure Stack Hub.

- **Внешние секреты**

    Сертификаты службы инфраструктуры для внешних служб, предоставляемых оператором Azure Stack Hub. Внешние секреты содержат сертификаты для следующих служб:

    - портал администрирования;
    - общедоступный портал;
    - Azure Resource Manager для администратора;
    - глобальный Azure Resource Manager;
    - Key Vault администратора;
    - Key Vault
    - Хост-процесс для расширений администратора
    - ACS (включая хранилище BLOB-объектов, таблиц и очередей);
    - ADFS*;
    - Graph*.
    
    \* Применимо, только если поставщиком удостоверений среды являются службы федерации Active Directory (AD FS).

> [!Note]
> Все остальные защищенные ключи и строки, включая пароли BMC и коммутатора, пароли учетной записи пользователя и администратора, по-прежнему вручную обновляются администратором.

> [!Important]
> Начиная с Azure Stack Hub 1811, смена секретов выполняется отдельно для внутренних и внешних сертификатов.

Чтобы поддерживать целостность инфраструктуры Azure Stack Hub, операторам нужно менять секреты своей инфраструктуры с частотой, соответствующей требованиям безопасности организации.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Смена секретов с помощью внешних сертификатов из нового центра сертификации

Azure Stack Hub поддерживает смену секретов с помощью внешних сертификатов из нового центра сертификации (ЦС) в следующих контекстах.

|Установленный ЦС|Целевой ЦС для смены секрета|Поддерживается|Поддерживаемые версии Azure Stack Hub|
|-----|-----|-----|-----|
|С самозаверяющими сертификатами|Корпоративный|Поддерживается|1903 и более поздние версии|
|С самозаверяющими сертификатами|С самозаверяющими сертификатами|Не поддерживается||
|С самозаверяющими сертификатами|Общедоступный<sup>*</sup>|Поддерживается|1803 и более поздние версии|
|Корпоративный|Корпоративный|Поддерживается. Версии 1803–1903: поддерживается, если клиенты используют ТОТ ЖЕ корпоративный ЦС, что и во время развертывания.|1803 и более поздние версии|
|Корпоративный|С самозаверяющими сертификатами|Не поддерживается||
|Корпоративный|Общедоступный<sup>*</sup>|Поддерживается|1803 и более поздние версии|
|Общедоступный<sup>*</sup>|Корпоративный|Поддерживается|1903 и более поздние версии|
|Общедоступный<sup>*</sup>|С самозаверяющими сертификатами|Не поддерживается||
|Общедоступный<sup>*</sup>|Общедоступный<sup>*</sup>|Поддерживается|1803 и более поздние версии|

<sup>*</sup>Указывает, что общедоступными называются центры сертификации, которые являются частью программы доверенных корневых центров сертификации Windows. Вы можете ознакомиться с полным списком в статье [Microsoft Trusted Root Certificate Program: Participants (as of June 27, 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) (Список участников программы доверенных корневых центров сертификации Майкрософт по состоянию на 27 июня 2017 г.).

## <a name="fixing-alerts"></a>Исправление оповещений

Когда до истечения срока действия секретов осталось 30 дней, на портале администратора появляются следующие оповещения:

- ожидается истечение срока действия пароля учетной записи службы;
- ожидается истечение срока действия внутреннего сертификата;
- ожидается истечение срока действия внешнего сертификата.

Выполнение смены секрета с помощью описанных ниже инструкций исправит эти оповещения.

> [!Note]
> В средах Azure Stack Hub до версии 1811 могут возникать предупреждения об ожидающих обработки внутренних сертификатах или об окончании срока действия секретов. Эти предупреждения являются неточными. Их следует игнорировать без выполнения смены внутренних секретов. Неточные оповещения об истечении срока действия внутренних секретов — это известная проблема, которая устранена в версии 1811. Срок действия внутренних секретов не истекает, если среда была активна в течение двух лет.

## <a name="pre-steps-for-secret-rotation"></a>Предварительные шаги для смены секретов

   > [!IMPORTANT]
   > Если смена секретов уже выполнена в среде Azure Stack Hub, обновите систему до версии 1811 или выше, прежде чем еще раз выполнить смену секретов. Смена секретов выполняется через [привилегированную конечную точку](azure-stack-privileged-endpoint.md), и для нее нужны учетные данные оператора Azure Stack Hub. Если операторы вашей среды Azure Stack Hub не знают, выполнялась ли смена секретов в этой среде, обновите систему до версии 1811, прежде чем еще раз выполнить смену секретов.

1. Мы настоятельно рекомендуем обновить экземпляр Azure Stack до версии 1811.

    > [!Note]
    > В версиях до 1811 вам не нужно выполнять смену секретов для добавления сертификатов хост-процесса для расширений. См. руководство по [подготовке хост-процесса для расширений для Azure Stack Hub и добавлению сертификатов](azure-stack-extension-host-prepare.md).

2. Операторы могут отметить, что во время смены секретов Azure Stack Hub появляются и автоматически закрываются предупреждения.  Такое поведение считается нормальным и предупреждения можно игнорировать.  Операторы могут проверить действительность этих предупреждений, выполнив команду **Test-AzureStack**.  Если операторы, использующие System Center Operations Manager для мониторинга систем Azure Stack Hub, переведут системы в режим обслуживания, предупреждений больше не будет в системах ITSM, но они будут появляться, если система Azure Stack Hub станет недоступной.

3. Уведомите пользователей об операциях обслуживания. Запланируйте стандартные окна обслуживания, по возможности в нерабочие часы. Операции технического обслуживания могут влиять на рабочие нагрузки пользователей и на операции портала.

    > [!Note]
    > Следующие шаги применяются только при смене внешних секретов Azure Stack Hub.

4. Перед сменой секретов запустите **[Test-AzureStack](azure-stack-diagnostic-test.md)** и убедитесь, что все выходные данные тестов свидетельствуют о работоспособности.
5. Подготовьте новый набор внешних сертификатов для замены. Новый набор соответствует [требованиям к сертификатам инфраструктуры открытых ключей Azure Stack Hub](azure-stack-pki-certs.md). Вы можете создать запрос на подпись сертификата (CSR), чтобы [приобрести или создать новые сертификаты PKI](azure-stack-get-pki-certs.md) и [подготовить их к использованию в среде Azure Stack Hub](azure-stack-prepare-pki-certs.md). Обязательно проверьте подготавливаемые сертификаты, следуя инструкциям в статье [Проверка сертификатов PKI](azure-stack-validate-pki-certs.md).
6. Сохраните резервную копию в сертификаты, используемые для смены, в защищенном расположении резервной копии. Если смена выполняется, а затем завершается со сбоем, замените сертификаты в общей папке резервными копиями перед повторным запуском смены. Резервные копии необходимо сохранить в безопасном расположении.
7. Создайте общую папку, к которой можно получить доступ из виртуальных машин ERCS. Общая папка должна быть доступна для чтения и записи для удостоверения **CloudAdmin**.
8. Откройте консоль интегрированной среды сценариев PowerShell на компьютере с доступом к общей папке. Перейдите к общей папке.
9. Запустите файл **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** , чтобы создать требуемые каталоги для внешних сертификатов.

> [!IMPORTANT]
> Сценарий CertDirectoryMaker создаст структуру папок формата
>
> **.\Certificates\AAD** или ***.\Certificates\ADFS***, в зависимости от поставщика удостоверений, используемого для Azure Stack Hub.
>
> Крайне важно, чтобы ваша структура папок заканчивалась папками **AAD** или **ADFS**, а все подкаталоги находились в этой структуре. В противном случае появится строка **Start-SecretRotation**:
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> В сообщении об ошибке указывается, что возникла проблема с доступом к общей папке, но на самом деле здесь принудительно применяется структура папок. Дополнительные сведения можно найти в [модуле PublicCertHelper](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1) средства проверки готовности Microsoft AzureStack.
>
> Важно также, чтобы структура вашей общей папки начиналась с папки **Certificates**, иначе она также не пройдет проверку.
> Путь для подключения общей папки должен выглядеть как **\\\\\<IP-адрес>\\\<имя общей папки>\\** и содержать внутри папку **Certificates\AAD** или **Certificates\ADFS**.
>
> Пример:
> - Fileshare = **\\\\\<IP-адрес>\\\<имя общей папки>\\** .
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IP-адрес>\\\<имя общей папки>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Смена внешних секретов

Чтобы выполнить смену внешних секретов, сделайте следующее.

1. В каталоге **\Certificates\\\<поставщик_удостоверений>** , созданном на предварительном этапе, установите новый набор внешних сертификатов для замены в структуру каталога в соответствии с **форматом обязательных сертификатов** (см. [перечень требований к сертификатам PKI Azure Stack Hub](azure-stack-pki-certs.md#mandatory-certificates)).

    Пример структуры папок для поставщика удостоверений Azure AD:
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Создайте сеанс PowerShell с [привилегированной конечной точкой](azure-stack-privileged-endpoint.md) с использованием учетной записи **CloudAdmin** и сохраните сеансы в качестве переменных. Эта переменная будет использоваться как параметр в следующем шаге.

    > [!IMPORTANT]  
    > Не входите в сеанс. Сохраните сеанс как переменную.

3. Выполните команду **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Передайте переменную сеанса PowerShell с привилегированной конечной точкой в качестве параметра **Сеанс**.

4. Выполните команду **Start-SecretRotation** со следующими параметрами:
    - **PfxFilesPath**  
    Укажите сетевой путь к созданному ранее каталогу сертификатов.  
    - **PathAccessCredential**  
    Объект PSCredential для учетных данных в общей папке.
    - **CertificatePassword**  
    Защищенная строка пароля, используемая для всех созданных файлов сертификатов PFX.

5. Дождитесь смены секретов. Смена внешних секретов занимает около часа.

    Когда смена сертификатов будет успешно выполнена, в консоли появится следующее: **Overall action status: Success** (Общее состояние действия: успешно выполнено)

    > [!Note]
    > Если не удается выполнить смену секретов, следуйте инструкциям в сообщении об ошибке и повторно запустите командлет **Start-SecretRotation** с параметром **-ReRun**.

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Обратитесь в службу поддержки, если при смене секретов постоянно возникают сбои.

6. После успешного завершения смены секретов удалите свои сертификаты из общего ресурса, созданного на предварительном шаге, и сохраните их в безопасном расположении резервного копирования.

## <a name="use-powershell-to-rotate-secrets"></a>Использование PowerShell для смены секретов

Следующий пример PowerShell демонстрирует командлеты и параметры, необходимые для смены секретов.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Смена только внутренних секретов

> [!Note]
> Смену внутренних секретов следует выполнять только в том случае, если вы подозреваете, что внутренний секрет был скомпрометирован вредоносным объектом, или если вы получили оповещение (в версии 1811 или более поздней), указывающее, что срок действия внутренних сертификатов скоро истечет. В средах Azure Stack Hub до версии 1811 могут возникать предупреждения об ожидающих обработки внутренних сертификатах или об окончании срока действия секретов. Эти предупреждения являются неточными. Их следует игнорировать без выполнения смены внутренних секретов. Неточные оповещения об истечении срока действия внутренних секретов — это известная проблема, которая устранена в версии 1811. Срок действия внутренних секретов не истекает, если среда была активна в течение двух лет.

1. Создайте сеанс PowerShell с [привилегированной конечной точкой](azure-stack-privileged-endpoint.md).
2. В сеансе с привилегированной конечной точкой выполните **Start-SecretRotation -Internal**.

    > [!Note]
    > В средах Azure Stack Hub до версии 1811 использовать флаг **-Internal** не требуется. Командлет **Start-SecretRotation** сменяет только внутренние секреты.

3. Дождитесь смены секретов.

   Когда смена сертификатов будет успешно выполнена, в консоли появится следующее: **Overall action status: Success** (Общее состояние действия: успешно выполнено)
    > [!Note]
    > Если не удается выполнить смену секретов, следуйте инструкциям в сообщении об ошибке и повторно выполните командлет **Start-SecretRotation** с параметрами **-Internal** и **-ReRun**.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Обратитесь в службу поддержки, если при смене секретов постоянно возникают сбои.

## <a name="start-secretrotation-reference"></a>Справочник по Start-SecretRotation

Этот командлет меняет секреты в системе Azure Stack Hub. Он выполняется только с использованием привилегированной конечной точки Azure Stack Hub.

### <a name="syntax"></a>Синтаксис

#### <a name="for-external-secret-rotation"></a>Для смены внешних секретов

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Для смены внутренних секретов

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Для повторной смены внешних секретов

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Для повторной смены внутренних секретов

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>Description

Командлет **Start-SecretRotation** меняет секреты инфраструктуры в системе Azure Stack Hub. По умолчанию он сменяет только сертификаты всех конечных точек инфраструктуры внешней сети. При использовании флага -Internal будут сменяться внутренние секреты инфраструктуры. При смене конечных точек инфраструктуры внешней сети командлет **Start-SecretRotation** необходимо выполнить в блоке скрипта **Invoke-Command** в сеансе с привилегированной конечной точкой среды Azure Stack Hub, переданном в параметре **Session**.

### <a name="parameters"></a>Параметры

| Параметр | Тип | Обязательно | Положение | По умолчанию | Description |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False  | именованная  | None  | Путь к общей папке в каталоге **\Certificates**, который содержит все сертификаты конечных точек внешней сети. Требуется только при смене внешних секретов. Конечным каталогом должен быть **\Certificates**. |
| `CertificatePassword` | SecureString | False  | именованная  | None  | Пароль для всех сертификатов, предоставляемых в -PfXFilesPath. Необходимое значение, если PfxFilesPath предоставлен, когда сменяются внешние секреты. |
| `Internal` | String | False | именованная | None | Флаг -Internal должен использоваться каждый раз, когда оператор Azure Stack Hub хочет сменить внутренние секреты инфраструктуры. |
| `PathAccessCredential` | PSCredential | False  | именованная  | None  | Учетные данные PowerShell для общего ресурса каталога **\Certificates**, который содержит все сертификаты конечных точек внешней сети. Требуется только при смене внешних секретов.  |
| `ReRun` | SwitchParameter | False  | именованная  | None  | Повторное выполнение необходимо использовать при каждой повторной попытке смены секретов после неудачи. |

### <a name="examples"></a>Примеры

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Смена только секретов внутренней инфраструктуры

Эту команду необходимо выполнять с использованием [привилегированной конечной точки среды](azure-stack-privileged-endpoint.md) Azure Stack Hub.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Эта команда меняет все инфраструктурные секреты, предоставляемые для внутренней сети Azure Stack Hub.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Смена только секретов внешней инфраструктуры  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Эта команда сменяет сертификаты TSL, используемые для конечных точек инфраструктуры внешней сети Azure Stack Hub.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Смена секретов внешней и внутренней инфраструктуры (только **до версии 1811**).

> [!IMPORTANT]
> Эта команда применяется только к Azure Stack **до версии 1811**, так как в ней смена для внутренних и внешних сертификатов выполняется раздельно.
>
> **Начиная с версии *1811+* вы больше не сможете сменять внутренние и внешние сертификаты.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Эта команда меняет все секреты инфраструктуры, предоставляемые во внутренней сети Azure Stack Hub, а также сертификаты TSL, используемые конечными точками инфраструктуры внешней сети Azure Stack Hub. Командлет Start-SecretRotation сменяет все созданные стеком секреты и, так как сертификаты предоставлены, сертификаты внешних конечных точек.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Обновление учетных данных контроллера управления основной платой (BMC)

Контроллер управления основной платой отслеживает физическое состояние серверов. Обратитесь к поставщику оборудования OEM, чтобы получить инструкции по обновлению пароля и имени учетной записи пользователя для BMC.

>[!NOTE]
> Поставщик OEM может предоставлять дополнительные приложения для управления. Изменение имени пользователя или пароля для других приложений управления не влияет на имя пользователя или пароль для BMC.

1. **Версии ниже 1910**. Обновите контроллер управления основной платой на физических серверах Azure Stack Hub в соответствии с инструкциями поставщика. Пароли и имена пользователя для всех контроллеров BMC в среде должны совпадать. Имена пользователей BMC должны содержать не более 16 знаков.

   **1910 и более поздние версии**. Вам больше не нужно обновлять учетные данные контроллера управления основной платой на физических серверах Azure Stack Hub в соответствии с инструкциями поставщиков OEM. Пароли и имена пользователя для всех контроллеров BMC в среде должны совпадать. Имена пользователей BMC должны содержать не более 16 знаков.

    | Параметр | Description | Штат |
    | --- | --- | --- |
    | BypassBMCUpdate | При использовании этого параметра учетные данные в BMC не обновляются. Обновляется только внутреннее хранилище данных Azure Stack Hub. | Необязательно |

2. Откройте привилегированную конечную точку в сеансах Azure Stack Hub. См. инструкции по [использованию привилегированной конечной точки в Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. После изменения запроса PowerShell на **[IP-адрес или имя виртуальной машины ERCS]: PS>** или на **[azs-ercs01]: PS>** (в зависимости от среды) выполните `Set-BmcCredential`, запустив `Invoke-Command`. Передайте переменную сеанса привилегированной конечной точки в качестве параметра. Пример:

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Также можно использовать статическую версию PowerShell с паролями в качестве строк кода:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о системе безопасности Azure Stack Hub](azure-stack-security-foundations.md)
