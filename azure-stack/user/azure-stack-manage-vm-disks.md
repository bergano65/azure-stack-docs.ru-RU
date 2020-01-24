---
title: Создание хранилища дисков виртуальной машины в Azure Stack Hub | Документация Майкрософт
description: Создание дисков для виртуальных машин в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 66147be9158726ab9ba01d011ba0fa2fd8f141bc
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883513"
---
# <a name="create-vm-disk-storage-in-azure-stack-hub"></a>Создание хранилища дисков виртуальной машины в Azure Stack Hub

В этой статье описано создание хранилища для дисков виртуальной машины с помощью портала Azure Stack Hub или PowerShell.

## <a name="overview"></a>Обзор

Начиная с версии 1808, Azure Stack Hub поддерживает использование управляемых и неуправляемых дисков в виртуальных машинах в качестве дисков операционной системы и дисков данных. До выпуска версии 1808 поддерживались только неуправляемые диски.

[Управляемые диски](/azure/virtual-machines/windows/managed-disks-overview) упрощают управление дисками виртуальных машин Azure IaaS. Они управляют учетными записями хранения, связанными с этими дисками. Вам нужно только выбрать размер диска, а Azure Stack Hub самостоятельно создаст диск и будет управлять им.

Для хранения неуправляемых дисков необходимо создать учетную запись хранения. Созданные диски называются дисками виртуальной машины и хранятся в контейнерах в учетной записи хранения.

## <a name="best-practice-guidelines"></a>Рекомендации

Рекомендуется использовать управляемые диски для виртуальной машины, чтобы упростить управление и балансировку емкости. Перед использованием управляемых дисков вам не нужно подготавливать учетную запись хранения и контейнеры. При создании нескольких управляемых дисков диски распределяются по нескольким томам, что помогает сбалансировать емкость томов.  

Для неуправляемых дисков рекомендуется размещать каждый диск виртуальной машины в отдельном контейнере, чтобы повысить производительность и снизить общие расходы. Несмотря на то, что в одном контейнере можно разместить диски операционной системы и диски данных, рекомендуется, чтобы один контейнер содержал либо диск операционной системы, либо диск данных, но не оба одновременно.

Если вы добавляете на виртуальную машину один или нескольких дисков данных, создайте для этих дисков дополнительные контейнеры. Диски ОС для дополнительных виртуальных машин также лучше размещать в отдельных контейнерах.

Если вы создаете виртуальные машины, можно использовать для них одну и ту же учетную запись хранения. Только создаваемые контейнеры должны быть уникальными.

## <a name="adding-new-disks"></a>Добавление новых дисков

В приведенной ниже таблице указано, как добавлять диски с помощью портала и PowerShell.

| Метод | Параметры
|-|-|
|Пользовательский портал|— Добавление нового диска данных на существующую виртуальную машину. Эти диски создаются с помощью Azure Stack Hub. </br> </br> — Добавление имеющегося диска в виде VHD-файла на заранее созданную виртуальную машину. Для этого необходимо подготовить VHD-файл и передать его в Azure Stack Hub. |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | — Создайте виртуальную машину с диском ОС и в то же время добавьте на нее один или несколько дисков данных. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Добавление дисков на виртуальную машину с помощью портала

Если для создания виртуальной машины используется портал, по умолчанию для большинства элементов Marketplace создается только диск ОС.

После создания виртуальной машины вы можете выполнить на портале следующие операции:

* создать диск данных и подключить его к виртуальной машине;
* передать существующий диск данных и подключить его к виртуальной машине.

Каждый неуправляемый диск следует размещать в отдельном контейнере.

