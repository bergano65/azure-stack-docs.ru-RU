---
title: Обновление 1902 для Azure Stack | Документация Майкрософт
description: Сведения о новых возможностях и известных проблемах в обновлении 1902 для интегрированных систем Azure Stack, а также о том, где можно скачать это обновление.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: a220bdef3a1243510bf7d9ace5991cd638c93f28
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65387141"
---
# <a name="azure-stack-1902-update"></a>Обновление 1902 для Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье описывается содержимое пакета обновления 1902. Обновление содержит улучшения, исправления и новые функции для этой версии Azure Stack. В этой статье также описываются известные проблемы этого выпуска и содержится ссылка для скачивания обновления. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу обновления, и проблемы со сборкой (после установки).

> [!IMPORTANT]  
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 1902 для Azure Stack — **1.1902.0.69**.

## <a name="hotfixes"></a>Исправления

Azure Stack выпускает исправления на регулярной основе. Обязательно установите [последнее исправление Azure Stack](#azure-stack-hotfixes) для обновления 1901 перед обновлением Azure Stack до версии 1902.

Исправления Azure Stack применимы только к интегрированным системам Azure Stack. Не устанавливайте исправления в пакете ASDK.

> [!TIP]  
> Подпишитесь на следующие веб-каналы *RSS* или *Atom*, чтобы следить за исправлениями Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Исправления для Azure Stack

- **1901**: [KB 4500636 — исправление Azure Stack 1.1901.5.109](https://support.microsoft.com/help/4500636).
- **1902**: [KB 4500637 — исправление Azure Stack 1.1902.3.75](https://support.microsoft.com/help/4500637).

## <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Версию 1902 можно установить непосредственно с помощью выпусков [1.1901.0.95 или 1.1901.0.99](azure-stack-update-1901.md#build-reference), не устанавливая предварительно исправления для версии 1901. Но если у вас установлено старое исправление **1901.2.103**, перед установкой версии 1902 необходимо установить новое [исправление 1901.3.105](https://support.microsoft.com/help/4495662).

- Перед началом установки этого обновления запустите [Test-AzureStack](azure-stack-diagnostic-test.md) со следующими параметрами для проверки состояния Azure Stack и устраните все найденные операционные проблемы, включая все предупреждения и сбои. Кроме того, просмотрите активные предупреждения и решите проблемы с теми, которые требуют принятия мер:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Если при выполнении **Test-AzureStack** включен параметр `AzsControlPlane`, то в выходных данных **Test-AzureStack** появится приведенная ниже ошибка. **FAIL Azure Stack Control Plane Websites Summary** (Сводка по плоскости управления Azure Stack: сбой). Данное сообщение об ошибке можно игнорировать.

- При управлении Azure Stack с помощью System Center Operations Manager (SCOM) обновите [пакет управления для Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) до версии 1.0.3.11 перед обновлением до версии 1902.

- Начиная с выпуска 1902, формат пакета для обновления Azure Stack изменился с **.bin/.exe/.xml** на **.zip/.xml**. Клиенты с подключенными единицами масштабирования Azure Stack увидят на портале сообщение **Доступно обновление**. Неподключенные клиенты теперь могут просто скачать и импортировать ZIP-файл с соответствующим XML-файлом.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Улучшения

- В сборке 1902 для портала администрирования Azure Stack представлен новый пользовательский интерфейс для создания планов, предложений, квот и дополнительных планов. Дополнительные сведения, включая снимки экрана, см. в статье [Создание плана в Azure Stack](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Повышена надежность увеличения объема во время операции добавления узла при переключении состояния единицы масштабирования с "Расширение хранилища" на "Выполняется".

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Чтобы обеспечить оптимальную целостность и безопасность пакета, а также упростить управление скачиваемыми данными в автономном режиме, корпорация Майкрософт изменила формат пакета обновления с EXE-файлов и BIN-файлов на ZIP-файл. Новый формат повышает надежность процесса распаковки, который иногда может вызывать остановку подготовки обновления. Тот же формат пакета также применяется к пакетам обновления от изготовителя оборудования.
- Упрощена работа с Azure Stack при выполнении Test-AzureStack. Теперь можно просто выполнить Test-AzureStack -Group UpdateReadiness, а не передавать десять дополнительных параметров после директивы Include.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Чтобы повысить общую надежность и доступность основных служб инфраструктуры во время обновления, собственный поставщик ресурсов обновления при необходимости будет определять и вызывать автоматические глобальные исправления как часть плана действий по обновлению. Рабочие процессы глобального исправления включают следующее:

  - Инфраструктура проверяется на наличие виртуальных машин, которые находятся не в оптимальном состоянии, и выполняется попытка устранить обнаруженные проблемы (при необходимости).
  - Выполняется проверка на наличие проблем со службой SQL в рамках плана управления и предпринимается попытка устранить их (при необходимости).
  - Проверяется служба программной подсистемы балансировки нагрузки (SLB) в сетевом контроллере (NC) и выполняется попытка устранить выявленные проблемы (при необходимости).
  - Проверяется состояние сетевого контроллера (NC) и выполняется попытка устранить выявленные проблемы (при необходимости).
  - Проверка состояния узлов структуры службы агента аварийного восстановления (ERCS) и устранение выявленных проблем (при необходимости).
  - Проверка состояния роли инфраструктуры и ее восстановление (при необходимости).
  - Проверка состояния узлов структуры службы Azure Consistent Storage (ACS) и устранение выявленных проблем (при необходимости).

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Усовершенствования средств диагностики Azure Stack для повышения надежности и производительности коллекции журналов. Дополнительное ведение журнала для сетевых служб и служб идентификации. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Повышена надежность Test-AzureStack при выполнении теста готовности к смене секретов.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Повышена надежность AD Graph при взаимодействии со средой Active Directory клиента.

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Улучшен сбор данных инвентаризации аппаратного обеспечения с помощью Get-AzureStackStampInformation.

- Для повышения надежности операций, выполняющихся в инфраструктуре ERCS, память каждого экземпляра ERCS увеличена с 8 ГБ до 12 ГБ. В установке интегрированных систем Azure Stack память в общем увеличивается до 12 ГБ.

<!-- 110303935 IcM Reported by HKEX -->
- В сборке 1902 устранена проблема со службой контроллеров виртуальных коммутаторов сети, при которой все виртуальные машины на определенном узле переходили в автономный режим.  При этом служба переставала отвечать и подключиться к ней не удавалось, а отработка отказа роли не выполнялась в другой работоспособный экземпляр. Решить проблему можно было только силами службы технической поддержки Майкрософт.

> [!IMPORTANT]
> Чтобы в процессе внесения исправлений и обновления простой клиента был минимальным, убедитесь, что для метки Azure Stack доступно более 12 ГБ свободного места. Для этого просмотрите колонку **Емкость**. Увеличение памяти отразится в колонке **Емкость** после успешной установки обновления.

## <a name="common-vulnerabilities-and-exposures"></a>Распространенные уязвимости и риски

В рамках этого обновления устанавливаются следующие обновления системы безопасности:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006);
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595);
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596);
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597);
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598);
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599);
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600);
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601);
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602);
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615);
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616);
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618);
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619);
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621);
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623);
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625);
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626);
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627);
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628);
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630);
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631);
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632);
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633);
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635);
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636);
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656);
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659);
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660);
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662);
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663).

