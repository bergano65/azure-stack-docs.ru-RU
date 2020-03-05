---
title: Создание виртуальной машины Linux с помощью PowerShell в Azure Stack Hub
description: Создание виртуальной машины Linux с помощью PowerShell в Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 5e0fb0ec59451874ce641006a626d8de82392f94
ms.sourcegitcommit: 390eac7abc94cea1405178e8d6a9358f6488f5d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78231627"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-powershell-in-azure-stack-hub"></a>Краткое руководство. Создание виртуальной машины с сервером Linux с помощью PowerShell в Azure Stack Hub

Вы можете создать виртуальную машину Ubuntu Server 16.04 LTS с помощью PowerShell в Azure Stack Hub. В этой статье описано, как создать и использовать виртуальную машину. Здесь также объясняется, как выполнить следующие задачи:

* подключиться к виртуальной машине через удаленный клиент;
* установить веб-сервер NGINX и открыть его стандартную домашнюю страницу;
* очистить неиспользуемые ресурсы.

## <a name="prerequisites"></a>Предварительные требования

* Образ Linux в Azure Stack Hub Marketplace. По умолчанию Azure Stack Hub Marketplace не содержит образа Linux. Обратитесь к оператору Azure Stack Hub, чтобы он предоставил нужный образ Ubuntu Server 16.04 LTS. Для этого оператор может выполнить инструкции из статьи [Скачивание элементов Marketplace из Azure в Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md).

* Для создания ресурсов и управления ими Azure Stack Hub требуется определенная версия Azure CLI. 
  * Если вы еще не настроили PowerShell для Azure Stack Hub, см. руководство по [установке PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md). 
  * Настроив PowerShell для Azure Stack Hub, подключитесь к среде Azure Stack Hub. Инструкции см. в статье [Подключение к Azure Stack Hub в роли пользователя с помощью PowerShell](azure-stack-powershell-configure-user.md).

* Открытый ключ SSH с именем *id_rsa.pub* сохраняется в каталоге (с расширением *.ssh*) вашего профиля пользователя Windows. Дополнительные сведения о создании ключей SSH см. в статье [Использование открытого ключа SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором вы можете развертывать ресурсы Azure Stack Hub и управлять ими. Выполните следующий блок кода, чтобы создать группу ресурсов. 

> [!NOTE]
> В приведенных ниже примерах кода всем переменным уже присвоены значения. Но вы можете присвоить собственные значения.

```powershell  
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Создание ресурсов хранилища

Создайте учетную запись хранения, которая будет использоваться для хранения выходных данных диагностики загрузки.

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

Группа безопасности сети защищает виртуальную машину с использованием правил для входящего и исходящего трафика. Создайте правило входящего трафика для порта 3389, чтобы разрешить входящие подключения к удаленному рабочему столу, и правило входящего трафика для порта 80, чтобы разрешить входящий веб-трафик.

```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
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

Создайте конфигурацию виртуальной машины. Эта конфигурация содержит параметры, которые используются при развертывании виртуальной машины (например, учетные данные пользователя, размер и образ виртуальной машины).

```powershell
# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Add the SSH key to the VM
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the VM
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="vm-quick-create-full-script"></a>Быстрое создание виртуальной машины. Полный сценарий

> [!NOTE]
> По сути, на этом шаге показано объединенное представление приведенного выше кода, но с использованием пароля, а не ключа SSH для аутентификации.

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack Hub resources. From your development kit or the Azure Stack Hub integrated system, run the following code block to create a resource group. Though we've assigned values for all the variables in this article, you can use these values or assign new ones.
#>

# Edit your variables, if required

# Create variables to store the location and resource group names
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for VM password
$VMPassword = 'Password123!'

# End of variables - no need to edit anything past that point to deploy a single VM

# Create a resource group
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account, and then create a storage container for the Ubuntu Server 16.04 LTS image

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the VM image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, a subnet, and a public IP address, resources that are used provide network connectivity to the VM

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


### Create a network security group and a network security group rule

<#
The network security group secures the VM by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the VM

# The network card connects the VM to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a VM
<#
Create a VM configuration. This configuration includes the settings used when deploying the VM. For example: user credentials, size, and the VM image.
#>

# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Развернув виртуальную машину, настройте подключение к ней по протоколу SSH. Чтобы получить общедоступный IP-адрес виртуальной машины, используйте команду [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress).

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Чтобы подключиться к виртуальной машине, в клиентской системе, в которой установлен протокол SSH, выполните приведенную ниже команду. Если вы работаете в Windows, можно использовать [PuTTY](https://www.putty.org/) для установки подключения.

```
ssh <Public IP Address>
```

Когда появится запрос, войдите в систему как **azureuser**. Если при создании ключей SSH вы указали парольную фразу, введите ее здесь.

## <a name="install-the-nginx-web-server"></a>Установка веб-сервера NGINX

Чтобы обновить источники пакетов и установить последнюю версию пакета NGINX, выполните следующий скрипт:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Просмотр страницы приветствия nginx

Теперь, когда на виртуальной машине установлен веб-сервер NGINX и открыт порт 80, вы можете обращаться к веб-серверу по общедоступному IP-адресу этой виртуальной машины. Откройте веб-браузер и перейдите на страницу ```http://<public IP address>```.

![Страница приветствия веб-сервера NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Ресурсы, которые вам больше не нужны, можно удалить, используя команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Чтобы удалить группу ресурсов и все ресурсы в ней, выполните следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы развернули простую виртуальную машину с сервером Linux. Дополнительные сведения о виртуальных машинах Azure Stack Hub см. в статье [Рекомендации по использованию виртуальных машин в Azure Stack Hub](azure-stack-vm-considerations.md).
