---
title: Применение Azure CLI для пользователей Azure Stack Hub | Документация Майкрософт
description: Узнайте, как включить кроссплатформенный интерфейс командной строки (CLI) для развертывания и администрирования ресурсов в Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 333fd0eeb3f3e7162069d98c8f45a94228af5032
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534045"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>Применение Azure CLI для пользователей Azure Stack Hub

Корневой сертификат ЦС можно предоставить пользователям Azure Stack Hub, чтобы они могли использовать Azure CLI на своих виртуальных машинах для разработки. Этот сертификат понадобится пользователям для управления ресурсами с помощью CLI.

 - **Корневой сертификат ЦС Azure Stack Hub** нужен, если CLI используется на рабочей станции без Пакета средств разработки Azure Stack.  

 - **Конечная точка псевдонимов виртуальных машин.** Она предоставляет псевдонимы, например UbuntuLTS или Win2012Datacenter, которые позволяют при развертывании виртуальной машины в одном параметре указать сведения об издателе образа, предложении, номере SKU и версии.  

В разделах ниже описано, как получить эти значения.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Экспорт корневого сертификата ЦС Azure Stack Hub

При использовании интегрированной системы вам не нужно экспортировать корневой сертификат ЦС. На ASDK нужно экспортировать корневой сертификат ЦС.

Чтобы экспортировать корневой сертификат ASDK в PEM-формате, войдите в пакет разработки и выполните следующий скрипт.

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>Настройка конечной точки псевдонимов виртуальных машин

Операторам Azure Stack Hub следует настроить общедоступную конечную точку и разместить в ней файл псевдонимов виртуальных машин. В файле псевдонимов виртуальных машин хранится общее имя образа в JSON-формате. Имя будет использоваться при развертывании виртуальной машины в качестве параметра Azure CLI.  

Прежде чем добавить запись в файл псевдонимов, [скачайте образы из Azure Marketplace](azure-stack-download-azure-marketplace-item.md) или [опубликуйте собственный пользовательский образ](azure-stack-add-vm-image.md). Когда вы публикуете пользовательский образ, запишите сведения об издателе, предложении, номере SKU и версии, которые указываете во время публикации. Если используется образ из Marketplace, вы можете просмотреть эти сведения с помощью командлета `Get-AzureVMImage`.  

Мы предлагаем [образец файла псевдонимов](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) с несколькими широко используемыми псевдонимами образов. Вы можете использовать его в качестве отправной точки. Разместите этот файл в расположении, доступном для всех клиентов, которые используют CLI. Например, его можно поместить в учетную запись хранилища BLOB-объектов, а пользователям предоставить соответствующий URL-адрес:

1. Скачайте [образец файла](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) с сайта GitHub.
2. Создайте учетную запись хранения в Azure Stack Hub. Затем создайте контейнер BLOB-объектов. Установите для политики доступа значение public.  
3. Отправьте JSON-файл в только что созданный контейнер. После этого просмотрите URL-адрес большого двоичного объекта. Выберите имя большого двоичного объекта, затем URL-адрес из раздела свойств большого двоичного объекта.

## <a name="next-steps"></a>Дальнейшие действия

- [Развертывание шаблонов с помощью интерфейса командной строки Azure](../user/azure-stack-deploy-template-command-line.md )
- [Подключение с помощью PowerShell](azure-stack-powershell-install.md)
- [Управление разрешениями пользователей](azure-stack-manage-permissions.md)
