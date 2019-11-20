---
title: Подготовка виртуальной машины на основе Red Hat для Azure Stack | Документация Майкрософт
titleSuffix: Azure Stack
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD) Azure, содержащий операционную систему RedHat Linux.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: d8b986dede7e55cb0418219fce6ac78673eeff60
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802287"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Подготовка виртуальной машины на основе Red Hat для Azure Stack

В этой статье вы узнаете, как подготовить виртуальную машину Red Hat Enterprise Linux (RHEL) для использования в Azure Stack. В статье рассматривается RHEL 7.1 и более поздние версии. Низкоуровневые оболочки для подготовки, о которых идет речь в этой статье, — это Hyper-V, Kernel-based Virtual Machine (KVM) и VMware.

Сведения о поддержке Red Hat Enterprise Linux приведены в статье [Red Hat и Azure Stack: Red Hat и Azure Stack](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Подготовка виртуальной машины под управлением Red Hat в диспетчере Hyper-V

В этом разделе предполагается, что вы уже получили ISO-файл с веб-сайта Red Hat и установили образ RHEL на виртуальный жесткий диск. Дополнительные сведения о том, как использовать диспетчер Hyper-V для установки образа операционной системы, см. в статье об [установке Hyper-V и настройке виртуальной машины](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Замечания по установке RHEL

* Формат VHDX не поддерживается в Azure Stack. В Azure поддерживается только формат фиксированного виртуального жесткого диска. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd. Если вы используете VirtualBox, при создании диска выберите **фиксированный размер** вместо динамически выделяемого (по умолчанию).
* В Azure Stack поддерживаются только виртуальные машины поколения 1. Виртуальную машину первого поколения можно преобразовать из формата VHDX в формат VHD, а также переключить с динамически расширяемого диска на диск фиксированного размера. Вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. в статье о том, [какое поколение (1 или 2) выбрать для виртуальной машины в Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.
* При установке операционной системы Linux рекомендуется использовать стандартные разделы, а не диспетчер логических томов (LVM), который часто используется по умолчанию при установке. Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если нужно подключить диск с OC к другой идентичной виртуальной машине в целях устранения неполадок.
* Требуется поддержка ядра для подключения файловых систем UDF. При первой загрузке UDF-носитель, подключенный к гостевой машине, передает конфигурацию подготовки на виртуальную машину Linux. Агент Azure Linux должен подключить файловую систему UDF для считывания конфигурации и подготовки виртуальной машины.
* Не настраивайте раздел swap на диске операционной системы. Вы можете настроить агент Linux для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны ниже.
* Размер виртуальной памяти всех виртуальных жестких дисков в Azure должен быть округлен до 1 МБ. Перед конвертацией диска RAW в формат VHD убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. Дополнительные сведения можно найти в инструкциях ниже.
* В Azure Stack не поддерживается cloud-init. На виртуальной машине должна быть настроена поддерживаемая версия агента Windows Azure Linux (WALA).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>Подготовка виртуальной машины RHEL 7 с помощью диспетчера Hyper-V

1. В диспетчере Hyper-V выберите виртуальную машину.

1. Выберите **Подключение**, чтобы открыть окно консоли для виртуальной машины.

1. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст в соответствии с потребностями:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

    ```bash
    sudo systemctl enable network
    ```

1. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Это изменение гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь службе поддержки Azure при отладке. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт.

   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера. Мы рекомендуем удалить следующие параметры:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Остановка и удаление cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки (что обычно задано по умолчанию). Измените файл `/etc/ssh/sshd_config` , добавив в него следующую строку:

    ```sh
    ClientAliveInterval 180
    ```

1. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Установите агент Linux для Azure, выполнив следующую команду:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Локальный диск ресурсов является временным и может быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Чтобы отменить регистрацию подписки, выполните следующую команду:

    ```bash
    sudo subscription-manager unregister
    ```

1. Если вы используете систему, развернутую с помощью центра сертификации предприятия, виртуальная машина RHEL не будет доверять корневому сертификату Azure Stack. Необходимо поместить его в доверенное корневое хранилище. Дополнительные сведения см. в разделе о [добавлении доверенных корневых сертификатов на сервер](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. В диспетчере Hyper-V выберите **Действие** > **Завершение работы**.

1. Преобразуйте виртуальный жесткий диск в VHD фиксированного размера с помощью либо функции "Изменить диск" диспетчера Hyper-V, либо команды Convert-VHD PowerShell. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Подготовка виртуальной машины под управлением Red Hat в KVM

1. Скачайте образ KVM RHEL 7 с веб-сайта Red Hat. В примере используется RHEL 7.

1. Задайте пароль пользователя root.

    Создайте зашифрованный пароль и скопируйте результат выполнения команды:

    ```bash
    openssl passwd -1 changeme
    ```

   Установите корневой пароль с помощью Guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Во втором поле привилегированного пользователя замените "!!" зашифрованным паролем.

1. Создайте виртуальную машину в KVM из образа qcow2. Укажите тип диска **qcow2** и задайте для модели устройства интерфейса виртуальной сети значение **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.

1. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

    ```bash
    sudo systemctl enable network
    ```

1. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Эта команда также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт.

   Графическая и "тихая" загрузка бесполезны в облачной среде, где все журналы отправляются на последовательный порт. При желании можно оставить параметр `crashkernel`. Этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера. Мы рекомендуем удалить следующие параметры:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Добавьте модули Hyper-V в initramfs.

    Измените файл `/etc/dracut.conf` , добавив в него следующее содержимое:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Повторно создайте initramfs:

    ```bash
    dracut -f -v
    ```

1. Остановка и удаление cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:

    ```bash
    systemctl enable sshd
    ```

    Измените файл /etc/ssh/sshd_config, включив в него следующие строки:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. При создании пользовательского VHD-файла для Azure Stack имейте в виду, что WALinuxAgent версий 2.2.20–2.2.35 (монопольный доступ) не работает в средах Azure Stack. Для подготовки образа вы можете использовать версии 2.2.20/2.2.35. Чтобы использовать для подготовки пользовательского образа версии выше 2.2.35, обновите Azure Stack до выпуска версии 1903 или установите исправление 1901/1902.

    Выполните такие действия, чтобы скачать WALinuxAgent:

    a. Скачайте setuptools.

    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```

   b. Скачайте и распакуйте версию 2.2.20 агента из нашего репозитория GitHub.

    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```

    c. Установите setup.py.

    ```bash
    sudo python setup.py install
    ```

    d. Перезапустите waagent.

    ```bash
    sudo systemctl restart waagent
    ```

    д. Проверьте, совпадает ли версия агента со скачанной версией. В нашем примере это должна быть версия 2.2.20.

    ```bash
    waagent -version
    ```

1. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Локальный диск ресурсов является временным и может быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

    ```bash
    subscription-manager unregister
    ```

1. Если вы используете систему, развернутую с помощью центра сертификации предприятия, виртуальная машина RHEL не будет доверять корневому сертификату Azure Stack. Необходимо поместить его в доверенное корневое хранилище. Дополнительные сведения см. в разделе о [добавлении доверенных корневых сертификатов на сервер](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Завершите работу виртуальной машины в KVM.

1. Конвертируйте образ qcow2 в формат VHD.

    > [!NOTE]
    > В команде qemu-img версии 2.2.1 и более поздних версий есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. В противном случае выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.

    Для начала конвертируйте образ в формат RAW:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер до 1 МБ:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Иначе с помощью qemu версии **2.6** или выше добавьте параметр `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Подготовка виртуальной машины на основе Red Hat с помощью VMware

В этом разделе предполагается, что вы уже установили виртуальную машину RHEL в VMware. Дополнительные сведения об установке операционной системы на виртуальной машине VMware см. [здесь](https://partnerweb.vmware.com/GOSIG/home.html).

* При установке операционной системы Linux мы рекомендуем использовать стандартные разделы, а не LVM (который зачастую используется по умолчанию при установке). Этот метод позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с операционной системой может быть подключен к другой виртуальной машине в целях устранения неполадок. При желании на дисках с данными можно использовать LVM или RAID.
* Не настраивайте раздел swap на диске операционной системы. Вы можете настроить агент Linux для создания файла подкачки на временном диске с ресурсами. Дополнительные сведения об этой конфигурации приведены ниже.
* При создании виртуального жесткого диска выберите параметр **Store virtual disk as a single file**(Сохранять виртуальный диск как один файл).

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>Подготовка виртуальной машины RHEL 7 с помощью VMware

1. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

    ```bash
    sudo chkconfig network on
    ```

1. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт. Мы рекомендуем удалить следующие параметры:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

1. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Добавьте модули Hyper-V в initramfs.

    Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Повторно создайте initramfs:

    ```bash
    dracut -f -v
    ```

1. Остановите и удалите cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно этот параметр задан по умолчанию. Измените файл `/etc/ssh/sshd_config` , добавив в него следующую строку:

    ```sh
    ClientAliveInterval 180
    ```

1. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Установите агент Linux для Azure, выполнив следующую команду:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Чтобы отменить регистрацию подписки, выполните следующую команду:

    ```bash
    sudo subscription-manager unregister
    ```

1. Если вы используете систему, развернутую с помощью центра сертификации предприятия, виртуальная машина RHEL не будет доверять корневому сертификату Azure Stack. Необходимо поместить его в доверенное корневое хранилище. Дополнительные сведения см. в разделе о [добавлении доверенных корневых сертификатов на сервер](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Завершите работу виртуальной машины и конвертируйте файл VMDK в формат VHD.

    > [!NOTE]
    > В команде qemu-img версии 2.2.1 и более поздних версий есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. В противном случае выполните обновление до версии 2.6 или выше. Справочные материалы: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Для начала конвертируйте образ в формат RAW:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер до 1 МБ:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Иначе с помощью qemu версии **2.6** или выше добавьте параметр `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Подготовка виртуальной машины под управлением Red Hat из ISO-образа с помощью автоматического использования файла kickstart

1. Создайте файл kickstart, который будет включать содержимое ниже, и сохраните его. Дополнительные сведения об установке kickstart см. [здесь](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Поместите файл kickstart в место, доступное для системы установки.

1. В диспетчере Hyper-V создайте новую виртуальную машину. На странице **Подключить виртуальный жесткий диск** выберите параметр **Подключить виртуальный жесткий диск позднее** и выполните инструкции в мастере создания виртуальной машины.

1. Откройте параметры виртуальной машины:

    a. Загрузите новый виртуальный жесткий диск на виртуальную машину. Выберите параметры **VHD Format** (Формат VHD) и **Фиксированный размер**.

    b. Подключите установочный ISO-образ к DVD-дисководу.

    c. В BIOS выберите загрузку с компакт-диска.

1. Запустите виртуальную машину. Когда отобразится руководство по установке, нажмите клавишу **TAB** , чтобы настроить параметры загрузки.

1. Введите `inst.ks=<the location of the kickstart file>` в конце параметров загрузки и нажмите клавишу **ВВОД**.

1. Дождитесь завершения процесса установки. По окончании процесса виртуальная машина завершит работу автоматически. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="known-issues"></a>Известные проблемы

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Не удалось включить драйвер Hyper-V в начальный электронный диск при использовании низкоуровневой оболочки Hyper V

В некоторых случаях установщики Linux могут не включать драйверы для Hyper-V в начальный электронный диск (initrd или initramfs), если только не обнаружат, что он работает в среде Hyper-V.

Если для подготовки образа Linux используется другая система виртуализации (например, Oracle VM VirtualBox, Xen Project и пр.), вам может потребоваться выполнить повторную сборку initrd, чтобы убедиться, что на начальном электронном диске доступны по крайней мере модули ядра hv_vmbus и hv_storvsc. Это известная проблема по крайней мере в системах на основе предшествующего дистрибутива Red Hat.

Чтобы устранить эту проблему, необходимо добавить модули Hyper-V в initramfs и выполнить повторную сборку:

Измените файл `/etc/dracut.conf`, добавив следующее содержимое:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Повторно создайте initramfs:

```bash
dracut -f -v
```

Дополнительные сведения см. в разделе о [повторном создании initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Дополнительная информация

Теперь виртуальный жесткий диск Red Hat Enterprise Linux можно использовать для создания новых виртуальных машин в Azure Stack. Если вы впервые отправляете VHD-файл в Azure Stack, ознакомьтесь со статьей [Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Чтобы получить дополнительные сведения о низкоуровневых оболочках, сертифицированных для запуска Red Hat Enterprise Linux, посетите [веб-сайт Red Hat](https://access.redhat.com/certified-hypervisors).
