---
title: Запуск и остановка ASDK
description: Узнайте, как запустить и остановить Пакет средств разработки Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 68523f429fb49bfc28151f3d92f3bb3cd64a0b71
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873470"
---
# <a name="start-and-stop-the-asdk"></a>Запуск и остановка ASDK
Не рекомендуется просто перезапускать главный компьютер ASDK. Следуйте инструкциям по корректному завершению работы и перезапуску служб ASDK, приведенным в этой статье.

## <a name="stop-azure-stack"></a>Остановка Azure Stack 
Чтобы правильно завершить работу служб Azure Stack и главного компьютера ASDK, используйте следующие команды PowerShell:

1. Войдите на главный компьютер ASDK с именем AzureStack\AzureStackAdmin.
2. Откройте PowerShell с правами администратора (не путайте с интегрированной средой сценариев Windows PowerShell).
3. Для установки сеанса привилегированной конечной точки (PEP) выполните следующие команды: 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Затем в сеансе PEP используйте командлет **Stop-AzureStack**, чтобы остановить работу служб Azure Stack и завершить работу главного компьютера ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Просмотрите выходные данные PowerShell, чтобы убедиться, что все службы Azure Stack успешно завершили свою работу до завершения работы главного компьютера ASDK. Этот процесс занимает несколько минут.

## <a name="start-azure-stack"></a>Запуск Azure Stack 
Службы ASDK должны запускаться автоматически при запуске главного компьютера. Однако время запуска служб инфраструктуры ASDK зависит от производительности конфигурации аппаратного обеспечения главного компьютера ASDK. В некоторых случаях для успешного перезапуска всех служб может потребоваться несколько часов.

Независимо от того, каким образом была завершена работа ASDK, после включения главного компьютера необходимо выполнить шаги ниже, чтобы убедиться, что все службы Azure Stack запущены и полностью работоспособны: 

1. Включите главный компьютер ASDK. 
2. Войдите на главный компьютер ASDK с именем AzureStack\AzureStackAdmin.
3. Откройте PowerShell с правами администратора (не путайте с интегрированной средой сценариев Windows PowerShell).
4. Для установки сеанса привилегированной конечной точки (PEP) выполните следующие команды:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Затем в сеансе PEP выполните следующие команды, чтобы проверить состояние запуска служб Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Просмотрите выходные данные, чтобы убедиться, что службы Azure Stack успешно перезапущены.

Чтобы узнать больше о рекомендуемых процедурах для правильного завершения работы и перезапуска служб Azure Stack, ознакомьтесь со статьей [Запуск и остановка Azure Stack](../operator/azure-stack-start-and-stop.md).

## <a name="troubleshoot-startup-and-shutdown"></a>Устранение неполадок при запуске и завершении работы 
Выполните эти шаги, если службы Azure Stack не запустились в течение двух часов после включения главного компьютера ASDK:

1. Войдите на главный компьютер ASDK с именем AzureStack\AzureStackAdmin.
2. Откройте PowerShell с правами администратора (не путайте с интегрированной средой сценариев Windows PowerShell).
3. Для установки сеанса привилегированной конечной точки (PEP) выполните следующие команды:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Затем в сеансе PEP выполните следующие команды, чтобы проверить состояние запуска служб Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Просмотрите выходные данные и устраните ошибки. Дополнительные сведения см. в статье [Запуск проверочного теста в Azure Stack](../operator/azure-stack-diagnostic-test.md).
6. Перезапустите службы Azure Stack из этого сеанса PEP, запустив командлет **Start-AzureStack**:

   ```powershell
   Start-AzureStack
   ```

Если запуск командлета **Start-AzureStack** завершается сбоем, посетите [форум технической поддержки Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack), чтобы получить помощь по устранению неполадок с ASDK. 

## <a name="next-steps"></a>Дальнейшие действия 
Дополнительные сведения о средстве диагностики Azure Stack и регистрации проблем см. в статье [Средства диагностики Azure Stack](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
