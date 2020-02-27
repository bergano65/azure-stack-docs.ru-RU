---
title: Отчет о проверке Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Создайте отчет о проверке с помощью инструмента проверки готовности Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: c64c9b4b5a6f73f5dd8f5e323ff16a44589173cc
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508162"
---
# <a name="azure-stack-hub-validation-report"></a>Отчет о проверке Azure Stack Hub

Используйте [инструмент проверки готовности Azure Stack Hub](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69), чтобы определить, выполнены ли условия, необходимые для поддержки развертывания и обслуживания окружения Azure Stack Hub. Результаты сохраняются в JSON-файле отчета. В отчете отображаются подробные и сводные данные о состоянии компонентов, необходимых для развертывания Azure Stack Hub. Этот отчет также содержит сведения о смене секретов для существующих развертываний Azure Stack Hub.  

## <a name="where-to-find-the-report"></a>Где найти отчет

При работе средства результаты сохраняются в файле **AzsReadinessCheckerReport.json**. Также создается файл журнала с именем **AzsReadinessChecker.log**. Расположение этих файлов указывается в PowerShell вместе с результатами проверки.

![Результаты run-validation для инструмента проверки готовности Azure Stack Hub](./media/azure-stack-validation-report/validation.png)

В обоих файлах сохраняются результаты всех последовательных проверок на этом компьютере. Например, вы можете запустить средство только для проверки сертификатов, затем снова запустить его для проверки удостоверений Azure, а в третий раз — для проверки регистрации. Результаты всех трех проверок будут доступны в JSON-файле отчета.  

По умолчанию оба файла записываются в `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Используйте параметр `-OutputPath <path>` в конце командной строки, чтобы задать другое расположение для отчетов.
- Используйте параметр `-CleanReport` в конце команды, чтобы удалить из файла **AzsReadinessCheckerReport.json** сведения о предыдущих запусках средства.

## <a name="view-the-report"></a>Просмотр отчета

Чтобы просмотреть отчет в PowerShell, укажите путь к отчету в параметре `-ReportPath`. Эта команда выводит содержимое отчета и указывает проверки, для которых еще нет результатов.

Например, следующая команда позволяет просмотреть из командной строки PowerShell отчет, расположенный в текущем каталоге.

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Просмотр сводного отчета

Чтобы просмотреть сводную информацию из отчета, добавьте параметр `-summary` в конец командной строки PowerShell. Пример:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Сводные данные содержат список проверок, по которым пока нет данных, а также сведения об успешных или неудачных результатах проверок. Вы должны увидеть результат, аналогичный приведенному ниже.

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Просмотр отчета с фильтром

Чтобы просмотреть отчет, отфильтрованный проверкам определенного типа, укажите параметр `-ReportSections` с одним из следующих значений.

- Сертификат
- AzureRegistration;
- AzureIdentity;
- График
- ADFS
- Задания
- All  

Например, для извлечения из отчета сводной информации только о сертификатах выполните следующую команду PowerShell:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
