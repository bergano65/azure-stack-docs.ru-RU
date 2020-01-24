---
title: Удаление данных пользователя портала из Azure Stack Hub по запросу. | Документы Майкрософт
description: Узнайте, как оператор Azure Stack Hub может удалять данные пользователей портала по запросу пользователей Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/10/2019
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
monikerRange: azs-1802
ms.openlocfilehash: ac28a67f7b1409ebc5a786a88e8b9702df94c2ff
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022772"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>Удаление данных пользователя портала из Azure Stack Hub

Операторы Azure Stack Hub могут удалять данные пользователей портала по запросу. Пользователи Azure Stack Hub могут настраивать портал, закрепляя плитки и изменяя макет панели мониторинга. Пользователи также могут изменять тему и выбирать язык по умолчанию. 

Данные пользователя портала — это элементы из категории "Избранное" и недавно использовавшиеся ресурсы на пользовательском портале Azure Stack Hub. В статье описано, как удалить данные пользователя портала.

Удалять пользовательские настройки портала нужно только после удаления пользовательской подписки.

> [!NOTE]
> После выполнения инструкций из этого руководства по удалению данных некоторые пользовательские данные могут все еще оставаться в журналах событий. Эти данные могут храниться в течение нескольких дней, пока журналы не будут автоматически обновлены.

## <a name="requirements"></a>Требования

- [Установка PowerShell для Azure Stack Hub](azure-stack-powershell-install.md).
- [Скачайте средства Azure Stack Hub последней версии](azure-stack-powershell-download.md) на сайте GitHub.
- Включите учетную запись пользователя в каталог.
- Настройте учетные данные администратора Azure Stack Hub для доступа к конечной точке администратора Resource Manager.

> [!NOTE]
> Если вам нужно удалить данные пользователя, который был приглашен из гостевого каталога (мультитенантного), вам понадобится разрешение на чтение для этого каталога. Дополнительные сведения см. далее в разделе о [сценарии для поставщика облачных служб](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Удаление данных пользователя портала с помощью имени участника-пользователя

В этом сценарии предполагается, что подписка поставщика по умолчанию и пользователь принадлежат к одному каталогу или что у вас есть доступ на чтение к каталогу, с которым связан пользователь.

Обязательно [скачайте средства Azure Stack Hub последней версии](azure-stack-powershell-download.md) на сайте GitHub, прежде чем продолжать.

Для выполнения этой процедуры используйте компьютер, который может взаимодействовать с конечной точкой администратора Resource Manager в Azure Stack Hub.

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (запуск от имени администратора), перейдите в корневую папку в каталоге **AzureStack-Tools-master** и импортируйте необходимый модуль PowerShell:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Выполните команды ниже. Обязательно замените заполнители значениями, соответствующими вашей среде.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > Аргумент `azureStackDirectoryTenantId` является необязательным. Если это значение не указано, скрипт выполняет поиск имени участника-пользователя во всех каталогах клиентов, зарегистрированных в Azure Stack Hub, а затем удаляет данные всех соответствующих пользователей портала.

## <a name="clear-portal-user-data-in-guest-directory"></a>Удаление данных пользователя портала в гостевом каталоге

В этом сценарии оператор Azure Stack Hub не имеет доступа к гостевому каталогу, с которым связан пользователь. Это распространенный сценарий для поставщиков облачных решений (CSP).

Чтобы оператор Azure Stack Hub мог удалить данные пользователя портала, ему необходим по крайней мере идентификатор объекта пользователя.

Пользователь должен запросить идентификатор объекта и предоставить его оператору Azure Stack Hub. Оператор не имеет доступа к каталогу, в котором находится пользователь.

### <a name="user-retrieves-the-user-object-id"></a>Получение пользователем идентификатора объекта пользователя

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (запуск от имени администратора), перейдите в корневую папку в каталоге **AzureStack-Tools-master** и импортируйте необходимый модуль PowerShell.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Выполните команды ниже. Обязательно замените заполнители значениями, соответствующими вашей среде.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > Пользователь должен передать оператору Azure Stack Hub идентификатор объекта пользователя, возвращаемый указанным скриптом.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Удаление данных пользователя портала оператором Azure Stack Hub

После получения идентификатора объекта пользователя оператор Azure Stack Hub выполняет следующие команды, чтобы удалить данные пользователя портала:

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (запуск от имени администратора), перейдите в корневую папку в каталоге **AzureStack-Tools-master** и импортируйте необходимый модуль PowerShell.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Выполните следующие команды, изменив параметр в соответствии со своей средой:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>Дальнейшие действия

- [Зарегистрируйте Azure Stack Hub в Azure](azure-stack-registration.md) и заполните [Azure Stack Hub Marketplace](azure-stack-marketplace.md) элементами, которые можно предложить пользователям.
