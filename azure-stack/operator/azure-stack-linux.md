---
title: Добавление образов Linux в Azure Stack Hub Marketplace
description: Узнайте, как добавлять образы Linux в Azure Stack Hub Marketplace.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: b5e15568b2943d34dd5456f924db59cfcf48cb7f
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890261"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Добавление образов Linux в Azure Stack Hub Marketplace

Виртуальные машины Linux можно развернуть в Azure Stack Hub, добавив образ на базе Linux в Azure Stack Hub Marketplace. Проще всего добавить образ Linux в Azure Stack Hub с помощью управления Marketplace. Эти образы были подготовлены и протестированы на совместимость с Azure Stack Hub.

## <a name="marketplace-management"></a>Управление Marketplace

Сведения о том, как скачать образы Linux из Azure Marketplace, см. в статье [Скачивание элементов Marketplace в Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Выберите образы Linux, которые вы хотите предложить пользователям в Azure Stack Hub.

Эти образы часто обновляются, поэтому чаще посещайте портал управления Marketplace, чтобы обеспечить их актуальное состояние.

## <a name="prepare-your-own-image"></a>Подготовка собственного образа

По возможности скачивайте образы, доступные на портале управления Marketplace. Они были подготовлены и протестированы для Azure Stack Hub.

### <a name="azure-linux-agent"></a>Агент Linux для Azure

Агент Linux для Azure (обычно называется **WALinuxAgent** или **walinuxagent**) использовать обязательно, при этом не все версии агента будут работать с Azure Stack Hub. Azure Stack Hub не поддерживает версии 2.2.21–2.2.34 (включительно). Для использования последней версии агента выше 2.2.35 примените исправления 1901 и 1902 или обновите Azure Stack Hub до выпуска 1903 (или последующего). Обратите внимание, что [cloud-init](https://cloud-init.io/) поддерживается в выпусках Azure Stack Hub, более поздних, чем версия 1910.

| Сборка Azure Stack Hub | Сборка агента Linux для Azure |
| ------------- | ------------- |
| 1.1901.0.99 или предыдущая | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 или последующая |
| 1.1902.2.73  | 2.2.35 или последующая |
| 1.1903.0.35  | 2.2.35 или последующая |
| Выпуски после 1903 | 2.2.35 или последующая |
| Не поддерживается | 2.2.21–2.2.34 |
| Выпуски после 1910 | Все версии агента Azure WALA|

Можно подготовить свой собственный образ Linux с помощью следующих инструкций.

* [Подготовка виртуальной машины на основе CentOS для Azure](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуального жесткого диска Debian для Azure](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [Подготовка виртуальной машины SLES или openSUSE для Azure](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Сервер Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

[Cloud-init](https://cloud-init.io/) поддерживается в выпусках Azure Stack Hub, более поздних, чем версия 1910. Чтобы настроить виртуальную машину Linux с помощью cloud-init, выполните приведенные ниже инструкции для PowerShell.

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>Шаг 1. Создание файла cloud-init.txt с облачной конфигурацией

Создайте файл cloud-init.txt и вставьте в него приведенную ниже облачную конфигурацию:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>Шаг 2. Добавление ссылки на cloud-init.txt во время развертывания виртуальной машины Linux

Отправьте файл в учетную запись хранения Azure, учетную запись хранения Azure Stack Hub или репозиторий GitHub, доступный для виртуальной машины Azure Stack Hub в Linux.
В настоящее время использование cloud-init для развертывания виртуальных машин поддерживается только в REST, PowerShell и CLI. В Azure Stack Hub нет связанного пользовательского интерфейса портала.

Чтобы создать виртуальную машину Linux с помощью PowerShell, выполните [эти](../user/azure-stack-quick-create-vm-linux-powershell.md) инструкции, но обязательно добавьте ссылку на файл cloud-init.txt в рамках флага `-CustomData`:

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Добавление образа в Marketplace

Следуйте указаниям по [добавлению образа в marketplace](azure-stack-add-vm-image.md). Убедитесь, что для параметра `OSType` задано значение `Linux`.

После добавления образа в Marketplace создается элемент Marketplace, и пользователи могут развернуть виртуальную машину Linux.

## <a name="next-steps"></a>Дальнейшие действия

* [Скачивание элементов Marketplace из Azure в Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
* [Общие сведения об Azure Stack Hub Marketplace](azure-stack-marketplace.md)
