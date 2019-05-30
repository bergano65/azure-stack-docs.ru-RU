---
title: Подключение к Azure Stack | Документация Майкрософт
description: Сведения о подключении к пакету ASDK.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 9a16201db137e7f34b42a0b12bbe802d3b10bdff
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267215"
---
# <a name="connect-to-the-asdk"></a>Подключение к пакету ASDK

Для управления ресурсами сначала необходимо подключиться к пакету средств разработки Azure Stack (ASDK). В этой статье описаны действия, которые помогут вам подключиться к пакету ASDK. Есть следующие варианты подключения:

* [Подключение к удаленному рабочему столу (RDP).](#connect-with-rdp) При подключении с помощью удаленного рабочего стола один пользователь может быстро выполнить подключение к пакету средств разработки.
* [Виртуальная частная сеть (VPN).](#connect-with-vpn) При подключении с помощью VPN несколько пользователей могут подключаться к порталам Azure Stack одновременно с использованием клиентов за пределами инфраструктуры Azure Stack. Для VPN-подключения требуются некоторые настройки.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Подключение к Azure Stack с помощью RDP

Подключение удаленного рабочего стола позволяет одному пользователю единовременно подключаться напрямую с узла ASDK и управлять ресурсами на портале администратора или пользовательском портале Azure Stack. 

> [!TIP]
> Этот вариант также позволяет при подключении к узлу ASDK использовать RDP для входа на виртуальные машины, созданные в узле ASDK. 

1. Откройте подключение удаленного рабочего стола (mstc.exe) и подключитесь к IP-адресу узла пакета разработки, используя учетную запись с правом на удаленный вход в узел ASDK. По умолчанию право удаленно подключаться к узлу ASDK предоставлено пользователю **AzureStack\AzureStackAdmin**.  

2. Откройте диспетчер сервера (ServerManager.exe) на компьютере с пакетом средств разработки. Выберите **Локальный сервер**, отключите **конфигурацию усиленной безопасности Internet Explorer** и закройте диспетчер сервера.

3. Войдите на портал администрирования с учетной записью **AzureStack\CloudAdmin** или используйте другие учетные данные оператора Azure Stack. Адрес портала администрирования ASDK: [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Войдите на пользовательский портал с учетной записью **AzureStack\CloudAdmin** или используйте другие учетные данные пользователя Azure Stack. Адрес пользовательского портала ASDK: [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Дополнительные сведения о том, когда следует использовать ту или иную учетную запись, см. в разделе [Основы администрирования ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Подключение к Azure Stack с помощью VPN

К узлу ASDK можно установить VPN-подключение с разделенным туннелированием, чтобы получать доступ к порталам Azure Stack и установленным локально инструментам, таким как Visual Studio и PowerShell. Используя VPN-подключения, несколько пользователей могут одновременно подключаться к ресурсам Azure Stack, размещенным в ASDK.

VPN-подключение поддерживается в развертываниях Azure AD и служб федерации Active Directory (AD FS).

> [!NOTE]
> VPN-подключение *не предоставляет* возможность подключения к виртуальным машинам Azure Stack. Подключившись через VPN, вы не сможете использовать RDP для подключения к виртуальным машинам Azure Stack.

### <a name="prerequisites"></a>Предварительные требования
Прежде чем настраивать VPN-подключение к ASDK, убедитесь, что выполнены следующие предварительные требования.

- Установите [Azure PowerShell, совместимый с Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell), на своем локальном компьютере.  
- Скачайте [средства, необходимые для работы с Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Настройка VPN-подключения

Чтобы создать VPN-подключение к пакету ASDK, откройте PowerShell с правами администратора на локальном компьютере под управлением Windows. Затем выполните следующий скрипт (обновите значения IP-адреса и пароля для своей среды):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Если программа установки завершается успешно, **Azure Stack** появится в списке VPN-подключений.

![Сетевые подключения](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

  Подключитесь к экземпляру Azure Stack, используя один из следующих двух способов:  

  * Использование команды `Connect-AzsVpn`:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * На локальном компьютере выберите **Параметры сети** > **VPN** > **Azure Stack** > **Подключиться**. При запросе на вход введите имя пользователя (**AzureStack\AzureStackAdmin**) и пароль.

При первом подключении вы увидите запрос на установку корневого сертификата Azure Stack из **AzureStackCertificateAuthority** в хранилище сертификатов на локальном компьютере. Это позволяет добавить центр сертификации ASDK в список доверенных узлов. Нажмите **Да**, чтобы установить сертификат.

![Корневой сертификат](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Запрос может быть скрыт окном PowerShell или другим приложением.

### <a name="test-vpn-connectivity"></a>Проверка VPN-подключения

Чтобы проверить соединение с порталом, откройте веб-браузер и перейдите на пользовательский портал (https://portal.local.azurestack.external/) или на портал администрирования (https://adminportal.local.azurestack.external/). 

Войдите с соответствующими учетными данными, чтобы создавать ресурсы и управлять ими.  

## <a name="next-steps"></a>Дополнительная информация

[Устранение неполадок](asdk-troubleshooting.md)
