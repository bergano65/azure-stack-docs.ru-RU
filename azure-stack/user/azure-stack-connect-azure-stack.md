---
title: Подключение к Azure Stack Hub | Документация Майкрософт
description: Из этой статьи вы узнаете, как подключиться к Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cb6a1e99def82625d01864601c504b62425b7a5f
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535898"
---
# <a name="connect-to-azure-stack-hub"></a>Подключение к Azure Stack Hub

Для управления ресурсами необходимо подключиться к Пакету средств разработки Azure Stack. В этой статье описаны действия, требуемые для подключения к комплекту разработки. Можно использовать один из следующих вариантов подключения:

* Удаленный рабочий стол. Позволяет быстро выполнить параллельное подключение одного пользователя из пакета разработки.
* Виртуальная частная сеть (VPN). Позволяет выполнить параллельное подключение нескольких пользователей из клиентов за пределами инфраструктуры Azure Stack Hub (требуется настройка).

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>Подключение к Azure Stack Hub с помощью удаленного рабочего стола
С помощью подключения удаленного рабочего стола один пользователь может работать с порталом для управления ресурсами.

1. Откройте подключение к удаленному рабочему столу и подключитесь к комплекту разработки. Введите **AzureStack\AzureStackAdmin** в качестве имени пользователя и пароль администратора, указанный во время настройки Azure Stack Hub.  

2. На компьютере с комплектом разработки откройте диспетчер сервера, щелкните **Локальный сервер**, отключите усиленную безопасность Internet Explorer и закройте диспетчер сервера.

3. Чтобы открыть портал, перейдите по адресу (https://portal.local.azurestack.external/) и выполните вход с помощью учетных данных.


## <a name="connect-to-azure-stack-hub-with-vpn"></a>Подключение к Azure Stack Hub с помощью VPN

Вы можете установить VPN-подключение с разделенным туннелем к Пакету средств разработки Azure Stack. Через VPN-подключение можно получить доступ к порталу администратора, порталу пользователя и установленным локально средствам, таким как Visual Studio и PowerShell, для управления ресурсами Azure Stack Hub. VPN-подключение поддерживается в развертываниях на базе Azure Active Directory (Azure AD) и служб федерации Active Directory (AD FS). VPN-подключения позволяют нескольким клиентам одновременно подключаться к Azure Stack Hub. 

> [!NOTE] 
> Это VPN-подключение не предоставляет возможность подключения к виртуальным машинам инфраструктуры Azure Stack Hub. 

### <a name="prerequisites"></a>Предварительные требования

* Установите [Azure PowerShell, совместимый с Azure Stack Hub](../operator/azure-stack-powershell-install.md), на своем локальном компьютере.  
* Скачайте [средства, необходимые для работы с Azure Stack Hub](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Настройка подключения VPN

Чтобы создать VPN-подключение к комплекту разработки, откройте сеанс PowerShell с повышенными привилегиями на локальном компьютере с Windows и запустите следующий скрипт (укажите значения IP-адреса и пароля для своей среды):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Если программа установки завершается успешно, `azurestack` появится в списке VPN-подключений.

![Сетевые подключения](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>Подключение к Azure Stack Hub

Подключитесь к экземпляру Azure Stack Hub, используя один из двух способов.  

* С помощью команды `Connect-AzsVpn`: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  При появлении запроса обозначьте узел Azure Stack Hub как доверенный и установите сертификат из **AzureStackCertificateAuthority** в хранилище сертификатов на локальном компьютере. (запрос может появиться вне окна сеанса PowerShell). 

* На локальном компьютере выберите **Параметры сети** > **VPN** > `azurestack` > **Подключиться**. При запросе на вход введите имя пользователя (AzureStack\AzureStackAdmin) и пароль.

### <a name="test-the-vpn-connectivity"></a>Проверка VPN-подключения

Чтобы проверить подключение портала, откройте браузер, перейдите на портал пользователя https://portal.local.azurestack.external/), войдите в систему и создайте ресурсы.  

## <a name="next-steps"></a>Дальнейшие действия



