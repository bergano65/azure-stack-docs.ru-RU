---
title: Отправка в Azure журналов диагностики Azure Stack Hub с помощью привилегированной конечной точки
description: Сведения о том, как можно отправить в Azure журналы диагностики Azure Stack Hub с помощью привилегированной конечной точки.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520563"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Отправка в Azure журналов диагностики Azure Stack Hub с помощью привилегированной конечной точки

При использовании этого способа вам не придется указывать целевое место хранения. Если параметры **fromDate** и **toDate** не указаны, по умолчанию используется период в 4 часа с момента выполнения команды. 

Вы можете указать необязательные параметры **FilterByRole** или **FilterByResourceProvider**, чтобы включить журналы только по конкретной роли или по поставщику дополнительных ресурсов. 

Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов в интегрированной системе. 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

## <a name="parameter-considerations"></a>Рекомендации по настройке параметров 

* Параметры **FromDate** и **ToDate** можно использовать для сбора журналов за конкретный период времени. Если эти параметры не указаны, по умолчанию журналы собираются за последние четыре часа.

* Чтобы фильтровать журналы по имени компьютера, используйте параметр **FilterByNode**. Пример:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Чтобы фильтровать журналы по типу, используйте параметр **FilterByLogType**. Доступна фильтрация по файлу, общему ресурсу или по событию Windows. Пример:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Параметр **FilterByResourceProvider** позволяет отправить журналы диагностики по конкретному поставщику дополнительных ресурсов. Ниже приведен общий синтаксис.
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Отправка журналов диагностики для Центра Интернета вещей: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Отправка журналов диагностики для Центров событий:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Отправка журналов диагностики для Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* Используйте параметр **FilterByRole** для отправки журналов диагностики из ролей VirtualMachines и BareMetal:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* Отправка журналов из ролей VirtualMachines и BareMetal с фильтром по дате файлов журнала за последние 8 часов:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Отправка журналов диагностики из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за период от восьми до двух часов назад:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```


## <a name="next-steps"></a>Дальнейшие действия

[Отправка журналов диагностики Azure Stack Hub с помощью привилегированной конечной точки](azure-stack-get-azurestacklog.md)
