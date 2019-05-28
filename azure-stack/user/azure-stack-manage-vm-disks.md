---
title: Управление дисками виртуальной машины в Azure Stack | Документация Майкрософт
description: Создание дисков для виртуальных машин в Azure Stack.
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 10900095a79da2639212b5fa58becbafb7bee0eb
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941199"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>Создание хранилища для дисков виртуальных машин в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье описано создание хранилища для дисков виртуальной машины с помощью портала Azure Stack или PowerShell.

## <a name="overview"></a>Обзор

Начиная с версии 1808, Azure Stack поддерживает использование на виртуальных машинах управляемых и неуправляемых дисков в качестве дисков операционной системы и дисков данных. До выпуска версии 1808 поддерживались только неуправляемые диски.

[Управляемые диски](/azure/virtual-machines/windows/managed-disks-overview) упрощают управление дисками виртуальных машин Azure IaaS. Они управляют учетными записями хранения, связанными с этими дисками. Вам нужно только выбрать размер диска, а Azure Stack самостоятельно создаст диск и будет управлять им.

Для хранения неуправляемых дисков необходимо создать учетную запись хранения. Созданные диски называются дисками виртуальной машины и хранятся в контейнерах в учетной записи хранения.

### <a name="best-practice-guidelines"></a>Рекомендации

Мы рекомендуем размещать каждый диск виртуальной машины в отдельном контейнере, чтобы повысить производительность и снизить общие расходы. Контейнер должен содержать диск ОС или диск данных, но не оба одновременно. Но вы также можете поместить диски обоих типов в один контейнер.

Если вы добавляете на виртуальную машину один или нескольких дисков данных, создайте для этих дисков дополнительные контейнеры. Диски ОС для дополнительных виртуальных машин также лучше размещать в отдельных контейнерах.

Если вы создаете виртуальные машины, можно использовать для них одну и ту же учетную запись хранения. Только создаваемые контейнеры должны быть уникальными.

### <a name="adding-new-disks"></a>Добавление новых дисков

В приведенной ниже таблице указано, как добавлять диски с помощью портала и PowerShell.

| Метод | Параметры
|-|-|
|Пользовательский портал|— Добавление нового диска данных на существующую виртуальную машину. Эти диски создаются с помощью Azure Stack. </br> </br>— Добавление имеющегося диска в виде VHD-файла на заранее созданную виртуальную машину. Для этого необходимо подготовить VHD-файл и передать его в Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | — Создайте виртуальную машину с диском ОС и в то же время добавьте на нее один или несколько дисков данных. |

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

