---
title: Заметки к выпуску Пакета средств разработки Azure Stack | Документация Майкрософт
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 8de4447cd30204d0d4e1611afd057a75dc7688da
ms.sourcegitcommit: 2cd17b8e7352891d8b3eb827d732adf834b7693e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428681"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](../operator/azure-stack-updates.md#determine-the-current-version).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11904036"></a>Сборка 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Так как может произойти превышение времени ожидания у субъекта-службы при выполнении скрипта регистрации, чтобы успешно [зарегистрировать ASDK](asdk-register.md), необходимо изменить скрипт PowerShell **RegisterWithAzure.psm1**. Выполните следующее:

  1. На главном компьютере ASDK откройте в редакторе файл **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** с повышенным уровнем разрешений.
  2. В конце строки 1249 добавьте параметр `-TimeoutInSeconds 1800`. Это необходимо, чтобы избежать превышение времени ожидания у субъекта-службы при запуске скрипта регистрации. Теперь строка 1249 должна выглядеть следующим образом:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Исправлены проблемы подключения VPN, обнаруженные [здесь, в выпуске 1902](#known-issues).

- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1904.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1904.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-1903"></a>Сборка 1903

В полезные данные обновления 1903 не включен выпуск ASDK.

## <a name="build-11902069"></a>Сборка 1.1902.0.69

### <a name="new-features"></a>новые функции;

- В сборке 1902 для портала администрирования Azure Stack представлен новый пользовательский интерфейс для создания планов, предложений, квот и дополнительных планов. Дополнительные сведения, включая снимки экрана, см. в статье [Создание плана в Azure Stack](../operator/azure-stack-create-plan.md).

- Список других изменений и усовершенствований для этого выпуска см. в [этом разделе](../operator/azure-stack-update-1902.md#improvements) заметок о выпуске Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Известные проблемы

- Проблема установления VPN-подключения с другого узла в ASDK с помощью действия, описанного в [этой статье](asdk-connect.md). При попытке подключиться к среде ASDK из VPN-клиента вы получите сообщение об ошибке, что **указано неверное имя пользователя или пароль**, даже в том случае, если вы уверены, что используется правильная учетная запись и вы правильно ввели пароль. Проблема заключается не в ваших учетных данных, а скорее в изменениях протокола проверки подлинности, используемого для VPN-подключения в ASDK. Чтобы обойти эту проблему, выполните следующие действия.

   Сначала внесите изменения в протокол проверки подлинности, используемый на стороне сервера ASDK.

   1. RDP к узлу ASDK.
   2. Откройте сеанс PowerShell с повышенными правами, войдя в систему как AzureStack\AzureStackAdmin c использованием пароля, указанного во время развертывания.
   3. Выполните следующие команды:

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   Далее измените скрипт подключений на стороне клиента. Самый простой способ сделать это — внести изменения непосредственно в модуль сценария C:\AzureStack-Tools-master\connect\azurestack.connect.psm1.

   1. Измените командлет **Add-AzsVpnConnection**, чтобы изменить параметр `AuthenticationMethod` с `MsChapv2` на `EAP`.

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Измените командлет **Connect-AzsVpn** с использования `rasdial @ConnectionName $User $PlainPassword` на `rasphone`, поскольку в EAP требуется интерактивный вход.

      ```powershell
      rasphone $ConnectionName
      ```

   3. Сохраните изменения и повторно импортируйте модуль **azurestack.connect.psm1**.
   4. Следуйте инструкциям в [этой статье](asdk-connect.md#set-up-vpn-connectivity).
   5. При подключении к ASDK через VPN подключитесь, войдя в **Параметры сети и Интернета** Windows, затем выберите **VPN**, вместо того чтобы подключаться с панели задач, чтобы убедиться, что предлагается ввести учетные данные.

- Обнаружена проблема, из-за которой отбрасываются пакеты размером более 1450 байт для внутреннего балансировщика нагрузки (ILB). Проблема связана с тем, что значение параметра MTU на узле слишком низкое для размещения инкапсулированных пакетов VXLAN, проходящих роль, которая в сборке 1901 была перемещена на узел. Есть как минимум два сценария, при работе с которыми наблюдается проявление этой проблемы:

  - SQL-запросы к группам доступности SQL Always On, находящиеся за внутренним балансировщиком нагрузки (ILB), размером более 660 байт.
  - Возникновение ошибки во время развертывания Kubernetes при попытке включить несколько главных экземпляров.  

  Эта проблема возникает при наличии связи между виртуальной машиной и внутренним балансировщиком нагрузки в той же виртуальной сети, но в разных подсетях. Вы можете обойти эту проблему, выполнив следующие команды в командной строке с повышенными привилегиями на узле ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Сборка 1.1901.0.95

Дополнительные сведения см. в разделе [Указание сборки](../operator/azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>изменения

Эта сборка включает в себя следующие улучшения для Azure Stack.

- Компоненты преобразования сетевых адресов (NAT) и BGP теперь развернуты на физическом узле. Это избавляет от необходимости иметь два общедоступных или корпоративных IP-адреса для развертывания ASDK, а также упрощает развертывание.
- Резервные копии интегрированных систем Azure Stack теперь можно [проверить](asdk-validate-backup.md) с помощью сценария PowerShell **asdk-installer.ps1**.

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-update-1901.md#new-features) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-update-1901.md#fixed-issues) заметок к выпуску Azure Stack. Список известных проблем см. в [этом разделе](../operator/azure-stack-update-1901.md#known-issues-post-installation).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-update-1901.md#azure-stack-hotfixes) нельзя применять к Azure Stack ASDK.
