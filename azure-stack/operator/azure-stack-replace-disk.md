---
title: Добавление физического диска
titleSuffix: Azure Stack Hub
description: Узнайте, как заменить физический диск в Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 12/02/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 9b94a094440b6c84d95500b23074a5fec12727e5
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491940"
---
# <a name="replace-a-physical-disk-in-azure-stack-hub"></a>Замена физического диска в Azure Stack Hub

В этой статье описывается общий процесс замены физического диска в Azure Stack Hub. Если физический диск выйдет из строя, его следует как можно скорее заменить.

> [!Note]  
> При замене физического диска данных **не** требуется переводить узел единиц масштабирования в режим обслуживания (остановка) заранее. Кроме того, после замены физического диска узел единицы масштабирования не нужно восстанавливать с помощью портала администратора Azure Stack Hub. В следующей статье содержатся дополнительные сведения о [замене аппаратного компонента на узле единиц масштабирования Azure Stack Hub](azure-stack-replace-component.md).

Эту процедуру можно использовать для развертываний, содержащих диски с возможностью оперативной замены.

Фактические шаги по замене диска будут варьироваться в зависимости от поставщика изготовителя оборудования (OEM). Подробные инструкции, относящиеся к вашей системе, см. в документации поставщика по элементам, заменяемым в условиях эксплуатации (FRU).

## <a name="review-disk-alert-information"></a>Просмотр сведений оповещения о диске
Когда диск выходит из строя, вы получаете оповещение о том, что соединение с физическим диском потеряно.

![Оповещение, показывающее потерянное соединение с физическим диском в разделе администрирования Azure Stack Hub](media/azure-stack-replace-disk/DiskAlert.png)

Если открыть оповещение, в его описании будет содержаться узел единицы масштабирования и точное расположение физического слота диска, который необходимо заменить. Azure Stack Hub дополнительно помогает идентифицировать неисправный диск, используя возможности светодиодного индикатора.

## <a name="replace-the-physical-disk"></a>Замена физического диска

Следуйте инструкциям по FRU своего поставщика OEM для фактической замены диска.

> [!note]
> Заменяйте диски для одного узла единицы масштабирования за раз. Дождитесь завершения работ по восстановлению виртуальных дисков, прежде чем перейти к следующему узлу единицы масштабирования.

Чтобы предотвратить использование неподдерживаемого диска в интегрированной системе, система блокирует диски, которые не поддерживаются вашим поставщиком. Если вы пытаетесь использовать неподдерживаемый диск, новое предупреждение сообщит вам, что диск был помещен на карантин из-за неподдерживаемой модели или встроенного ПО.

После замены диска Azure Stack Hub автоматически обнаруживает новый диск и начинает процесс восстановления виртуального диска.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell"></a>Проверка состояния восстановления виртуального диска с помощью Azure Stack Hub PowerShell

После замены диска можно отслеживать состояние работоспособности виртуального диска и ход выполнения задания восстановления с помощью Azure Stack Hub PowerShell.

1. Для этого нужно установить Azure Stack Hub PowerShell. Дополнительные сведения об установке PowerShell для Azure Stack Hub см. в [этой статье](azure-stack-powershell-install.md).
2. Подключитесь к Azure Stack Hub с помощью PowerShell в роли оператора. Ознакомьтесь с дополнительными сведениями о [подключении к Azure Stack Hub с помощью PowerShell в качестве оператора](azure-stack-powershell-configure-admin.md).
3. Выполните следующие командлеты, чтобы проверить состояние работоспособности и восстановления виртуального диска:

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Работоспособность томов Azure Stack Hub в PowerShell](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Проверьте состояние системы Azure Stack Hub. Дополнительные сведения см. в статье о [проверке состояния системы Azure Stack Hub](azure-stack-diagnostic-test.md).
5. При необходимости можно выполнить следующую команду, чтобы проверить состояние замененного физического диска.

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

    Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
    ```

    ![Физические диски, замененные в Azure Stack Hub с помощью PowerShell](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Проверка состояния восстановления виртуального диска с помощью привилегированной конечной точки

После замены диска можно отслеживать состояние работоспособности виртуального диска и ход выполнения задания восстановления с помощью привилегированной конечной точки. Выполните следующие шаги с любого компьютера с сетевым подключением к привилегированной конечной точке.

1. Откройте сеанс Windows PowerShell и подключитесь к привилегированной конечной точке.

    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```
  
2. Выполните следующую команду, чтобы просмотреть состояние работоспособности виртуального диска:

    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```

   ![Выходные данные команды PowerShell Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Чтобы просмотреть текущее состояние задания хранилища, выполните следующую команду:

    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```

    ![Выходные данные команды PowerShell Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Проверьте состояние системы Azure Stack Hub. Дополнительные сведения см. в статье о [проверке состояния системы Azure Stack Hub](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Устранение неполадок с виртуальным диском с помощью привилегированной конечной точки

Если задание восстановления виртуального диска не отвечает, выполните следующую команду, чтобы перезапустить задание:

```powershell
Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
```