>[!NOTE]  
>Диски, созданные и управляемые в Azure, называются [управляемыми дисками](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Создание и подключение нового диска данных с помощью портала

1. На портале выберите **Все службы** и **Виртуальные машины**.
   ![Пример. Панель мониторинга виртуальных машин](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Выберите виртуальную машину, созданную ранее.
   ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Для виртуальной машины последовательно выберите **Диски** и **Add data disk** (Добавить диск с данными).
   ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Для диска с данными:
   * Введите **LUN**. Значение LUN должно быть допустимым числом.
   * Выберите **Создание диска**.
   ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. В колонке **Создание управляемого диска**:
   * Введите **имя** диска.
   * Выберите существующую **Группу ресурсов** или создайте новую.
   * Выберите **расположение**. По умолчанию для расположения установлен тот же контейнер, где размещен диск ОС.
   * Выберите **тип учетной записи**.
      ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **SSD (цен. категория "Премиум")**  
      Диски цен. категории "Премиум" поддерживаются твердотельными накопителями и обеспечивают стабильную производительность с низкой задержкой. Они обеспечивают оптимальное соотношение цены и производительности и идеально подходят для приложений с интенсивным вводом-выводом и для производственных рабочих нагрузок.

      **HDD (цен. категория "Стандартный")**  
      Диски цен. категории "Стандартный" поддерживаются магнитными дисками и предпочтительны для приложений, где доступ к данным осуществляется редко. Избыточные между зонами диски поддерживаются хранилищем, избыточным между зонами (ZRS), которое реплицирует ваши данные в несколько зон, обеспечивая доступность ваших данных даже в том случае, если одна зона не работает.

   * Выберите **тип источника**.

     Создайте диск из моментального снимка другого диска, большого двоичного объекта в учетной записи хранения или создайте пустой диск.

      **Моментальный снимок.** Выберите моментальный снимок, если он доступен. Моментальный снимок должен быть доступен в подписке и расположении виртуальной машины.

      **Большой двоичный объект хранилища**:
     * Добавьте универсальный код ресурса (URI) для большого двоичного объекта хранилища, который содержит образ диска.  
     * Щелкните **Просмотреть**, чтобы открыть колонку учетных записей хранения. Инструкции см. в разделе [Добавление диска с данными из учетной записи хранения](#add-a-data-disk-from-a-storage-account).
     * Выберите тип операционной системы изображения: **Windows**, **Linux** или **нет (диск с данными)** .

   * Выберите **Размер (ГиБ)** .

     Стоимость диска цен. категории "Стандартный" увеличивается в зависимости от размера диска. Стоимость и производительность диска цен. категории "Премиум" увеличиваются в зависимости от размера диска. Дополнительные сведения см. в статье [Цены на управляемые диски](https://go.microsoft.com/fwlink/?linkid=843142).

   * Нажмите кнопку **Создать**. Azure Stack Hub создает и проверяет управляемый диск.

6. После того как Azure Stack Hub создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.

   ![Пример Просмотр диска](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Добавление диска с данными из учетной записи хранения

Подробные сведения о работе с учетными записями хранения в Azure Stack Hub см. в статье [Общие сведения о хранилище Azure Stack Hub](azure-stack-storage-overview.md).

1. Выберите используемую **учетную запись хранения**.
2. Выберите **контейнер**, в который необходимо поместить диск данных. В колонке **Контейнеры** можно создать контейнер, если это необходимо. Затем можно изменить расположение нового диска на собственный контейнер. При использовании отдельного контейнера для каждого диска распространение расположения диска данных может повысить производительность.
3. Щелкните **Выбрать**, чтобы сохранить выбор.

    ![Пример Выбор контейнера](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Подключение существующего диска данных к виртуальной машине

1. [Подготовьте VHD-файл](/azure/virtual-machines/windows/classic/createupload-vhd), который будет использоваться в качестве диска данных для виртуальной машины. Отправьте этот VHD-файл в учетную запись хранения для использования с виртуальной машиной, к которой нужно подключить этот VHD-файл.

    - Запланируйте размещение VHD-файла в контейнере, отличном от того, в котором хранится диск ОС.  
    - Прежде чем передавать в Azure какой-либо виртуальный жесткий диск, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
    - Прежде чем начать миграцию на [Управляемые диски](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview), внимательно изучите [этот раздел](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks).
    
    ![Пример Отправка VHD-файла](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. После отправки VHD-файла можно подключить VHD к виртуальной машине. В меню слева выберите **Виртуальные машины**.  
 ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Выберите виртуальную машину из списка.

    ![Пример выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. На странице виртуальной машины выберите **Диски** и **Подключить существующий**.

    ![Пример Подключение существующего диска](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. На странице **Подключение существующего диска** выберите **VHD-файл**. Откроется страница **Учетные записи хранения**.

    ![Пример Выбор VHD-файла](media/azure-stack-manage-vm-disks/select-vhd.png)

6. В разделе **Учетные записи хранения** выберите нужную учетную запись. Затем выберите контейнер, в который ранее передали VHD-файл. Выберите нужный VHD-файл и щелкните **Выбрать**, чтобы сохранить выбор.

    ![Пример Выбор контейнера](media/azure-stack-manage-vm-disks/select-container2.png)

7. В разделе **Подключение существующего диска** выбранный файл находится в списке **VHD-файл**. Измените для диска параметр **Кэширование узла** и щелкните **ОК**, чтобы сохранить новую конфигурацию диска для виртуальной машины.

    ![Пример Подключение VHD-файла](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. После того как Azure Stack Hub создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.

    ![Пример Завершение подключения диска](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>Добавление нескольких дисков к виртуальной машине с помощью PowerShell

С помощью PowerShell вы можете подготовить виртуальную машину и добавить новые диски данных или подключить имеющийся управляемый диск или VHD-файл в качестве диска данных.

Командлет **Add-AzureRmVMDataDisk** добавляет диск данных к виртуальной машине. Диск данных можно добавить к новой виртуальной машине при ее создании или к имеющейся виртуальной машине. Для неуправляемого диска укажите параметр **VhdUri**, чтобы распределить диски в разные контейнеры.

### <a name="add-data-disks-to-a-new-vm"></a>Добавление дисков данных в **новую** виртуальную машину

В следующих примерах используются команды PowerShell для создания виртуальной машины с тремя дисками данных. Команды предоставляются частями из-за незначительных различий при использовании управляемых и неуправляемых дисков. 

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>Создание конфигурации виртуальной машины и сетевых ресурсов

Следующий скрипт создает объект виртуальной машины и сохраняет его в переменной `$VirtualMachine`. Команды присваивают имя и размер виртуальной машине, а затем создают сетевые ресурсы (виртуальную сеть, подсеть, виртуальный сетевой адаптер, NSG и общедоступный IP-адрес) для виртуальной машины:

```powershell
# Create new virtual machine configuration
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"

# Set variables
$rgName = "myResourceGroup"
$location = "local"

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
                                  -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
                                  -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
                                                -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
                                                -SourceAddressPrefix Internet -SourcePortRange * `
                                                -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
                                       -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
                                   -Location $location -SubnetId $vnet.Subnets[0].Id `
                                   -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

```

#### <a name="add-managed-disk"></a>Добавление управляемых дисков
>[!NOTE]  
>Этот раздел предназначен только для добавления управляемых дисков. 

Следующие три команды добавляют управляемые диски данных в виртуальную машину, заданную в переменной `$VirtualMachine`. Каждая команда указывает имя и дополнительные свойства диска:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -CreateOption Empty
```

Следующая команда добавляет диск ОС в качестве управляемого диска к виртуальной машине, заданной в переменной `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disk"></a>Добавление неуправляемого диска

>[!NOTE]  
>Этот раздел предназначен только для добавления неуправляемых дисков. 

Следующие три команды назначают пути трех неуправляемых дисков данных переменным `$DataDiskVhdUri01`, `$DataDiskVhdUri02`и `$DataDiskVhdUri03`. Задайте разные имена путей в URL-адресе для распределения дисков в различных контейнерах:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Следующие три команды добавляют диски данных в виртуальную машину, заданную в переменной `$VirtualMachine`. Каждая команда указывает имя и дополнительные свойства диска. Универсальные коды ресурсов (URI) каждого диска хранятся в переменных `$DataDiskVhdUri01`, `$DataDiskVhdUri02` и `$DataDiskVhdUri03`:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Следующие команды добавляют неуправляемый диск ОС к виртуальной машине, заданной в переменной `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```


#### <a name="create-new-virtual-machine"></a>Создание виртуальной машины
Используйте следующие команды PowerShell, чтобы настроить образ ОС, добавить сетевую конфигурацию к виртуальной машине, а затем запустите новую виртуальную машину.

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```


### <a name="add-data-disks-to-an-existing-vm"></a>Добавление дисков данных на **имеющуюся** виртуальную машину
В следующих примерах используются команды PowerShell для добавления трех дисков данных в имеющуюся виртуальную машину.

#### <a name="get-virtual-machine"></a>Получение виртуальной машины

 Первая команда получает виртуальную машину с именем **VirtualMachine** с помощью командлета **Get-AzureRmVM**. Она сохраняет виртуальную машину в переменной `$VirtualMachine`:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>Добавление управляемых дисков

>[!NOTE]  
>Этот раздел предназначен только для добавления управляемых дисков.

Следующие три команды добавляют управляемые диски данных к виртуальной машине, заданной в переменной `$VirtualMachine`. Каждая команда указывает имя и дополнительные свойства диска:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" -Lun 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" -Lun 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" -Lun 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="add-unmanaged-disk"></a>Добавление неуправляемого диска

>[!NOTE]  
>Этот раздел предназначен только для добавления неуправляемых дисков. 

Следующие три команды назначают пути для трех дисков данных переменным `$DataDiskVhdUri01`, `$DataDiskVhdUri02`и `$DataDiskVhdUri03`. Разные имена путей в универсальных кодах ресурсов (URI) VHD указывают различные контейнеры для размещения диска:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Следующие три команды добавляют диски данных к виртуальной машине, заданной в переменной `$VirtualMachine`. Каждая команда указывает имя, расположение и дополнительные свойства диска. Универсальные коды ресурсов (URI) каждого диска хранятся в переменных `$DataDiskVhdUri01`, `$DataDiskVhdUri02` и `$DataDiskVhdUri03`:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="update-virtual-machine-state"></a>Обновление состояния виртуальной машины

Эта команда обновляет состояние виртуальной машины, заданной в переменной `$VirtualMachine` в `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о виртуальных машинах Azure Stack Hub см. в статье [Возможности виртуальных машин Azure Stack Hub](azure-stack-vm-considerations.md).
