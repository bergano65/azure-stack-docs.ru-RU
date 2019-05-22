---
title: Интеграция внешнего решения для мониторинга с Azure Stack | Документация Майкрософт
description: Узнайте, как интегрировать Azure Stack с внешним решением для мониторинга в центре обработки данных.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 2871b5183833830368307c5d2b5152e3909fd3ea
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618828"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Интеграция внешнего решения для мониторинга с Azure Stack

Для внешнего мониторинга инфраструктуры Azure Stack требуется отслеживать программное обеспечение Azure Stack, физические компьютеры и физические сетевые коммутаторы. Каждая из этих областей предлагает свой метод извлечения сведений о работоспособности и оповещениях.

- Программное обеспечение Azure Stack предлагает API на основе REST для получения данных работоспособности и оповещений. При использовании таких программно-определяемых технологий, как служба "Локальные дисковые пространства", отслеживание работоспособности хранилища и оповещения являются частью мониторинга программного обеспечения.
- Физические компьютеры могут предоставлять данные работоспособности и оповещений через контроллеры управления основной платой (BMC).
- Физические сетевые устройства могут предоставлять данные работоспособности и оповещений по протоколу SNMP.

Каждое решение Azure Stack поставляется с узлом отслеживания жизненного цикла оборудования. На нем выполняется программное обеспечение для мониторинга физических серверов и сетевых устройств, предоставленное поставщиком изготовителя оборудования. Обратитесь к своему поставщику OEM, чтобы узнать, можно ли интегрировать предлагаемые им решения для мониторинга с решениями для мониторинга в вашем центре обработки данных.

> [!IMPORTANT]
> Внешние решения для мониторинга, которые вы используете, не должны использовать агенты. Нельзя устанавливать сторонние агенты внутри компонентов Azure Stack.

На следующей диаграмме показан поток трафика между интегрированной системой Azure Stack, узлом отслеживания жизненного цикла оборудования, внешним решением для мониторинга и внешней системой отправки запросов и сбора данных.

![Схема прохождения трафика между Azure Stack, решением для мониторинга и решением для отправки запросов](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Интеграция внешних средств мониторинга непосредственно с физическими серверами запрещена и активно блокируется списками управления доступом (ACL).  Интеграция внешних средств мониторинга непосредственно с физическими сетевыми устройствами поддерживается. Обратитесь к своему поставщику OEM за сведениями о том, как включить эту функцию.

В этой статье объясняется, как интегрировать Azure Stack с внешними решениями для мониторинга, например System Center Operations Manager и Nagios. В ней также поясняется, как программно работать с оповещениями с помощью PowerShell или вызовов REST API.

## <a name="integrate-with-operations-manager"></a>Интеграция с Operations Manager

Operations Manager можно использовать для внешнего мониторинга Azure Stack. Пакет управления System Center для Microsoft Azure Stack позволяет отслеживать несколько развернутых служб Azure Stack с помощью одного экземпляра Operations Manager. Пакет управления использует интерфейсы REST API поставщика ресурсов работоспособности и поставщика ресурсов обновления для взаимодействия с Azure Stack. Если вы планируете обойти программное обеспечение для мониторинга, предоставленное изготовителем оборудования, которое выполняется на узле отслеживания жизненного цикла оборудования, то вы можете установить пакеты управления поставщика оборудования для отслеживания физических серверов. Можно также использовать обнаружение сетевых устройств Operations Manager для наблюдения за сетевыми коммутаторами.

Пакет управления для Azure Stack предоставляет следующие возможности.

- Можно управлять несколькими развернутыми службами Azure Stack.
- Поддерживаются Azure Active Directory (Azure AD) и службы федерации Active Directory (AD FS).
- Можно извлекать и закрывать оповещения.
- Доступна панель мониторинга работоспособности и производительности.
- Доступно автоматическое обнаружение режима обслуживания, в котором выполняется установка исправлений и обновлений.
- Доступны задачи принудительного обновления для развернутой службы и региона.
- Можно добавить пользовательские сведения для региона.
- Поддержка уведомлений и отчетов.

Скачать пакет управления System Center для Microsoft Azure Stack и соответствующее руководство пользователя можно [отсюда](https://www.microsoft.com/en-us/download/details.aspx?id=55184) или непосредственно из Operations Manager.

Чтобы реализовать решение для отправки запросов, можно интегрировать Operations Manager с System Center Service Manager. Интегрированный соединитель продукта обеспечивает двунаправленный обмен данными, который позволяет закрыть оповещение в Azure Stack и Operations Manager после обработки запроса на обслуживание в Service Manager.

На следующей схеме показана интеграция Azure Stack с существующим развертыванием System Center. Можно еще больше автоматизировать работу Service Manager с помощью System Center Orchestrator или Service Management Automation (SMA) для выполнения операций в Azure Stack.

![Схема интеграции с Operations Manager, Service Manager и SMA](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Интеграция с Nagios

Подключаемый модуль мониторинга Nagios был разработан вместе с партнерскими решениями Cloudbase, которые предоставляются по разрешенной лицензии на бесплатное программное обеспечение Массачусетского технологического института.

Этот подключаемый модуль создан на языке Python, и в нем используется REST API поставщика ресурсов работоспособности. Он обеспечивает базовые функции получения и закрытия оповещений в Azure Stack. Как и пакет управления System Center, он позволяет добавить несколько развернутых служб Azure Stack и отправлять уведомления.

Подключаемый модуль работает с Nagios Enterprise и Nagios Core. Его можно скачать [здесь](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Сайт скачивания также содержит сведения об установке и настройке.

### <a name="plugin-parameters"></a>Параметры подключаемого модуля

Настройте файл подключаемого модуля Azurestack_plugin.py, указав следующие параметры:

| Параметр | ОПИСАНИЕ | Пример |
|---------|---------|---------|
| *arm_endpoint* | Конечная точка Azure Resource Manager (администратор). | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Конечная точка Azure Resource Manager (администратор).  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Идентификатор подписки администратора. | Его можно получить с помощью портала администратора или PowerShell. |
| *User_name* | Имя пользователя в подписке оператора. | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Пароль подписки оператора. | mypassword |
| *Client_id* | Клиент | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Имя региона Azure Stack. | local |
|  |  |

* Указан универсальный глобальный уникальный идентификатор PowerShell. Его можно использовать для каждого развертывания.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Мониторинг работоспособности и оповещений с помощью PowerShell

Если вы не используете Operations Manager, Nagios или решение на основе Nagios, то можете использовать PowerShell, чтобы интегрировать широкий спектр решений для мониторинга с Azure Stack.

1. Чтобы использовать PowerShell, убедитесь, в среде оператора Azure Stack [установлен и настроен компонент PowerShell](azure-stack-powershell-install.md). Установите PowerShell на локальном компьютере с доступом к конечной точке Resource Manager (администратор) (https://adminmanagement.[регион].[внешнее_полное_доменное_имя]).

2. Выполните следующие команды для подключения к среде Azure Stack в качестве оператора Azure Stack.

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Ниже приведены примеры команд для работы с оповещениями.
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Подробнее

Сведения о встроенных средствах мониторинга работоспособности см. в разделе [Мониторинг работоспособности и оповещений в Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Дополнительная информация

[Интеграция решений для обеспечения безопасности](azure-stack-integrate-security.md)
