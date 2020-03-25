---
title: Поиск идентификатора облака
description: Сведения о том, как найти свой идентификатор облака в разделе "Справка и поддержка" в Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520623"
---
# <a name="find-your-cloud-id"></a>Поиск идентификатора облака

В этой статье описывается, как получить идентификатор облака с помощью портала администрирования или привилегированной конечной точки (PEP). 

## <a name="use-the-administrator-portal"></a>Использование портала администрирования

1. Откройте портал администрирования. 
1. Выберите элемент **Управление регионами**.

   ![Снимок экрана с панелью мониторинга](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Щелкните **Свойства** и скопируйте значение в поле **Stamp Cloud ID** (Идентификатор облака в единице масштабирования).

   ![Снимок экрана, на котором показаны свойства региона и идентификатор облака в единице масштабирования](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Использование привилегированной конечной точки

1. Откройте сеанс PowerShell с повышенными правами и выполните приведенный ниже скрипт. Замените IP-адрес виртуальной машины привилегированной конечной точки и учетные данные администратора облака, необходимые для вашей среды. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>Дальнейшие действия

* [Упреждающая отправка журналов](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [Немедленная отправка журналов](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






