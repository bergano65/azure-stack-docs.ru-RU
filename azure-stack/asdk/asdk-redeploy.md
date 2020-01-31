---
title: Повторное развертывание ASDK
description: Узнайте, как повторно развернуть Пакет средств разработки Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: d0fc4539b581474c9db2a2dbb05495c9b1bce695
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873521"
---
# <a name="redeploy-the-asdk"></a>Повторное развертывание ASDK
Из этой статьи вы узнаете, как выполнить повторное развертывание Пакета средств разработки Azure Stack (ASDK) в среде, не являющейся рабочей. Так как обновление ASDK не поддерживается, чтобы перейти на новую версию необходимо его повторное развертывание. Вы можете также повторно развернуть ASDK, когда нужно начать развертывание с нуля.

> [!IMPORTANT]
> Обновление ASDK до новой версии не поддерживается. Каждый раз, когда необходимо выполнить обновление до последней версии Azure Stack, нужно повторно развертывать ASDK на главном компьютере ASDK.

## <a name="remove-azure-registration"></a>Удаление регистрации в Azure 
Если установка ASDK уже была зарегистрирована в Azure, необходимо удалить ресурс регистрации перед повторным развертыванием ASDK. Повторно зарегистрируйте ASDK, чтобы элементы стали доступными в Marketplace при повторном развертывании ASDK. Если вы не регистрировали ASDK с помощью подписки Azure раньше, этот раздел можно пропустить.

Чтобы удалить ресурс регистрации, используйте командлет **Remove-AzsRegistration** для отмены регистрации Azure Stack. Затем с помощью командлета **Remove-AzureRMResourceGroup** удалите группу ресурсов Azure Stack из подписки Azure:

1. На компьютере с доступом к привилегированной конечной точке откройте консоль PowerShell с правами администратора. Для ASDK этот компьютер является главным компьютером ASDK.

2. Для отмены регистрации установки ASDK и удаления группы ресурсов **azurestack** из подписки Azure выполните следующие команды PowerShell:

   ```powershell    
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

3. При запуске сценария вам будет предложено войти в подписку Azure и локальный экземпляр ASDK.
4. После выполнения скрипта появятся сообщения, аналогичные приведенным ниже:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Теперь регистрация Azure Stack для подписки Azure должна быть отменена. Группа ресурсов azurestack также должна быть удалена. Эта группа ресурсов создается при первой регистрации ASDK в Azure.

## <a name="deploy-the-asdk"></a>Развертывание пакета SDK для Azure Stack
Чтобы повторно развернуть Azure Stack, необходимо выполнить описанную ниже процедуру с самого начала. В зависимости от того, был ли использован скрипт установщика Azure Stack (asdk-installer.ps1) для установки ASDK, шаги будут отличаться.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Повторное развертывание ASDK с помощью скрипта установщика
1. Откройте консоль PowerShell с повышенными привилегиями на компьютере ASDK и перейдите к скрипту asdk-installer.ps1 в каталоге **AzureStack_Installer**, расположенном на несистемном диске. Запустите срипт и щелкните **Перезагрузка**.

   ![Запуск скрипта asdk-installer.ps1](media/asdk-redeploy/1.png)

2. Выберите основную операционную систему (не **Azure Stack**) и нажмите кнопку **Далее**.

   ![Перезагрузка в операционной системе узла](media/asdk-redeploy/2.png)

3. После перезагрузки узла ASDK в основную операционную систему войдите как локальный администратор. Найдите и удалите файл **C:\CloudBuilder.vhdx**, который использовался при предыдущем развертывании.

4. Сделайте то же, что и при первом [развертывании ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Повторное развертывание ASDK без использования установщика
Если для установки ASDK не использовался сценарий asdk-installer.ps1, то перед тем, как повторно развернуть ASDK, необходимо вручную перенастроить главный компьютер ASDK.

1. Запустите служебную программу конфигурации системы, выполнив файл **msconfig.exe** на компьютере ASDK. На вкладке **Загрузка** выберите операционную систему главного компьютера (не Azure Stack), щелкнув **Set as default** (Использовать по умолчанию), а затем нажмите кнопку **ОК**. При появлении запроса щелкните **Перезагрузить**.

      ![Настройка конфигурации загрузки](media/asdk-redeploy/4.png)

2. После перезагрузки узла ASDK в основную операционную систему войдите как локальный администратор на главный компьютер ASDK. Найдите и удалите файл **C:\CloudBuilder.vhdx**, который использовался при предыдущем развертывании.

3. Сделайте то же, что и при первом [развертывании ASDK с помощью PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Дальнейшие действия
[Задачи, выполняемые после развертывания ASDK](asdk-post-deploy.md)