2. Выберите виртуальную машину, созданную ранее.
   ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Для виртуальной машины последовательно выберите **Диски** и **Add data disk** (Добавить диск с данными).
   ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Для диска с данными:
   * Введите **LUN**. Значение LUN должно быть допустимым числом.
   * Выберите **Создание диска**.
   ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. В колонке **Создание управляемого диска**:
   * Введите **имя** диска.
   * Выберите имеющуюся или создайте новую **группу ресурсов**.
   * Выберите **расположение**. По умолчанию для расположения установлен тот же контейнер, где размещен диск ОС.
   * Выберите **тип учетной записи**.
      ![Пример. Подключение нового диска к виртуальной машине](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **SSD (цен. категория "Премиум")**  
      Диски цен. категории "Премиум" поддерживаются твердотельными накопителями и обеспечивают стабильную производительность с низкой задержкой. Они обеспечивают оптимальное соотношение цены и производительности и идеально подходят для приложений с интенсивным вводом-выводом и для производственных рабочих нагрузок.

      **HDD (цен. категория "Стандартный")**  
      Диски цен. категории "Стандартный" поддерживаются магнитными дисками и предпочтительны для приложений, где доступ к данным осуществляется редко. Избыточные между зонами диски поддерживаются хранилищем, избыточным между зонами (ZRS), которое реплицирует ваши данные в несколько зон и доступно даже в том случае, если одна зона не работает.

   * Выберите **тип источника**.

     Создайте диск из моментального снимка другого диска, большого двоичного объекта в учетной записи хранения или создайте пустой диск.

      **Моментальный снимок.** Выберите моментальный снимок, если он доступен. Моментальный снимок должен быть доступен в подписке и расположении виртуальной машины.

      **Большой двоичный объект хранилища**:
     * Добавьте универсальный код ресурса (URI) для большого двоичного объекта хранилища, который содержит образ диска.  
     * Щелкните **Обзор**, чтобы открыть колонку учетных записей хранения. Инструкции см. в разделе [Добавление диска с данными из учетной записи хранения](#add-a-data-disk-from-a-storage-account).
     * Выберите тип ОС образа: **Windows**, **Linux** или **Нет (диск с данными)** .

   * Выберите **Размер (ГиБ)** .

     Стоимость диска цен. категории "Стандартный" увеличивается в зависимости от размера диска. Стоимость и производительность диска цен. категории "Премиум" увеличиваются в зависимости от размера диска. Дополнительные сведения см. в статье [Цены на управляемые диски](https://go.microsoft.com/fwlink/?linkid=843142).

   * Нажмите кнопку **Создать**. Azure Stack создает и проверяет управляемый диск.

6. После того как Azure Stack создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.

   ![Пример: Просмотр диска](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Добавление диска с данными из учетной записи хранения

Подробные сведения о работе с учетными записями хранения в Azure Stack см. в статье [Общие сведения о хранилище Azure Stack](azure-stack-storage-overview.md).

1. Выберите используемую **учетную запись хранения**.
2. Выберите **контейнер**, в который необходимо поместить диск данных. В колонке **Контейнеры** можно создать контейнер, если это необходимо. Затем можно изменить расположение нового диска на собственный контейнер. При использовании отдельного контейнера для каждого диска распространение расположения диска данных может повысить производительность.
3. Щелкните **Выбрать**, чтобы сохранить выбор.

    ![Пример: Выбор контейнера](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Подключение существующего диска данных к виртуальной машине

1. [Подготовьте VHD-файл](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd), который будет использоваться в качестве диска данных для виртуальной машины. Отправьте этот VHD-файл в учетную запись хранения для использования с виртуальной машиной, к которой нужно подключить этот VHD-файл.

    Запланируйте размещение VHD-файла в контейнере, отличном от того, в котором хранится диск ОС.
    ![Пример. Отправка VHD-файла](media/azure-stack-manage-vm-disks/upload-vhd.png)

2. После отправки VHD-файла можно подключить VHD к виртуальной машине. В меню слева выберите **Виртуальные машины**.  
 ![Пример. Выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Выберите виртуальную машину из списка.

    ![Пример: выбор виртуальной машины на панели мониторинга](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. На странице виртуальной машины выберите **Диски** и **Подключить существующий**.

    ![Пример: Подключение существующего диска](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. На странице **Подключение существующего диска** выберите **VHD-файл**. Откроется страница **Учетные записи хранения**.

    ![Пример: Выбор VHD-файла](media/azure-stack-manage-vm-disks/select-vhd.png)

6. В разделе **Учетные записи хранения** выберите нужную учетную запись. Затем выберите контейнер, в который ранее передали VHD-файл. Выберите нужный VHD-файл и щелкните **Выбрать**, чтобы сохранить выбор.

    ![Пример: Выбор контейнера](media/azure-stack-manage-vm-disks/select-container2.png)

7. В разделе **Подключение существующего диска** выбранный файл находится в списке **VHD-файл**. Измените для диска параметр **Кэширование узла** и щелкните **ОК**, чтобы сохранить новую конфигурацию диска для виртуальной машины.

    ![Пример: Подключение VHD-файла](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. После того как Azure Stack создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.

    ![Пример: Завершение подключения диска](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Добавление нескольких неуправляемых дисков к виртуальной машине с помощью PowerShell

Вы можете использовать PowerShell для подготовки виртуальной машины и добавления новых дисков данных или подключения уже имеющихся **VHD-файлов** в качестве дисков данных.

Командлет **Add-AzureRmVMDataDisk** добавляет диск данных к виртуальной машине. Диск данных можно добавить к новой виртуальной машине при ее создании или к имеющейся виртуальной машине. Укажите параметр **VhdUri**, чтобы распределить диски в разные контейнеры.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Добавление дисков данных в новую виртуальную машину

В приведенных ниже примерах используются команды PowerShell для создания виртуальной машины с тремя дисками данных, размещенными в различных контейнерах.

Первая команда создает объект виртуальной машины и сохраняет его в переменной `$VirtualMachine`. Команда назначает виртуальной машине имя и размер:

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
```

Следующие три команды назначают пути трех дисков данных переменным `$DataDiskVhdUri01`, `$DataDiskVhdUri02`и `$DataDiskVhdUri03`. Задайте разные имена путей в URL-адресе для распределения дисков в различных контейнерах:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Три заключительные команды добавляют диски данных в виртуальную машину, заданную в переменной `$VirtualMachine`. Каждая команда указывает имя, расположение и дополнительные свойства диска. Универсальные коды ресурсов (URI) каждого диска хранятся в переменных `$DataDiskVhdUri01`, `$DataDiskVhdUri02` и `$DataDiskVhdUri03`:

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

Используйте следующие команды PowerShell, чтобы добавить конфигурацию диска ОС и сети в виртуальную машину, а затем запустите новую виртуальную машину:

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

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
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Добавление дисков данных к имеющейся виртуальной машине

В следующих примерах используются команды PowerShell для добавления трех дисков данных в имеющуюся виртуальную машину. Первая команда получает виртуальную машину с именем **VirtualMachine** с помощью командлета **Get-AzureRmVM**. Она сохраняет виртуальную машину в переменной `$VirtualMachine`:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

Следующие три команды назначают пути трех дисков данных переменным `$DataDiskVhdUri01`, `$DataDiskVhdUri02`и `$DataDiskVhdUri03`. Разные имена путей в универсальных кодах ресурсов (URI) VHD указывают различные контейнеры для размещения диска:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Следующие три команды добавляют диски данных к виртуальной машине, заданной в переменной `$VirtualMachine`. Каждая команда указывает имя, расположение и дополнительные свойства диска. Универсальные коды ресурсов (URI) каждого диска хранятся в переменных `$DataDiskVhdUri01`, `$DataDiskVhdUri02` и `$DataDiskVhdUri03`:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

Последняя команда обновляет состояние виртуальной машины, заданной в переменной `$VirtualMachine` в `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о виртуальных машинах Azure Stack см. в [этих рекомендациях](azure-stack-vm-considerations.md).
