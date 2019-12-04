---
title: Обновление DNS-сервера пересылки в Azure Stack | Документация Майкрософт
description: Узнайте, как обновить DNS-сервер пересылки в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308245"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>Обновление DNS-сервера пересылки в Azure Stack

Чтобы инфраструктура Azure Stack могла разрешать внешние имена, необходим хотя бы один доступный DNS-сервер пересылки. Такой сервер необходимо предоставить для развертывания Azure Stack. Он используется для внутренних DNS-серверов Azure Stack в качестве сервера пересылки и обеспечивает разрешение внешних имен для таких служб, как проверка подлинности, управление Marketplace или использование.

DNS — это важная служба для инфраструктуры центра обработки данных, которая может измениться, и в таком случае необходимо будет обновить Azure Stack.

В этой статье описывается использование привилегированной конечной точки (PEP) для обновления DNS-сервера пересылки в Azure Stack. Рекомендуется использовать два надежных IP-адреса DNS-сервера пересылки.

1. Подключитесь к [привилегированной конечной точке](azure-stack-privileged-endpoint.md). Обратите внимание, что нет необходимости разблокировать привилегированную конечную точку, открывая запрос в службу поддержки.

2. Чтобы просмотреть текущие настройки DNS-сервера пересылки, выполните следующую команду. Вы также можете использовать свойства региона на портале администратора:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Чтобы обновить Azure Stack для использования нового DNS-сервера пересылки, выполните следующую команду:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Проверьте отсутствие ошибок в выходных данных команды.

## <a name="next-steps"></a>Дополнительная информация

[Интеграция брандмауэра](azure-stack-firewall.md)
