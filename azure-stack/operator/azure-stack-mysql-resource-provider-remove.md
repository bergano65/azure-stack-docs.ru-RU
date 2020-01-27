---
title: Удаление поставщика ресурсов MySQL в Azure Stack Hub | Документация Майкрософт
description: Сведения об удалении поставщика ресурсов MySQL из развертывания Azure Stack Hub.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 0f616bced206bf32da0ace8c1f3cb836c66962e6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534742"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Удаление поставщика ресурсов MySQL в Azure Stack Hub

Прежде чем удалить поставщик ресурсов MySQL, необходимо удалить все его зависимости. Кроме того, потребуется скопировать пакет развертывания, который был использован для установки поставщика ресурсов.

> [!NOTE]
> Скачать установщики поставщика ресурсов можно с помощью ссылок, приведенных в разделе [предварительных требований для развертывания поставщика ресурсов](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

При удалении поставщика ресурсов MySQL базы данных клиента не удаляются с серверов размещения.

## <a name="dependency-cleanup"></a>Очистка зависимостей

Есть несколько задач очистки, которые нужно запустить перед выполнением скрипта DeployMySqlProvider.ps1 для удаления поставщика ресурсов.

Эти задачи очистки выполняет оператор Azure Stack Hub:

* Удаление всех планов, которые ссылаются на этот адаптер MySQL.
* Удаление всех квот, которые связаны с этим адаптером MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Удаление поставщика ресурсов MySQL

1. Убедитесь, что удалены все существующие зависимости поставщика ресурсов MySQL.

   > [!NOTE]
   > Удаление поставщика ресурсов MySQL продолжится, даже если в этот момент его используют зависимые ресурсы.
  
2. Получите копию пакета установки поставщика ресурсов MySQL и запустите файл для самостоятельного извлечения содержимого во временный каталог.
3. Откройте новую консоль PowerShell с повышенными привилегиями и перейдите в каталог, в который ранее извлекли файлы установки поставщика ресурсов MySQL.
4. Запустите скрипт DeployMySqlProvider.ps1 со следующими параметрами:
    - **Uninstall**: Удаляет поставщик ресурсов и все связанные с ним ресурсы.
    - **PrivilegedEndpoint**: IP-адрес или DNS-имя привилегированной конечной точки.
    - **AzureEnvironment**: Среда Azure, используемая для развертывания Azure Stack Hub. Требуется только для развертываний Azure AD.
    - **CloudAdminCredential**: Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке.
    - **DirectoryTenantID**
    - **AzCredential**: Учетные данные администратора службы Azure Stack Hub. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack Hub.

## <a name="next-steps"></a>Дальнейшие действия

[Предложение служб приложений как PaaS](azure-stack-app-service-overview.md)
