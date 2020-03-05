---
title: Создание виртуальной машины Windows Server с помощью PowerShell в Azure Stack Hub
description: Создайте виртуальную машину Windows Server с помощью PowerShell в Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 14192029c2bbac427b6609abfc62b06e0ef937cb
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703746"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack-hub"></a>Краткое руководство. Создание виртуальной машины Windows Server с помощью PowerShell в Azure Stack Hub

Вы можете создать виртуальную машину Windows Server 2016 с помощью PowerShell в Azure Stack Hub. Чтобы создать и использовать виртуальную машину, выполните действия, описанные в этой статье. В этой статье приведены инструкции, которые помогут вам:

* подключиться к виртуальной машине через удаленный клиент;
* установить веб-сервер IIS и открыть его стандартную домашнюю страницу;
* очистить использованные ресурсы.

> [!NOTE]
>  Приведенные в этой статье инструкции можно выполнить из Пакета средств разработки Azure Stack или внешнего клиента на базе Windows (при подключении через VPN).

## <a name="prerequisites-for-windows-server-vm"></a>Предварительные требования для виртуальной машины Windows Server

* Убедитесь, что оператор Azure Stack Hub добавил образ **Windows Server 2016** в Azure Stack Hub Marketplace.

* Для создания и администрирования ресурсов в Azure Stack Hub требуется определенная версия Azure PowerShell. Если вы еще не настроили PowerShell для Azure Stack Hub, выполните действия по [установке](../operator/azure-stack-powershell-install.md) PowerShell.

* Настроив PowerShell для Azure Stack Hub, подключитесь к среде Azure Stack Hub. Инструкции см. в руководстве по [настройке пользовательской среды PowerShell в Azure Stack Hub](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором выполняется развертывание и администрирование ресурсов Azure Stack Hub. Из пакета средств разработки или интегрированной системы Azure Stack Hub выполните следующий блок кода, чтобы создать группу ресурсов. 

> [!NOTE]
> В примерах кода всем переменным уже присвоены значения. Но вы можете изменить эти значения, если потребуется.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Создание ресурсов хранилища

Создайте учетную запись хранения, чтобы хранить выходные данные диагностики загрузки.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Создание сетевых ресурсов

Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. С помощью этих ресурсов вы сможете установить сетевое подключение к виртуальной машине.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Создайте группу безопасности сети и правило группы безопасности сети.

Группа безопасности сети защищает виртуальную машину с использованием правил для входящего и исходящего трафика. Давайте создадим правило входящего трафика для порта 3389, чтобы разрешить входящие подключения к удаленному рабочему столу, и правило входящего трафика для порта 80, чтобы разрешить входящий веб-трафик.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-vm"></a>Создание сетевого адаптера для виртуальной машины

С помощью сетевого адаптера можно подключить виртуальную машину к подсети, группе безопасности сети и общедоступному IP-адресу.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте конфигурацию виртуальной машины. Эта конфигурация содержит параметры, которые используются при развертывании виртуальной машины, например учетные данные, размер и образ виртуальной машины.

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Для удаленного доступа к виртуальной машине, созданной на предыдущем шаге, потребуется ее общедоступный IP-адрес. Выполните следующую команду, чтобы получить общедоступный IP-адрес виртуальной машины:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Выполните приведенную ниже команду, чтобы создать сеанс удаленного рабочего стола с виртуальной машиной. Замените указанный IP-адрес *общедоступным IP-адресом* своей виртуальной машины. При появлении запроса введите имя пользователя и пароль, которые использовались при создании виртуальной машины.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Установка IIS с помощью PowerShell

Войдя на виртуальную машину Azure, вы можете установить IIS и включить локальное правило брандмауэра, разрешающее веб-трафик, с помощью одной строки кода PowerShell. Откройте командную строку PowerShell и выполните следующую команду:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Просмотр страницы приветствия IIS

Установив IIS и открыв порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия IIS по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте значение *publicIpAddress*, записанное в предыдущем разделе.

![Сайт IIS по умолчанию](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>Удаление виртуальной машины

Если группа ресурсов, содержащая виртуальную машину и связанные с ней ресурсы, больше не нужна, выполните следующую команду для ее удаления:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководстве вы развернули простую виртуальную машину Windows. Дополнительные сведения о виртуальных машинах Azure Stack Hub см. в статье [Рекомендации по использованию виртуальных машин в Azure Stack Hub](azure-stack-vm-considerations.md).
