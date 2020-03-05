---
title: Скачивание средств Azure Stack Hub из GitHub
description: Сведения о скачивании средств, необходимых для работы с Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 8d2f79d85055ef15f7dc0af3e1b36434f9c63d79
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698289"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Скачивание средств Azure Stack Hub из GitHub

**AzureStack-Tools** — это [репозиторий GitHub](https://github.com/Azure/AzureStack-Tools), в котором размещены модули PowerShell для администрирования и развертывания ресурсов в Azure Stack Hub. Если вы планируете установить VPN-подключение, можно скачать эти модули PowerShell в Пакет средств разработки Azure Stack (ASDK) или внешний клиент под управлением Windows. Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку **AzureStack-Tools**, выполнив следующий сценарий:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Возможности, предоставляемые модулями

Репозиторий **AzureStack-Tools** содержит модули PowerShell с поддержкой следующих функций для работы в среде Azure Stack Hub:  

| Функциональность | Описание | Кто может использовать этот модуль? |
| --- | --- | --- |
| [Возможности облачных служб](../user/azure-stack-validate-templates.md) | Этот модуль используется для получения возможностей облачных служб. Например, можно получить возможности облачных служб, например версию API и ресурсы Azure Resource Manager. Также можно получить расширения виртуальных машин для Azure Stack Hub и облаков Azure. | Операторы и пользователи облака. |
| [Управление политикой Azure с использованием модуля политики Azure Stack Hub](../user/azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с такой же версией и уровнем доступности службы, как и в Azure Stack Hub. | Операторы и пользователи облака. |
| [Регистрация в Azure](azure-stack-registration.md ) | Используйте этот модуль, чтобы зарегистрировать свой экземпляр ASDK в Azure. После регистрации вы сможете загружать элементы Azure Marketplace и использовать их в Azure Stack Hub. | Операторы облака. |
| [Развертывание Azure Stack Hub](../asdk/asdk-install.md) | Этот модуль используется для подготовки главного компьютера Azure Stack Hub к развертыванию и повторному развертыванию с помощью образа виртуального жесткого диска Azure Stack Hub (VHD). | Операторы облака.|
| [Подключение к Azure Stack Hub](azure-stack-powershell-install.md) | Этот модуль используется для настройки VPN-подключения к Azure Stack Hub. | Операторы и пользователи облака. |
| [Проверяющий элемент управления шаблона](../user/azure-stack-validate-templates.md) | Этот модуль позволяет проверить, можно ли развернуть существующий или новый шаблон в Azure Stack Hub. | Операторы и пользователи облака.|

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы с PowerShell в Azure Stack Hub](../user/azure-stack-powershell-overview.md).
- [Подключение к Azure Stack Hub в роли пользователя с помощью PowerShell](../user/azure-stack-powershell-configure-user.md).
- [Подключение к Пакету средств разработки Azure Stack с помощью VPN](../asdk/asdk-connect.md).
