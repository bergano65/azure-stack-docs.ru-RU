---
title: Подключение Azure Stack к Azure с помощью ExpressRoute | Документация Майкрософт
description: Сведения о подключении виртуальных сетей в Azure Stack к виртуальным сетям в Azure с помощью ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 2fee81a79b58294a9c43f8d550f139157cb64a9b
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283573"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Подключение Azure Stack к Azure с помощью Azure ExpressRoute

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье показано, как подключить виртуальную сеть Azure Stack к виртуальной сети Azure с помощью прямого подключения [Microsoft Azure ExpressRoute](/azure/expressroute/).

Можно использовать эту статью как руководство и настроить похожую тестовую среду с применением приведенных здесь примеров. Эту статью также можно использовать в качестве пошагового руководства по настройке собственной среды ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Обзор, предположения и предварительные требования

Azure ExpressRoute позволяет переносить локальные сети в Microsoft Cloud по частному подключению, которое предоставляется поставщиком услуг подключения. ExpressRoute не поддерживает VPN-подключение через общедоступный Интернет.

Дополнительные сведения об Azure ExpressRoute см. в [этой статье](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Предположения

В этой статье предполагается, что:

* у вас есть опыт работы с Azure;
* у вас есть базовое представление об Azure Stack;
* у вас есть базовое представление о сетевых подключениях.

### <a name="prerequisites"></a>Предварительные требования

Чтобы подключить Azure Stack и Azure с помощью ExpressRoute, требуются следующие компоненты:

* [Канал ExpressRoute](/azure/expressroute/expressroute-circuit-peerings), подготовленный через [поставщика услуг](/azure/expressroute/expressroute-locations).
* Подписка Azure, используемая при создании канала ExpressRoute и виртуальных сетей в Azure.
* Маршрутизатор, который:
  * Поддерживает VPN-подключения типа "сеть — сеть" между интерфейсом локальной сети и мультитенантным шлюзом Azure Stack.
  * Поддерживает создание нескольких компонентов VRF (Virtual Routing and Forwarding), если в вашем развертывании Azure Stack больше одного клиента.
* Маршрутизатор с:
  * портом WAN, подключенным к каналу ExpressRoute;
  * Портом локальной сети, подключенным к мультитенантному шлюзу Azure Stack.

### <a name="expressroute-network-architecture"></a>Сетевая архитектура ExpressRoute

На следующем рисунке показаны среды Azure Stack и Azure после настройки ExpressRoute с использованием примеров в этой статье:

![Сеть ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

На следующем рисунке показано подключение нескольких клиентов из инфраструктуры Azure Stack через маршрутизатор ExpressRoute к Azure в Microsoft Edge:

![Мультитенантные подключения с использованием ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

В примере в этой статье используется одна и та же мультитенантная архитектура, показанная на этой диаграмме, для подключения Azure Stack к Azure с помощью частного пиринга ExpressRoute. Это делается через VPN-подключение типа "сеть — сеть" из шлюза виртуальной сети в Azure Stack к маршрутизатору ExpressRoute.

В этой статье описано, как создать сквозное подключение между двумя виртуальными сетями из двух разных клиентов в Azure Stack к соответствующим виртуальным сетям в Azure. Настраивать два клиента необязательно. С помощью этих действий можно также настроить один клиент.

## <a name="configure-azure-stack"></a>Настройка Azure Stack

Чтобы настроить среду Azure Stack для первого клиента, используйте в качестве руководства следующие шаги. Если вы устанавливаете несколько клиентов, просто повторите эти действия:

>[!NOTE]
>Следуя этим действиям, вы узнаете, как создавать ресурсы с помощью портала Azure Stack, но то же самое можно сделать и с помощью PowerShell.

![Настройка сети Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Перед началом работы

Прежде чем приступать к настройке Azure Stack, требуется следующее:

* Развертывание системы с Azure Stack или развертывание Пакета средств разработки Azure Stack (ASDK). Сведения о развертывании ASDK см. в статье [Краткое руководство по оценке Пакета средств разработки Azure Stack](../asdk/asdk-download.md).
* Предложение Azure Stack, на которое могут подписаться пользователи. Дополнительные сведения см. в статье [Service, plan, offer, subscription overview](service-plan-offer-subscription-overview.md) (Обзор служб, планов, предложений и подписок).

### <a name="create-network-resources-in-azure-stack"></a>Создание сетевых ресурсов в Azure Stack

Ниже приведены инструкции по созданию необходимых ресурсов сети в Azure Stack для клиента.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Создание виртуальной сети и подсети виртуальной машины

1. Войдите на портал пользователя Azure Stack.

2. На портале выберите **+ Создать ресурс**.

3. В разделе **Azure Marketplace** щелкните **Сети**.

4. В разделе **Рекомендованные** выберите **Виртуальная сеть**.

5. В разделе **Создать виртуальную сеть** введите показанные в приведенной ниже таблице значения в соответствующие поля:

   |Поле  |Значение  |
   |---------|---------|
   |ИМЯ     |Tenant1VNet1         |
   |Пространство адресов     |10.1.0.0/16|
   |Имя подсети     |Tenant1-Sub1|
   |Диапазон адресов подсети     |10.1.1.0/24|

6. В поле **Подписка** должна появиться созданная ранее подписка. В оставшихся полях:

    * В разделе **Группа ресурсов** щелкните **Создать**, чтобы создать группу ресурсов, или если она у вас уже есть, выберите **Использовать существующий**.
    * Проверьте значение **расположения** по умолчанию.
    * Нажмите кнопку **Создать**.
    * (Необязательно.) Установите флажок **Закрепить на панели мониторинга**.

#### <a name="create-the-gateway-subnet"></a>Создание подсети шлюза

1. В разделе **Виртуальная сеть** выберите **Tenant1VNet1**.
1. В разделе **Параметры** выберите **Subnets** (Подсети).
1. Щелкните **+ Подсеть шлюза**, чтобы добавить подсеть шлюза в виртуальную сеть.
1. Подсети присвоено имя **GatewaySubnet** по умолчанию. Подсети шлюза являются исключениями, и для правильной работы они должны использовать это имя.
1. Проверьте, задано ли в поле **Диапазон адресов** значение **10.1.0.0/24**.
1. Нажмите кнопку **ОК**, чтобы создать подсеть шлюза.

#### <a name="create-the-virtual-network-gateway"></a>Создание шлюза виртуальной сети

1. Щелкните **+ Create a resource** (+ Создать ресурс) на пользовательском портале Azure Stack.
1. В разделе **Azure Marketplace** щелкните **Сети**.
1. В списке сетевых ресурсов выберите **Шлюз виртуальной сети**.
1. В поле **Имя** введите **GW1**.
1. Щелкните **Виртуальная сеть**.
1. Выберите **Tenant1VNet1** из раскрывающегося списка.
1. Выберите **Общедоступный IP-адрес**, затем **Выбрать общедоступный IP-адрес** и щелкните **Создать**.
1. В поле **Имя** введите **GW1-PiP** и нажмите кнопку **ОК**.
1. Для параметра **Тип VPN** должно быть установлено значение **Route-based** (На основе маршрутов) по умолчанию. Не изменяйте значение этого параметра.
1. Убедитесь, что для параметров **Подписка** и **Расположение** выбраны правильные значения. Нажмите кнопку **Создать**.

#### <a name="create-the-local-network-gateway"></a>Создание шлюза локальной сети

Ресурс шлюза локальной сети идентифицирует удаленный шлюз на другом конце VPN-подключения. В этом примере в качестве удаленного компонента подключения используется вложенный интерфейс локальной сети маршрутизатора ExpressRoute. 10.60.3.255 — удаленный адрес клиента 1 из предыдущей диаграммы.

1. Войдите на портал пользователя Azure Stack и выберите **Создать ресурс**.
1. В разделе **Azure Marketplace** щелкните **Сети**.
1. В списке сетевых ресурсов выберите **шлюз локальной сети**.
1. В поле **Имя** введите **ER-Router-GW**.
1. Значение поля **IP-адрес** см. на предыдущем рисунке. Вложенный интерфейс локальной сети маршрутизатора ExpressRoute для клиента 1 имеет IP-адрес 10.60.3.255. Введите IP-адрес соответствующего интерфейса маршрутизатора своей среды.
1. В поле **Адресное пространство** введите адресное пространство виртуальных сетей, которые нужно подключить к Azure. Подсети для клиента 1 выглядят следующим образом.

   * 192.168.2.0/24 — центральная виртуальная сеть в Azure;
   * 10.100.0.0/16 — периферийная виртуальная сеть в Azure.

   > [!IMPORTANT]
   > В этом примере предполагается, что вы используете статические маршруты подключения VPN типа "сеть — сеть" между шлюзом Azure Stack и маршрутизатором ExpressRoute.

1. Проверьте, выбраны ли правильные значения в полях **Подписка**, **Группа ресурсов** и **Расположение**. Щелкните **Создать**.

#### <a name="create-the-connection"></a>Создание подключения

1. Выберите **+ Создать ресурс** на пользовательском портале Azure Stack.
1. В разделе **Azure Marketplace** щелкните **Сети**.
1. В списке сетевых ресурсов выберите **Подключение**.
1. В разделе **Базовые** выберите **Сеть-сеть (IPSec)** в качестве значения параметра **Тип подключения**.
1. Выберите **подписку**, **группу ресурсов** и **расположение**. Последовательно выберите **ОК**.
1. В разделе **Параметры** выберите **Шлюз виртуальной сети**, а затем щелкните **GW1**.
1. Выберите **Шлюз локальной сети**, а затем щелкните **ER Router GW**.
1. В поле **Имя подключения** введите **ConnectToAzure**.
1. В поле **Общий ключ (PSK)** введите **abc123**, а затем нажмите кнопку **ОК**.
1. В разделе **Сводка** нажмите кнопку **ОК**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Получение общедоступного IP-адреса шлюза виртуальной сети

После создания шлюза виртуальной сети можно получить общедоступный IP-адрес шлюза. Запишите этот адрес, так как он может понадобиться вам позже для развертывания. В зависимости от развертывания, этот адрес используется в качестве **внутреннего IP-адреса**.

1. На пользовательском портале Azure Stack щелкните **Все ресурсы**.
1. В разделе **Все ресурсы** выберите шлюз виртуальной сети **GW1**, который приведен в примере.
1. В разделе **Шлюз виртуальной сети** щелкните **Обзор** в списке ресурсов. Кроме того, можно выбрать **Свойства**.
1. IP-адрес, который нужно записать, указан в поле **Общедоступный IP-адрес**. Для примера конфигурации этот адрес — 192.68.102.1.

#### <a name="create-a-virtual-machine-vm"></a>Создание виртуальной машины

Чтобы протестировать передачу данных через VPN-подключение, в каждой виртуальной сети Azure Stack необходимы виртуальные машины, отправляющие и получающие данные. Создайте виртуальную машину и разверните ее в подсети виртуальной машины для вашей виртуальной сети.

1. Выберите **+ Создать ресурс** на пользовательском портале Azure Stack.
1. В разделе **Azure Marketplace** щелкните **Вычисление**.
1. В списке образов виртуальных машин выберите образ **Windows Server 2016 Datacenter Eval**.

   >[!NOTE]
   >Если образ, используемый в этой статье, недоступен, попросите оператора Azure Stack предоставить другой образ Windows Server.

1. В разделе **Создание виртуальной машины** выберите **Основные сведения**, затем введите **VM01** в качестве **имени**.
1. Введите допустимое имя пользователя и пароль. Эта учетная запись будет использоваться для входа в виртуальную машину после ее создания.
1. Укажите **подписку**, **группу ресурсов** и **расположение**. Нажмите кнопку **ОК**.
1. В разделе **выбора размера** выберите размер виртуальной машины для этого экземпляра, а затем щелкните **Выбрать**.
1. Убедитесь, что в разделе **Параметры** указаны:

   * виртуальная сеть **Tenant1VNet1**;
   * подсеть **10.1.1.0/24**.

   Используйте параметры по умолчанию и нажмите кнопку **ОК**.

1. В разделе **Сводка** проверьте конфигурацию виртуальной машины, а затем нажмите кнопку **ОК**.

Чтобы добавить несколько клиентов, повторите действия, которые описаны в следующих разделах:

* [Создание виртуальной сети и подсети виртуальной машины](#create-the-virtual-network-and-vm-subnet)
* [Создание подсети шлюза](#create-the-gateway-subnet)
* [Создание шлюза виртуальной сети](#create-the-virtual-network-gateway)
* [Создание локального сетевого шлюза](#create-the-local-network-gateway)
* [Создание подключения](#create-the-connection)
* [Создание виртуальной машины](#create-a-virtual-machine)

Если вы используете клиент 2 в качестве примера, не забудьте изменить IP-адреса, чтобы избежать перекрытия.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>Настройка виртуальной машины NAT для обхода шлюза

> [!IMPORTANT]
> Этот раздел касается только развертываний ASDK. В развертываниях с несколькими узлами преобразование сетевых адресов (NAT) не требуется.

ASDK работает автономно и изолирован от сети, в которой развернут физический узел. Сеть VIP, к которой подключены шлюзы, на самом деле не внешняя. Она скрыта за маршрутизатором, выполняющим преобразование сетевых адресов (NAT).

Маршрутизатор — это узел ASDK, на котором запущена роль службы маршрутизации и удаленного доступа (RRAS). NAT необходимо настроить на узле ASDK, чтобы установить VPN-подключение типа "сеть — сеть" на обоих концах.

#### <a name="configure-the-nat"></a>Настройка NAT

1. Войдите в систему главного компьютера Azure Stack с учетной записью администратора.
1. Выполните скрипт в интегрированной среде сценариев PowerShell с повышенными привилегиями. Этот сценарий возвращает **адрес EBGP**.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. Чтобы настроить NAT, вставьте и измените приведенный ниже сценарий PowerShell. Отредактируйте сценарий, чтобы заменить `External BGPNAT address` и `Internal IP address` следующими значениями:

   * для *адреса EBGP* укажите 10.10.0.62;
   * для *внутреннего IP-адреса* укажите 192.168.102.1.

   Выполните следующий сценарий в интегрированной среде сценариев PowerShell с повышенными правами.

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Настройка Azure

После завершения настройки Azure Stack можно развернуть ресурсы Azure. На рисунке ниже приведен пример виртуальной сети клиента в Azure. Вы можете использовать любые имя и схему адресации для своей виртуальной сети Azure. Однако диапазоны адресов виртуальных сетей в Azure и Azure Stack должны быть уникальными и не перекрываться:

![Виртуальные сети Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Ресурсы, развертываемые в Azure и Azure Stack, похожи. Вы развернете следующие компоненты:

* виртуальные сети и подсети;
* подсеть шлюза;
* шлюз виртуальной сети;
* подключение;
* канал ExpressRoute.

Для примера сетевой инфраструктуры Azure используется следующая конфигурация:

* Центральная (192.168.2.0/24) и периферийная (10.100.0.0./16) виртуальные сети. Дополнительные сведения о звездообразной топологии сети см. в статье [Реализация звездообразной топологии сети в Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Рабочие нагрузки развертываются в периферийной виртуальной сети, а канал ExpressRoute подключается к центральной виртуальной сети.
* Две виртуальные сети подключены с помощью пиринга.

### <a name="configure-the-azure-vnets"></a>Настройка виртуальных сетей Azure

1. Войдите на портал Azure с помощью своих учетных данных Azure.
1. Создайте центральную виртуальную сеть в адресном диапазоне 192.168.2.0/24.
1. Создайте подсеть в диапазоне адресов 192.168.2.0/25 и добавьте подсеть шлюза в диапазон адресов 192.168.2.128/27.
1. Создайте периферийную виртуальную сеть и подсеть в диапазоне адресов 10.100.0.0/16.

Дополнительные сведения о создании виртуальной сети в Azure см. в [этой статье](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Настройка канала ExpressRoute

1. Просмотрите предварительные требования ExpressRoute в [этой статье](/azure/expressroute/expressroute-prerequisites).

1. Создайте канал ExpressRoute с помощью подписки Azure, как описано в статье [Создание и изменение канала ExpressRoute](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager).

   >[!NOTE]
   >Предоставьте службе ключ службы для канала, чтобы она могла настроить канал ExpressRoute на своей стороне.

1. Настройте частный пиринг в канале ExpressRoute, следуя указаниям из статьи [Создание и изменение пиринга для канала ExpressRoute](/azure/expressroute/expressroute-howto-routing-portal-resource-manager).

### <a name="create-the-virtual-network-gateway"></a>Создание шлюза виртуальной сети

Создайте шлюз виртуальной сети ExpressRoute в центральной виртуальной сети, следуя указаниям из статьи [Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell](/azure/expressroute/expressroute-howto-add-gateway-resource-manager).

### <a name="create-the-connection"></a>Создание подключения

Чтобы связать канал ExpressRoute с центральной виртуальной сетью, следуйте инструкциям в статье [Подключение виртуальной сети к каналу ExpressRoute](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

### <a name="peer-the-vnets"></a>Установление пиринга между виртуальными сетями

Установите пиринг между центральной и периферийной виртуальными сетями, следуя инструкциям в статье [Руководство. Подключение виртуальных сетей с помощью пиринга и портала Azure](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). При настройке пиринговой связи между виртуальными сетями убедитесь, что выбраны следующие параметры:

* Из центральной виртуальной сети к периферийной виртуальной сети: **Разрешить транзит шлюзов**.
* Из периферийной виртуальной сети к центральной виртуальной сети: **Use remote gateway** (Использовать удаленный шлюз).

### <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Разверните виртуальные машины рабочей нагрузки в периферийную виртуальную сеть.

Повторите эти действия для всех дополнительных виртуальных сетей клиента, которые необходимо подключить в Azure, через их соответствующие каналы ExpressRoute.

## <a name="configure-the-router"></a>Настройка маршрутизатора

Вы можете использовать следующую схему конфигурации маршрутизатора ExpressRoute как руководство по настройке маршрутизатора ExpressRoute. На этом рисунке показаны два клиента (клиент 1 и клиент 2) и их соответствующие каналы ExpressRoute. Каждый клиент связан с собственным компонентом VRF (Virtual Routing and Forwarding) в локальной и глобальной сети маршрутизатора ExpressRoute. Эта конфигурация обеспечивает сквозную изоляцию между двумя клиентами. Запишите IP-адреса, используемые в интерфейсах маршрутизатора, продолжая работать с примером конфигурации.

![Конфигурация маршрутизатора ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Вы можете использовать любой маршрутизатор, поддерживающий протокол IKEv2 VPN и BGP, чтобы разорвать VPN-подключение типа "сеть — сеть" с Azure Stack. Тот же маршрутизатор используется при подключении к Azure через канал ExpressRoute.

Следующий пример конфигурации Cisco ASR 1000 Series Aggregation Services Router поддерживает сетевую инфраструктуру, показанную на схеме *конфигурации маршрутизатора ExpressRoute*.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Проверка подключения

После установления подключения типа "сеть — сеть" и настройки канала ExpressRoute следует проверить подключение.

Выполните следующие тесты проверки связи:

* Войдите на одну из виртуальных машин в виртуальной сети Azure и проверьте связь с виртуальной машиной, созданной в Azure Stack.
* Войдите на одну из виртуальных машин в Azure Stack и проверьте связь с виртуальной машиной, созданной в виртуальной сети Azure.

>[!NOTE]
>Чтобы убедиться, что трафик передается через подключение типа "сеть — сеть" и ExpressRoute, с обеих сторон нужно проверить связь с выделенным IP-адресом виртуальной машины, а не виртуальным IP-адресом.

### <a name="allow-icmp-in-through-the-firewall"></a>Разрешение ICMP-трафика через брандмауэр

По умолчанию Windows Server 2016 не разрешает получение ICMP-пакетов через брандмауэр. Для каждой виртуальной машины, которая используется для проверки связи, необходимо разрешить получение ICMP-пакетов. Чтобы создать правило брандмауэра для ICMP, выполните следующий командлет в окне PowerShell с повышенными привилегиями.

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-vm"></a>Проверка связи с виртуальной машиной Azure Stack

1. Войдите на портал пользователя Azure Stack.

1. Найдите созданную виртуальную машину и выберите ее.

1. Нажмите кнопку **Подключиться**.

1. Из командной строки Windows или PowerShell с повышенными привилегиями введите **ipconfig/all**. Запишите IPv4-адрес, возвращаемый в выходных данных.

1. Проверьте связь с этим адресом IPv4 с виртуальной машины в виртуальной сети Azure.

   В примере среды этот IPv4-адрес получен из подсети 10.1.1.x/24. В вашей среде адрес может отличаться, но он должен находиться в подсети, которую вы создали для подсети виртуальной сети клиента.

### <a name="view-data-transfer-statistics"></a>Просмотр статистики передачи данных

Если вы хотите знать, какой объем трафика передается через подключение, эти сведения можно просмотреть на пользовательском портале Azure Stack. Просмотр статистики передачи данных также помогает выяснить, передавались ли данные при проверке связи через подключения VPN и ExpressRoute.

1. Войдите на портал пользователя Azure Stack и выберите **Все ресурсы**.
1. Перейдите в группу ресурсов вашего VPN-шлюза и выберите тип объекта **Подключение**.
1. В списке щелкните подключение **ConnectToAzure**.
1. В разделе **Подключения** > **Обзор** можно просмотреть статистику в полях **Входящие данные** и **Исходящие данные**. Там будут отображаться ненулевые значения.

   ![Входящие и исходящие данные](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Дополнительная информация

[Развертывание приложений в Azure и Azure Stack](../user/azure-stack-solution-pipeline.md )
