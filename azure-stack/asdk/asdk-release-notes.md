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
ms.date: 06/28/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: ba3ad4bf5e5d7f76d5d29e7967944be72e989c27
ms.sourcegitcommit: 068350a79805366e7e6536fb7df85a412bd0be99
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67511289"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](../operator/azure-stack-updates.md#determine-the-current-version).

Будьте в курсе новых возможностей ASDK, подписавшись на [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11906030"></a>Сборка 1.1906.0.30

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="changes"></a>изменения

- Добавлена поддержка виртуальной машины для вызова **AzS-SRNG01**, в которой размещена служба сбора журналов для Azure Stack. Подробные сведения см. в статье [Архитектура Пакета средств разработки Azure Stack](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- При создании ресурсов виртуальной машины с помощью некоторых образов Marketplace развертывание могло не завершаться корректно. В качестве временного решения этой проблемы вы можете щелкнуть ссылку **Скачать шаблон и параметры** на странице **Сводка** и щелкнуть кнопку **Развернуть** в колонке **Шаблон**. 
- Список проблем Azure Stack, которые были исправлены в этом выпуске, см. в [этом разделе](../operator/azure-stack-release-notes-1906.md#fixes) заметок о выпуске Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1906.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-11905040"></a>Сборка 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Исправлена проблема, при которой вам приходилось редактировать скрипт PowerShell **RegisterWithAzure.psm1** для успешной [регистрации ASDK](asdk-register.md).
- Список исправленных проблем для этого выпуска см. в [этом разделе](../operator/azure-stack-release-notes-1905.md#fixes) заметок к выпуску Azure Stack.
- Список известных проблем см. в [этой статье](../operator/azure-stack-release-notes-known-issues-1905.md).
- Обратите внимание, что [доступные исправления Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) нельзя применять к Azure Stack ASDK.

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
