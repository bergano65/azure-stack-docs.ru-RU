---
title: Настройка VPN-подключений типа "сеть — сеть" с использованием протоколов IPsec и IKE | Документация Майкрософт
description: Узнайте об использовании и настройке политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или подключений "виртуальная сеть — виртуальная сеть" в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 9c8911452dc77f77156c1256e42c4624b08b5648
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959313"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"

В этой статье описано, как настроить политики IPsec/IKE для VPN-подключений типа "сеть — сеть" в Azure Stack.

>[!NOTE]
> Чтобы использовать эту функцию, требуется сборка **1809** Azure Stack или более поздняя версия.  При использовании сборки до 1809 обновите систему Azure Stack до последней сборки, прежде чем выполнять действия, описанные в этой статье.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Параметры политики IPsec и IKE для VPN-шлюзов

Стандарт протоколов IPsec и IKE поддерживает широкий набор алгоритмов шифрования в разных комбинациях. Сведения о параметрах, поддерживаемых в Azure Stack, приведены в разделе [Параметры IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters). Эти параметры помогут выполнить требования к обеспечению безопасности или соответствия.

Эта статья содержит инструкции по созданию и настройке политики IPsec/IKE, а также ее применению к новому или существующему подключению.

## <a name="considerations"></a>Рекомендации

Учитывайте следующие рекомендации при использовании этих политик:

- Политика IPsec/IKE поддерживается только номерами SKU шлюзов уровня *Стандартный* и *Высокопроизводительный* (на основе маршрутов).

- Можно указать только **одну** комбинацию политик для каждого подключения.

- Вам следует указать все алгоритмы и параметры для IKE (основной режим) и IPsec (быстрый режим). Указать частичную спецификацию политики невозможно.

- Ознакомьтесь со спецификациями поставщиков VPN-устройств, чтобы убедиться, что политика поддерживается на локальных VPN-устройствах. Если политики несовместимы, невозможно будет установить подключения типа "сеть — сеть".

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Часть 1. Рабочий процесс создания и настройки политики IPsec/IKE

В этом разделе описывается рабочий процесс создания и обновления политики IPsec/IKE для VPN-подключений типа "сеть — сеть":

1. Создание виртуальной сети и VPN-шлюза.

2. Создание шлюза локальной сети для распределенного подключения.

3. Создание политики IPsec/IKE с выбранными алгоритмами и параметрами.

4. Создание VPN-подключения типа "сеть — сеть" с использованием политики IPsec/IKE.

5. Добавление, обновление и удаление политики IPsec/IKE для существующего подключения.

Инструкции из этой статьи помогут установить и настроить политики IPsec/IKE, как показано на рисунке.

