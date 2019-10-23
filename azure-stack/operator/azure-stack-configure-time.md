---
title: Настройка сервера времени для Azure Stack | Документация Майкрософт
description: Узнайте, как настроить сервер времени для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280555"
---
# <a name="configure-the-time-server-for-azure-stack"></a>Настройка сервера времени для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*  

В Azure Stack вы можете использовать привилегированную конечную точку (PEP) для изменения сервера времени. Укажите имя узла, которое разрешается в два или более IP-адреса NTP-серверов.

Azure Stack использует протокол NTP для подключения к серверам времени в Интернете. NTP-серверы предоставляют точное системное время. Это время используется в физических сетевых коммутаторах Azure Stack, на узле жизненного цикла оборудования, в службе инфраструктуры и на виртуальных машинах. Если часы не синхронизированы, в Azure Stack могут возникнуть серьезные проблемы с сетью и проверкой подлинности. Файлы журналов, документы и другие файлы будут создаваться с неправильными метками времени.

Для синхронизации времени в Azure Stack должен использоваться хотя бы один сервер времени (NTP). При развертывании Azure Stack вы указываете адрес NTP-сервера. Время является критически важной службой для инфраструктуры центра обработки данных. Если эта служба изменится, вам придется изменить параметры времени.

## <a name="configure-time"></a>Настройка времени

1. [Подключитесь к привилегированной конечной точке](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > Нет необходимости разблокировать привилегированную конечную точку, открывая запрос в службу поддержки.

2. Чтобы просмотреть текущие настройки NTP-сервера, выполните следующую команду.

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Чтобы изменить NTP-сервер, используемый в Azure Stack, немедленно синхронизировать время, выполните следующую команду.

    > [!Note]  
    > Эта процедура не обновляет сервер времени на физических коммутаторах.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Проверьте отсутствие ошибок в выходных данных команды.


## <a name="next-steps"></a>Дополнительная информация

[Просмотр отчета о готовности](azure-stack-validation-report.md)  
[Общие рекомендации по интеграции Azure Stack](azure-stack-datacenter-integration.md)  
