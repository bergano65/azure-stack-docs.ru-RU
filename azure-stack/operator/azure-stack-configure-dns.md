---
title: Обновление DNS-сервера пересылки в Azure Stack Hub
description: Узнайте, как обновить DNS-сервер пересылки в Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6d5138868b07e2a3d552cd154976352266941760
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704477"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Обновление DNS-сервера пересылки в Azure Stack Hub

Чтобы инфраструктура Azure Stack Hub могла разрешать внешние имена, необходим хотя бы один доступный DNS-сервер пересылки. Такой сервер необходимо предоставить для развертывания Azure Stack Hub. Он используется для внутренних DNS-серверов Azure Stack Hub в качестве сервера пересылки и обеспечивает разрешение внешних имен для таких служб, как проверка подлинности, управление Marketplace или использование.

DNS — это важная служба для инфраструктуры центра обработки данных, которая может измениться, и в таком случае необходимо будет обновить Azure Stack Hub.

В этой статье описывается использование привилегированной конечной точки (PEP) для обновления DNS-сервера пересылки в Azure Stack Hub. Рекомендуется использовать два надежных IP-адреса DNS-сервера пересылки.

1. Подключитесь к [привилегированной конечной точке](azure-stack-privileged-endpoint.md). Обратите внимание, что нет необходимости разблокировать привилегированную конечную точку, открывая запрос в службу поддержки.

2. Чтобы просмотреть текущие настройки DNS-сервера пересылки, выполните следующую команду. Вы также можете использовать свойства региона на портале администратора:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Чтобы обновить Azure Stack Hub для использования нового DNS-сервера пересылки, выполните следующую команду:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Проверьте отсутствие ошибок в выходных данных команды.

## <a name="next-steps"></a>Дальнейшие действия

[Интеграция брандмауэра](azure-stack-firewall.md)