![Установка и настройка политик IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Часть 2. Поддерживаемые алгоритмы шифрования и уровни надежности ключей

В таблице ниже перечислены поддерживаемые алгоритмы шифрования и уровни надежности ключей, которые могут настраивать клиенты Azure Stack.

| IPsec/IKEv2                                          | Параметры                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Шифрование IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Проверка целостности IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Группа DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048 (DHGroup2), DHGroup1, DHGroup1, нет         |
| Шифрование IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, нет |
| Целостность IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Группа PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, нет                         |
| Время существования QM SA                                       | (Необязательно — используются значения по умолчанию, если не заданы другие значения.)<br />                         Секунды (целое число, минимум 300, по умолчанию — 27 000 с)<br />                         Килобайты (целое число, минимум 1024, по умолчанию — 102 400 000 КБ) |
| Селектор трафика                                     | Селекторы трафика на основе политик не поддерживаются в Azure Stack.         |

- Ваша конфигурация локальных VPN-устройств должна совпадать со следующими алгоритмами и параметрами, указанными в политике Azure IPsec/IKE, или содержать их.

  - алгоритм шифрования IKE (основной режим или фаза 1);
  - алгоритм обеспечения целостности IKE (основной режим или фаза 1);
  - группа DH (основной режим или фаза 1);
  - алгоритм шифрования IKE (основной режим или фаза 2);
  - алгоритм обеспечения целостности IPsec (быстрый режим или фаза 2);
  - группа PFS (быстрый режим или фаза 2).
  - Время существования SA указывается исключительно в локальных спецификациях и эти значения не обязательно должны совпадать.

- Если для шифрования IPsec используется алгоритм GCMAES, необходимо указать одинаковую длину алгоритма и ключа для проверки целостности IPsec. Например, для обоих можно использовать GCMAES128.

- В приведенной выше таблице:

  - IKEv2 соответствует основному режиму или фазе 1;
  - IPsec соответствует быстрому режиму или фазе 2;
  - группа DH определяет группу Диффи — Хеллмана, которая используется в основном режиме или фазе 1;
  - группа PFS определяет группу Диффи — Хеллмана, которая используется в быстром режиме или фазе 2.

- Время существования SA основного режима IKEv2 составляет 28 800 секунд на VPN-шлюзах Azure Stack.

В следующей таблице перечислены соответствующие группы Диффи — Хеллмана, поддерживаемые пользовательскими политиками.

| Группа Диффи — Хелмана | DHGroup   | PFSGroup      | Длина ключа    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP (768 бит)  |
| 2                    | DHGroup2  | PFS2          | MODP (1024 бит) |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP (2048 бит) |
| 19                   | ECP256    | ECP256        | ECP (256 бит)   |
| 20                   | ECP384    | ECP384        | ECP (384 бит)   |
| 24                   | DHGroup24 | PFS24         | MODP (2048 бит) |

Дополнительные сведения см. на страницах [RFC 3526](https://tools.ietf.org/html/rfc3526) и [RFC 5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Часть 3. Создание нового подключения VPN типа "сеть — сеть" с использованием политики IPsec/IKE

В этом разделе описаны действия по созданию VPN-подключения типа "сеть — сеть" с использованием политики IPsec/IKE. С помощью приведенных ниже инструкций вы сможете создать подключение, как показано на следующем рисунке.

![Политика "сеть — сеть"](media/azure-stack-vpn-s2s/site-to-site.png)

См. дополнительные сведения о [создании виртуальной сети с VPN-подключением типа "сеть — сеть"](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).

- Командлеты PowerShell для Azure Resource Manager. Получите дополнительные сведения об [установке командлетов PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Шаг 1. Создание виртуальной сети, VPN-шлюза и шлюза локальной сети

#### <a name="1-declare-variables"></a>1. Объявление переменных

Этот пример начнем с объявления переменных. Обязательно замените заполнители своими значениями при настройке для рабочей среды.

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Подключение к подписке Azure и создание группы ресурсов

Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. См. дополнительные сведения о [подключении к Azure Stack в роли пользователя с помощью PowerShell](azure-stack-powershell-configure-user.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Создание виртуальной сети, VPN-шлюза и шлюза локальной сети

В следующем примере создается виртуальная сеть **TestVNet1** с тремя подсетями и VPN-шлюзом. При замене значений важно, чтобы вы назвали подсеть шлюза именем **GatewaySubnet**. Если вы используете другое имя, создание шлюза завершится сбоем.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Шаг 2. Создание VPN-подключения типа "сеть — сеть" с использованием политики IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Создайте политику IPsec/IKE

Этот пример скрипта создает политику IPsec/IKE со следующими алгоритмами и параметрами:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, нет, срок действия SA (14 400 секунд и 102 400 000 КБ).

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Если для IPsec используется алгоритм GCMAES, необходимо указать одинаковую длину ключа и алгоритма для шифрования и целостности данных IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Создайте подключение VPN типа "сеть — сеть" с использованием политики IPsec/IKE

Создайте VPN-подключение типа "сеть — сеть" и примените политику IPsec/IKE, созданную на предыдущем шаге.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> После указания для подключения политики IPsec/IKE VPN-шлюз Azure будет только отправлять и принимать предложения IPsec/IKE с определенными алгоритмами шифрования и уровнями стойкости ключей для этого подключения. Локальное VPN-устройство для подключения должно использовать или принимать точную комбинацию политик. В противном случае VPN-туннель типа "сеть — сеть" не будет установлен.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Часть 4. Обновление политики IPsec/IKE для подключения

В предыдущем разделе было показано, как управлять политикой IPsec/IKE для существующего подключения типа "сеть — сеть". В этом разделе описаны следующие операции в отношении подключения:

1. Отображение политики подключения IPsec/IKE.
2. Добавление или обновление политики IPsec/IKE для подключения.
3. Удаление политики IPsec/IKE для подключения.

> [!NOTE]
> Политика IPsec/IKE поддерживается только для *стандартных* и *высокопроизводительных* VPN-шлюзов на основе маршрутов. Она не поддерживается для шлюза с номером SKU *Базовый*.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Отображение политики подключения IPsec/IKE

В примере ниже показано, как получить настроенную для подключения политику IPsec/IKE. Эти скрипты основаны на скриптах из предыдущих разделов.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Последняя команда отображает текущую политику IPsec/IKE, настроенную для подключения, если она есть. Далее приводится пример выходных данных для подключения:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Если настроенная политика IPsec/IKE отсутствует, команда `$connection6.policy` возвращает пустые выходные данные. Это не значит, что для подключения не настроена политика IPsec/IKE. Это значит, что отсутствует настраиваемая политика IPsec/IKE. Фактическое подключение использует политику по умолчанию, согласованную между локальным VPN-устройством и VPN-шлюзом Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Добавление или обновление политики IPsec/IKE для подключения

Действия по добавлению новой политики для подключения или обновлению существующей аналогичны — создайте политику, а затем примените ее к подключению.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Можно еще раз получить сведения о подключении, чтобы проверить, обновилась ли политика.

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

В последней строке вы должны увидеть следующие выходные данные:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Удаление политики IPsec/IKE из подключения

После удаления настраиваемой политики из подключения VPN-шлюз Azure возвращается к [списку предложений IPsec/IKE по умолчанию](azure-stack-vpn-gateway-settings.md#ipsecike-parameters) и возобновляет согласование для локального VPN-устройства.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Вы снова можете использовать тот же скрипт, чтобы проверить, удалена ли политика из подключения.

## <a name="next-steps"></a>Дополнительная информация

- [Параметры конфигурации VPN-шлюза для Azure Stack](azure-stack-vpn-gateway-settings.md)
