---
title: Повторное развертывание Пакета средств разработки Azure Stack (ASDK) | Документация Майкрософт
description: Из этой статьи вы узнаете, как выполнить повторную установку ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 2ebb7c702eeb2c7f04f61f6bbc79aa0edaf8a860
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307472"
---
# <a name="redeploy-the-asdk"></a>Повторное развертывание ASDK
Из этой статьи вы узнаете, как выполнить повторное развертывание Пакета средств разработки Azure Stack (ASDK) в нерабочей среде. Так как обновление ASDK не поддерживается, чтобы перейти на новую версию необходимо его повторное развертывание. Кроме того, ASDK можно повторно развернуть в любой момент, когда понадобится начать работу с начала.

> [!IMPORTANT]
> Обновление ASDK до новой версии не поддерживается. Каждый раз, когда необходимо обновиться до последней версии Azure Stack, нужно повторно развертывать ASDK на главном компьютере пакета средств разработки.

## <a name="remove-azure-registration"></a>Удаление регистрации в Azure 
Если установка ASDK уже была зарегистрирована в Azure, необходимо удалить ресурс регистрации перед повторным развертыванием ASDK. Повторно зарегистрируйте ASDK, чтобы элементы стали доступными в Marketplace при повторном развертывании ASDK. Если вы не регистрировали ASDK с подпиской Azure раньше, этот раздел можно пропустить.

Чтобы удалить ресурс регистрации, используйте командлет **Remove-AzsRegistration** для отмены регистрации Azure Stack. Затем с помощью командлета **Remove-AzureRMResourceGroup** удалите группу ресурсов Azure Stack из подписки Azure:

1. На компьютере с доступом к привилегированной конечной точке откройте консоль PowerShell с правами администратора. Для ASDK этот компьютер является главным компьютером пакета средств разработки.

2. Для отмены регистрации установки ASDK и удаления группы ресурсов **azurestack** из подписки Azure выполните следующие команды PowerShell:

   ```Powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. При запуске скрипта вам будет предложено войти в подписку Azure и локальную установку ASDK.
4. После выполнения скрипта появятся сообщения, аналогичные приведенным ниже:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Теперь регистрация Azure Stack для подписки Azure должна быть отменена. Кроме того, группа ресурсов azurestack, созданная при регистрации ASDK в Azure, также будет удалена.

## <a name="deploy-the-asdk"></a>Развертывание пакета SDK для Azure Stack
Чтобы повторно развернуть Azure Stack, необходимо выполнить описанную ниже процедуру с самого начала. В зависимости от того, был ли использован скрипт установщика Azure Stack (asdk-installer.ps1) для установки ASDK, шаги будут отличаться.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Повторное развертывание ASDK с помощью скрипта установщика
1. Откройте консоль PowerShell с повышенными привилегиями на компьютере ASDK и перейдите к скрипту asdk-installer.ps1 в каталоге **AzureStack_Installer**, расположенном на несистемном диске. Запустите срипт и щелкните **Перезагрузка**.

   ![Запуск скрипта asdk-installer.ps1](media/asdk-redeploy/1.png)

2. Выберите основную операционную систему (не **Azure Stack**) и нажмите кнопку **Далее**.

   ![Перезагрузка в операционной системе узла](media/asdk-redeploy/2.png)

3. После перезагрузки узла пакета разработки в основную операционную систему войдите как локальный администратор. Найдите и удалите файл **C:\CloudBuilder.vhdx**, который использовался при предыдущем развертывании. 

4. Сделайте то же, что и при первом [развертывании ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Повторное развертывание ASDK без использования установщика
Если для установки ASDK не использовался скрипт asdk-installer.ps1, то перед тем, как повторно развернуть ASDK, необходимо вручную перенастроить главный компьютер пакета средств разработки.

1. Запустите служебную программу конфигурации системы, выполнив файл **msconfig.exe** на компьютере ASDK. На вкладке **Загрузка** выберите операционную систему главного компьютера (не Azure Stack), щелкнув **Set as default** (Использовать по умолчанию), а затем нажмите кнопку **ОК**. При появлении запроса щелкните **Перезагрузить**.

      ![Настройка конфигурации загрузки](media/asdk-redeploy/4.png)

2. После перезагрузки узла пакета разработки в основную операционную систему войдите как локальный администратор главного компьютера пакета разработки. Найдите и удалите файл **C:\CloudBuilder.vhdx**, который использовался при предыдущем развертывании. 

3. Сделайте то же, что и при первом [развертывании ASDK с помощью PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Дополнительная информация
[Задачи, выполняемые после развертывания ASDK](asdk-post-deploy.md)




