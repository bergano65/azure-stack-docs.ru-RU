---
title: Различия между управляемыми дисками и управляемыми образами в Azure Stack и рекомендации по работе с ними | Документация Майкрософт
description: Дополнительные сведения о различиях и рекомендациях при работе с управляемыми дисками и управляемыми образами в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: d32d7d8d484d6e9819440fc9261bae52d6395ae2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985899"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Управляемые диски Azure Stack. Различия и рекомендации

В этой статье перечислены известные различия между [управляемыми дисками Azure Stack](azure-stack-manage-vm-disks.md) и [управляемыми дисками для Azure](/azure/virtual-machines/windows/managed-disks-overview). Чтобы узнать об общих различиях между Azure Stack и Azure, прочитайте статью [Важные аспекты использования служб и создания приложений в Azure Stack](azure-stack-considerations.md).

Управляемые диски упрощают управление дисками виртуальных машин IaaS. Они управляют [учетными записями хранения](../operator/azure-stack-manage-storage-accounts.md), связанными с этими дисками.

> [!Note]  
> Управляемые диски в Azure Stack доступны, начиная с обновления 1808. Они теперь включены по умолчанию при создании виртуальных машин с помощью портала Azure Stack, начиная с обновления 1811.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Памятка. Различия между управляемыми дисками

| Функция | Azure (глобальная) | Azure Stack |
| --- | --- | --- |
|Шифрование неактивных данных |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE) (Шифрование службы хранилища Azure и шифрование дисков Azure)     |128-битное шифрование AES BitLocker      |
|Образ —          | Поддержка управляемого пользовательского образа |Поддерживаются|
|Варианты резервного копирования |Поддержка службы Azure Backup |Еще не поддерживается |
|Параметры аварийного восстановления |Поддержка Azure Site Recovery |Еще не поддерживается|
|Типы дисков     |SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") и HDD (цен. категория "Стандартный") |SSD (цен. категория "Премиум"), SSD (цен. категория "Стандартный") |
|Диски уровня "Премиум"  |Полностью поддерживается |Может быть подготовлено, но не имеет ограничений производительности или гарантий  |
|Операции ввода-вывода дисков уровня "Премиум"  |Зависит от размера диска  |2300 операций ввода-вывода в секунду на диск |
|Пропускная способность дисков уровня "Премиум" |Зависит от размера диска |145 МБ/с на диск |
|Размер диска  |Диски Azure ценовой категории "Премиум": от P4 (32 ГиБ) до P80 (32 ТиБ)<br>Диски SSD Azure ценовой категории "Стандартный": от E10 (128 ГиБ) до E80 (32 ТиБ)<br>Диски HDD Azure ценовой категории "Стандартный": от S4 (32 ГиБ) до S80 (32 ТиБ) |M4: 32 Гиб<br>M6: 64 Гиб<br>M10: 128 ГБ<br>M15: 256 Гиб<br>M20: 512 ГБ<br>M30: 1024 ГиБ |
|Моментальные снимки дисков|Поддерживается создание моментальных снимков управляемых дисков Azure, подключенных к запущенной виртуальной машине|Еще не поддерживается |
|Производительность аналитики дисков |Поддерживаются статистические и дисковые метрики |Еще не поддерживается |
|Миграция      |Предоставляется средство для переноса из существующих неуправляемых виртуальных машин Azure Resource Manager без необходимости повторного создания виртуальной машины  |Еще не поддерживается |

> [!NOTE]  
> Число операций ввода-вывода в управляемых дисках и объем пропускной способности в Azure Stack зависят от ограничений, а не задаются в ходе подготовки и могут обуславливаться аппаратным обеспечением и рабочими нагрузками, выполняющимися в Azure Stack.

## <a name="metrics"></a>Метрики

Кроме того, есть различия в метриках хранилища:

- С помощью Azure Stack данные транзакций в метриках хранилища не различают внутреннюю или внешнюю пропускную способность сети.
- Данные транзакции Azure Stack в метриках хранилища не включают доступ виртуальных машин к подключенным дискам.

## <a name="api-versions"></a>Версии API

Управляемые диски Azure Stack поддерживают следующие версии API:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Преобразование виртуальной машины для использования управляемых дисков

С помощью приведенного ниже сценария можно переключить заранее подготовленную виртуальную машину с неуправляемых дисков на управляемые. Замените значения заполнителей на собственные.

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Управляемые образы

