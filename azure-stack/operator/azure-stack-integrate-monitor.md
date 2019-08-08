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
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 7b5bfb39c3ec14c23b1df54c13f2733724fcfe05
ms.sourcegitcommit: ddb625bb01de11bfb75d9f7a1cc61d5814b3bc31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68712916"
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

Вы можете установить и настроить подключаемый модуль Nagios для Microsoft Azure Stack.

Подключаемый модуль мониторинга Nagios был разработан вместе с партнерскими решениями Cloudbase, которые предоставляются по разрешенной лицензии на бесплатное программное обеспечение Массачусетского технологического института.

Этот подключаемый модуль создан на языке Python, и в нем используется REST API поставщика ресурсов работоспособности. Он обеспечивает базовые функции получения и закрытия оповещений в Azure Stack. Как и пакет управления System Center, он позволяет добавить несколько развернутых служб Azure Stack и отправлять уведомления.

В Azure Stack версии 1.2 подключаемый модуль Nagios использует библиотеку Microsoft ADAL и поддерживает аутентификацию с помощью субъекта-службы с секретом или сертификатом. Также была упрощен процесс настройки, который теперь выполняется с помощью одного файла конфигурации с новыми параметрами. Теперь реализована поддержка развертываний в Azure Stack с помощью Azure Active Directory и AD FS, используемых в качестве систем удостоверений.

Подключаемый модуль работает с Nagios версии 4x и XI. Его можно скачать [здесь](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Сайт скачивания также содержит сведения об установке и настройке.

### <a name="requirements-for-nagios"></a>Требования для Nagios

1.  Версия Nagios не ниже 4.x.

2.  Библиотека Microsoft Azure Active Directory для Python. Ее можно установить с помощью Python PIP.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Установка подключаемого модуля

В этом разделе описывается, как установить подключаемый модуль Azure Stack при условии, что приложение Nagios уже установлено.

Пакет подключаемого модуля содержит следующие файлы:

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1.  Скопируйте подключаемый модуль `azurestack_plugin.py` в каталог `/usr/local/nagios/libexec`.

2.  Скопируйте обработчик `azurestack_handler.sh` в каталог `/usr/local/nagios/libexec/eventhandlers`.

3.  Убедитесь, что файл подключаемого модуля указан как исполняемый.

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Настройка подключаемого модуля

В файле azurestack.cfg доступны следующие параметры для настройки. Указанные жирным шрифтом параметры должны быть настроены независимо от выбранной модели аутентификации.

Подробные сведения о создании имени субъекта-службы см. [здесь](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Параметр | ОПИСАНИЕ | Аутентификация |
| --- | --- | --- |
| **External_domain_fqdn ** | Полное доменное имя внешнего домена |    |
| **region: ** | Имя региона |    |
| **tenant_id: ** | Идентификатор арендатора\* |    |
| client_id: | Идентификатор клиента | Имя субъекта-службы с секретом |
| client_secret: | Пароль клиента | Имя субъекта-службы с секретом |
| client_cert\*\*: | Путь к сертификату | Имя субъекта-службы с сертификатом |
| client_cert_thumbprint\*\*: | Отпечаток сертификата | Имя субъекта-службы с сертификатом |

\*Идентификатор арендатора не требуется при развертывании в Azure Stack с помощью служб федерации Active Directory.

\*\* Секрет клиента и сертификат клиента являются взаимоисключающими.

Другие файлы конфигурации содержат дополнительные параметры конфигурации, которые можно настраивать также и в Nagios.

> [!Note]  
> Проверьте расположение, указанное в файлах azurestack_hosts.cfg и azurestack_services.cfg.

| Конфигурация | ОПИСАНИЕ |
| --- | --- |
| azurestack_commands.cfg | Конфигурация обработчика, не требующая изменений |
| azurestack_contacts.cfg | Параметры уведомлений |
| azurestack_hosts.cfg | Именование развертывания Azure Stack |
| azurestack_services.cfg | Конфигурация службы |

### <a name="setup-steps"></a>Процедура настройки

1.  Измените файл конфигурации.

2.  Скопируйте измененные файлы конфигурации в папку `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Обновление конфигурации Nagios

Конфигурацию Nagios необходимо обновить для загрузки подключаемого модуля Nagios.

1.  Откройте этот файл:

```bash  
/usr/local/nagios/etc/nagios.cfg
```

2.  Добавьте следующие записи:

```bash  
# Load the Azure Stack Plugin Configuration
cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

3.  Перезагрузите Nagios.

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Закрытие активных оповещений вручную

Активные оповещения можно закрыть в Nagios с помощью функции настраиваемого уведомления. Настраиваемое уведомление выполняется так:

```
/close-alert <ALERT_GUID>
```

Оповещение также можно закрыть с помощью следующей команды из терминала:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Устранение неполадок

Устранить неполадки с подключаемым модулем можно, вызвав подключаемый модуль вручную в окне терминала. Для этого используйте следующий метод:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Мониторинг работоспособности и оповещений с помощью PowerShell

Если вы не используете Operations Manager, Nagios или решение на основе Nagios, то можете использовать PowerShell, чтобы интегрировать широкий спектр решений для мониторинга с Azure Stack.

1. Чтобы использовать PowerShell, убедитесь, в среде оператора Azure Stack [установлен и настроен компонент PowerShell](azure-stack-powershell-install.md). Установите PowerShell на локальном компьютере с доступом к конечной точке Resource Manager (администратор) (https://adminmanagement.[регион].[внешнее_полное_доменное_имя]).

2. Выполните следующие команды для подключения к среде Azure Stack в качестве оператора Azure Stack.

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Ниже приведены примеры команд для работы с оповещениями.
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Подробнее

Сведения о встроенных средствах мониторинга работоспособности см. в разделе [Мониторинг работоспособности и оповещений в Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Дополнительная информация

[Интеграция решений для обеспечения безопасности](azure-stack-integrate-security.md)
