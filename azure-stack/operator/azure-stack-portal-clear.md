---
title: Удаление данных пользователя портала из Azure Stack по запросу | Документация Майкрософт
description: Узнайте, как оператор Azure Stack может удалять данные пользователей портала по запросу пользователей Azure Stack.
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
ms.openlocfilehash: 2dd88656491a474e4082ff4e8321af836776b1f0
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019118"
---
# <a name="clear-portal-user-data-from-azure-stack"></a>Удаление данных пользователя портала из Azure Stack

Операторы Azure Stack могут удалять данные пользователей портала по запросу. Пользователи Azure Stack могут настраивать портал, закрепляя плитки и изменяя макет панели мониторинга. Пользователи также могут изменять тему и выбирать язык по умолчанию. 

Данные пользователя портала — это элементы из категории "Избранное" и недавно использовавшиеся ресурсы на пользовательском портале Azure Stack. В статье описано, как удалить данные пользователя портала.

Удалять пользовательские настройки портала нужно только после удаления пользовательской подписки.

> [!NOTE]
> После выполнения инструкций из этого руководства по удалению данных некоторые пользовательские данные могут все еще оставаться в журналах событий. Эти данные могут храниться в течение нескольких дней, пока журналы не будут автоматически обновлены.

## <a name="requirements"></a>Требования

- [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
- [Скачайте средства Azure Stack последней версии](azure-stack-powershell-download.md) на сайте GitHub.
- Включите учетную запись пользователя в каталог.
- Настройте учетные данные администратора Azure Stack для доступа к конечной точке администратора Resource Manager.

> [!NOTE]
> Если вам нужно удалить данные пользователя, который был приглашен из гостевого каталога (мультитенантного), вам понадобится разрешение на чтение для этого каталога. Дополнительные сведения см. далее в разделе о [сценарии для поставщика облачных служб](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Удаление данных пользователя портала с помощью имени участника-пользователя

В этом сценарии предполагается, что подписка поставщика по умолчанию и пользователь принадлежат к одному каталогу или что у вас есть доступ на чтение к каталогу, с которым связан пользователь.

Обязательно [скачайте средства Azure Stack последней версии](azure-stack-powershell-download.md) на сайте GitHub, прежде чем продолжать.

Для выполнения этой процедуры используйте компьютер, который может взаимодействовать с конечной точкой администратора Resource Manager в Azure Stack.

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (запуск от имени администратора), перейдите в корневую папку в каталоге **AzureStack-Tools-master** и импортируйте необходимый модуль PowerShell:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Выполните команды ниже. Обязательно замените заполнители значениями, соответствующими вашей среде.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack directory tenant ID.
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
   > `azureStackDirectoryTenantId` является необязательным. Если это значение не указано, скрипт выполняет поиск имени участника-пользователя во всех каталогах клиентов, зарегистрированных в Azure Stack, а затем удаляет данные всех соответствующих пользователей портала.

## <a name="clear-portal-user-data-in-guest-directory"></a>Удаление данных пользователя портала в гостевом каталоге

В этом сценарии оператор Azure Stack не имеет доступа к гостевому каталогу, с которым связан пользователь. Это распространенный сценарий для поставщиков облачных решений (CSP).

Чтобы оператор Azure Stack мог удалить данные пользователя портала, ему необходим по крайней мере идентификатор объекта пользователя.

Пользователь должен запросить идентификатор объекта и предоставить его оператору Azure Stack. Оператор не имеет доступа к каталогу, в котором находится пользователь.

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
   > Пользователь должен передать оператору Azure Stack идентификатор объекта пользователя, возвращаемый указанным скриптом.

## <a name="azure-stack-operator-removes-the-portal-user-data"></a>Удаление данных пользователя портала оператором Azure Stack

После получения идентификатора объекта пользователя оператор Azure Stack выполняет следующие команды, чтобы удалить данные пользователя портала:

1. Откройте сеанс Windows PowerShell с повышенными привилегиями (запуск от имени администратора), перейдите в корневую папку в каталоге **AzureStack-Tools-master** и импортируйте необходимый модуль PowerShell.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Выполните следующие команды, изменив параметр в соответствии со своей средой:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack directory tenant ID.
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

## <a name="next-steps"></a>Дополнительная информация

- [Регистрация Azure Stack в Azure](azure-stack-registration.md) и заполнение [Azure Stack Marketplace](azure-stack-marketplace.md) элементами, которые можно предложить пользователям.