Azure Stack поддерживает *управляемые образы*, которые позволяют создавать объект управляемого образа на универсальной виртуальной машине (как неуправляемой, так и управляемой), которая может создавать только виртуальные машины управляемого диска в будущем. Управляемые образы включают следующие два сценария.

- Вы обобщили неуправляемые виртуальные машины и хотите использовать управляемые диски в будущем.
- У вас есть универсальная управляемая виртуальная машина и вы хотите создать несколько похожих управляемых виртуальных машин.

### <a name="step-1-generalize-the-vm"></a>Шаг 1. Подготовка виртуальной машины к использованию

Для Windows: см. раздел [Generalize the Windows VM using Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) (Подготовка виртуальной машины Windows к использованию с помощью Sysprep). Для Linux: выполните шаг 1, описанный [здесь](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Обязательно подготовьте виртуальную машину к использованию. Создание виртуальной машины из образа, не подготовленного должным образом, приведет к возникновению ошибки **VMProvisioningTimeout**.

### <a name="step-2-create-the-managed-image"></a>Шаг 2. Создание управляемого образа

Создать управляемый образ можно с помощью портала, PowerShell или интерфейса командной строки. Следуйте инструкциям, приведенным в [этой статье](/azure/virtual-machines/windows/capture-image-resource) по Azure.

### <a name="step-3-choose-the-use-case"></a>Шаг 3. Выбор варианта использования

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Вариант 1. Миграция неуправляемых виртуальных машин на управляемые диски

Перед выполнением этого шага необходимо правильно подготовить виртуальную машину к использованию. После подготовки вы больше не сможете использовать эту виртуальную машину. Создание виртуальной машины из образа, не подготовленного должным образом, приведет к возникновению ошибки **VMProvisioningTimeout**.

Следуйте инструкциям, описанным [здесь](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account), чтобы создать управляемый образ с помощью универсального виртуального жесткого диска в учетной записи хранения. Этот образ можно использовать в дальнейшем для создания управляемых виртуальных машин.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Вариант 2. Создание управляемой виртуальной машины из управляемого образа с помощью PowerShell

После создания образа на основе существующей виртуальной машины с управляемым диском с помощью скрипта, указанного [здесь](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), используйте следующий пример скрипта, чтобы создать аналогичную виртуальную машину Linux на основе существующего объекта образа.

Для модуля Azure Stack PowerShell 1.7.0 или более поздней версии: следуйте инструкциям, приведенным [здесь](/azure/virtual-machines/windows/create-vm-generalized-managed).

Для модуля Azure Stack PowerShell 1.6.0 или более ранней версии:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Также можно создать виртуальную машину на основе управляемого образа с помощью портала. Дополнительные сведения см. в статьях управляемого образа Azure [Создание управляемого образа универсальной виртуальной машины в Azure](/azure/virtual-machines/windows/capture-image-resource) и [Создание виртуальной машины из управляемого образа](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Параметр Configuration

После применения обновления 1808 или более поздней версии необходимо выполнить следующую конфигурацию, прежде чем использовать управляемые диски.

- Если подписка была создана до пакета обновления версии 1808, выполните следующие шаги, чтобы обновить подписку. В противном случае во время развертывания виртуальных машин для этой подписки может произойти сбой с сообщением об ошибке "Internal error in disk manager" (В диспетчере дисков произошла внутренняя ошибка).
   1. На портале клиента перейдите в раздел **Подписки** и найдите подписку. Щелкните **Поставщики ресурсов**, затем **Microsoft.Compute** и выберите **Повторная регистрация**.
   2. В той же подписке перейдите в раздел **Управление доступом (IAM)** и убедитесь, что указан **Azure Stack — Управляемый диск**.
- Если вы используете среду с несколькими клиентами, попросите вашего оператора облака (из вашей организации или из компании поставщика служб) перенастроить каждый ваш гостевой каталог, выполнив шаги из [этой статьи](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). В противном случае, во время развертывания виртуальных машин в этой подписке, связанной с гостевым каталогом, может произойти сбой с сообщением об ошибке "В диспетчере дисков произошла внутренняя ошибка".

## <a name="next-steps"></a>Дополнительная информация

- [Сведения о виртуальных машинах Azure Stack](azure-stack-compute-overview.md)