Дополнительные сведения об этих уязвимостях доступны по предыдущим ссылкам или в статье базы знаний Майкрософт [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления

- При запуске [Test-AzureStack](azure-stack-diagnostic-test.md) выводится предупреждающее сообщение от контроллера управления основной платой (BMC). Это предупреждение можно проигнорировать.

- <!-- 2468613 - IS --> Во время установки этого обновления могут отображаться оповещения с заголовком `Error - Template for FaultType UserAccounts.New is missing.` Их можно игнорировать. Эти оповещения закроются автоматически после завершения установки этого обновления.

## <a name="post-update-steps"></a>Действия после обновления

- После установки этого обновления установите все применимые исправления. Дополнительные сведения см. в разделе [Исправления](#hotfixes), а также в статье [Политика обслуживания Azure Stack](azure-stack-servicing-policy.md).  

- Извлеките ключи шифрования неактивных данных и надежно сохраните их за пределами развертывания Azure Stack. Следуйте указаниям [о том, как извлечь ключи](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

Ниже перечислены известные проблемы после установки этой версии сборки.

### <a name="portal"></a>Microsoft Azure

<!-- 2930820 - IS ASDK --> 
- На порталах администратора и пользователя при поиске "Docker" элемент возвращается неправильно. Он недоступен в Azure Stack. При попытке его создания откроется колонка с указанием ошибки. 

<!-- 2931230 - IS  ASDK --> 
- Планы, добавленные в подписку пользователя как дополнительные, невозможно удалить даже при удалении основного плана из подписки. Дополнительный план будет оставаться в подписке, пока подписки, которые ссылаются на него, не будут удалены. 

<!-- TBD - IS ASDK --> 
- Два типа административной подписки, которые были добавлены в версии 1804, использовать не рекомендуется. Это такие типы подписки: **Подписка для учета** и **Подписка для** Эти типы подписок еще не готовы к использованию, но их уже можно увидеть в новых средах Azure Stack, начиная с версии 1804. Продолжайте использовать тип подписки **Поставщик по умолчанию**.

<!-- 3557860 - IS ASDK --> 
- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

<!-- 1663805 - IS ASDK --> 
- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. [Используйте PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan), чтобы проверить разрешения.

<!-- Daniel 3/28 -->
- Если на пользовательском портале перейти к большому двоичному объекту в учетной записи хранения и попытаться открыть раздел **Политика доступа**, соответствующее окно не отображается в дереве навигации. Чтобы обойти эту проблему, можно использовать приведенные ниже командлеты PowerShell для создания, получения, установки и удаления политик доступа соответственно.

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Службы вычислений

- При создании новой виртуальной машины Windows (VM) может отображаться следующая ошибка.

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Ошибка возникает, если вы включаете диагностику загрузки на виртуальной машине, но удаляете свою учетную запись хранения диагностики загрузки. Чтобы обойти эту проблему, повторно создайте учетную запись хранения с тем же именем, которое вы использовали ранее.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Интерфейс создания масштабируемого набора виртуальных машин предлагает образ на основе CentOS 7.2 в качестве варианта для развертывания. Так как этот образ не поддерживается в Azure Stack, выберите другую операционную систему для развертывания или используйте шаблон Azure Resource Manager, указав другой образ CentOS, скачанный оператором до развертывания из Marketplace.  

<!-- TBD - IS ASDK --> 
- После применения обновления 1902 могут возникнуть следующие проблемы при развертывании виртуальных машин с Управляемыми дисками.

   - Если подписка была создана до обновления 1808, при развертывании виртуальной машины с Управляемыми дисками может произойти сбой с сообщением о внутренней ошибке. Чтобы устранить ошибку, выполните следующие действия для каждой подписки.
      1. На портале клиента перейдите в раздел **Подписки** и найдите подписку. Щелкните **Поставщики ресурсов**, выберите **Microsoft.Compute**, а затем — **Повторная регистрация**.
      2. В той же подписке перейдите в раздел **Управление доступом (IAM)** и убедитесь, что указан **Azure Stack — Управляемый диск**.
   - Если вы настроили среды с несколькими клиентами, при развертывании виртуальных машин в подписке, которая связана с гостевым каталогом, возможен сбой с сообщением о внутренней ошибке. Для устранения этой ошибки выполните действия, описанные в [этой статье](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory), чтобы перенастроить все гостевые каталоги.

- Виртуальная машина Ubuntu 18.04, созданная с включенной авторизацией SSH, не позволяет использовать ключи SSH для входа. В качестве решения используйте расширение доступа к виртуальной машине для Linux для реализации ключей SSH после подготовки или проверку подлинности на основе пароля.

- Масштабируемый набор невозможно удалить в колонке **Масштабируемые наборы виртуальных машин**. В качестве решения выберите масштабируемый набор, который необходимо удалить, затем нажмите кнопку **Удалить** на панели **Обзор**.

### <a name="networking"></a>Сеть  

<!-- 3239127 - IS, ASDK -->
- При изменении на портале Azure Stack статического IP-адреса для конфигурации IP, которая привязана к сетевому адаптеру, подключенному к экземпляру виртуальной машины, вы увидите предупреждение: 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Вы можете игнорировать это сообщение, так как IP-адрес изменится, даже если не перезапустить экземпляр виртуальной машины.

<!-- 3632798 - IS, ASDK -->
- На портале при добавлении правила безопасности входящего трафика и выборе в качестве источника **тега службы** в списке **тегов источников** отображаются несколько вариантов, которые недоступны для Azure Stack. Ниже перечислены варианты, допустимые в Azure Stack:

  - **Интернет**;
  - **VirtualNetwork**;
  - **AzureLoadBalancer**.
  
  Другие варианты не поддерживаются как теги источников в Azure Stack. Аналогично, если добавить правило безопасности для исходящего трафика и выбрать **тег службы** как целевой объект, отобразится тот же список вариантов для **тега источника**. Единственные допустимые варианты такие же, как и для **тегов источников** (приведены в предыдущем списке).

- Группы безопасности сети (NSG) работают в Azure Stack не так, как в глобальной среде Azure. В Azure можно задать несколько портов в одном правиле группы безопасности сети (с помощью портала, PowerShell и шаблонов Resource Manager). Однако в Azure Stack невозможно задать несколько портов в одном правиле группы безопасности сети с помощью портала. Чтобы обойти эту проблему, используйте шаблон Resource Manager или PowerShell для установки этих дополнительных правил.

<!-- 3203799 - IS, ASDK -->
- В настоящее время Azure Stack не поддерживает подключение более 4 сетевых интерфейсов (NIC) к экземплярам виртуальных машин, независимо от размера экземпляра.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Служба приложений

<!-- 2352906 - IS ASDK --> 
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog 

- Конфигурация системного журнала не сохраняется в цикле обновления, поэтому клиент системного журнала теряет свою конфигурацию, а пересылка сообщений системного журнала останавливается. Эта проблема относится ко всем версиям платформы Azure Stack, начиная с общедоступной версии клиента системного журнала (1809). Чтобы обойти эту проблему, повторно настройте клиент системного журнала после установки обновления Azure Stack.

## <a name="download-the-update"></a>Скачивание обновления

Пакет обновления 1902 для Azure Stack можно скачать [здесь](https://aka.ms/azurestackupdatedownload). 

Только подключенные сценарии: развертывания Azure Stack будут периодически проверять защищенную конечную точку и автоматически сообщать, доступно ли обновление для вашего облака. Дополнительные сведения см. в статье [Управление обновлениями для Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения об управлении обновлениями в Azure Stack](azure-stack-updates.md).  
- [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
- В [этой статье](azure-stack-servicing-policy.md) описаны политика обслуживания для интегрированных систем Azure Stack и действия, необходимые для сохранения поддерживаемого состояния системы.  
- Сведения об использовании привилегированной конечной точки (PEP) для отслеживания и возобновления обновлений см. в статье [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](azure-stack-monitor-update.md).  
