---
title: Настройка сервера времени для Azure Stack Hub | Документация Майкрософт
description: Узнайте, как настроить сервер времени для Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: 2a473ab2b44362b7fc93cdd1879b1869f469ddfa
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534062"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Настройка сервера времени для Azure Stack Hub

В Azure Stack Hub вы можете использовать привилегированную конечную точку (PEP) для изменения сервера времени. Укажите имя узла, которое разрешается в два или более IP-адреса NTP-серверов.

Azure Stack Hub использует протокол NTP для подключения к серверам времени в Интернете. NTP-серверы предоставляют точное системное время. Это время используется в физических сетевых коммутаторах Azure Stack Hub, на узле жизненного цикла оборудования, в службе инфраструктуры и на виртуальных машинах. Если часы не синхронизированы, в Azure Stack Hub могут возникнуть серьезные проблемы с сетью и проверкой подлинности. Файлы журналов, документы и другие файлы будут создаваться с неправильными метками времени.

Для синхронизации времени в Azure Stack Hub должен использоваться сервер времени (NTP). При развертывании Azure Stack Hub вы указываете адрес NTP-сервера. Время является критически важной службой для инфраструктуры центра обработки данных. Если эта служба изменится, вам придется изменить параметры времени.

> [!NOTE]
> Azure Stack Hub поддерживает синхронизацию времени только с одним сервером времени (NTP). Вы не сможете указать несколько серверов для синхронизации времени в Azure Stack Hub.

## <a name="configure-time"></a>Настройка времени

1. [Подключитесь к привилегированной конечной точке](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > Нет необходимости разблокировать привилегированную конечную точку, открывая запрос в службу поддержки.

2. Чтобы просмотреть текущие настройки NTP-сервера, выполните следующую команду.

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Чтобы изменить NTP-сервер, используемый в Azure Stack Hub и немедленно синхронизировать время, выполните следующую команду.

    > [!Note]  
    > Эта процедура не обновляет сервер времени на физических коммутаторах.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Проверьте отсутствие ошибок в выходных данных команды.


## <a name="next-steps"></a>Дальнейшие действия

[Просмотр отчета о готовности](azure-stack-validation-report.md)  
[Планирование интеграции центра обработки данных для интегрированных систем Azure Stack Hub](azure-stack-datacenter-integration.md)  
