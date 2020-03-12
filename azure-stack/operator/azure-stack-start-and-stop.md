---
title: Запуск и остановка
titleSuffix: Azure Stack Hub
description: Сведения о том, как запустить и остановить Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: d6742ad75625eaed8f7e8c1ebec725fa1b62d53c
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367528"
---
# <a name="start-and-stop-azure-stack-hub"></a>Запуск и остановка Azure Stack Hub

Следуйте инструкциям по корректному завершению работы и перезапуску служб Azure Stack Hub, приведенным в этой статье. Операция *остановки* физически завершает работу и отключает питание всей среды Azure Stack Hub. При *запуске* включаются все роли инфраструктуры, а ресурсы клиента возвращаются в состояние, в котором они находились до завершения работы.

## <a name="stop-azure-stack-hub"></a>Остановка Azure Stack Hub

Чтобы остановить или завершить работу Azure Stack Hub, выполните следующие действия.

1. Подготовьте все рабочие нагрузки в ресурсах клиента в среде Azure Stack Hub к завершению работы.

2. Откройте сеанс привилегированной конечной точки (PEP) на компьютере с сетевым доступом к виртуальным машинам ERCS Azure Stack Hub. См. инструкции по [использованию привилегированной конечной точки в Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. Из сеанса PEP выполните следующую команду:

    ```powershell
      Stop-AzureStack
    ```

4. Дождитесь отключения питания на всех физических узлах Azure Stack Hub.

> [!Note]
> Чтобы проверить состояние питания физического узла, следуйте инструкциям от изготовителя оборудования (OEM), поставившего оборудование Azure Stack Hub.

## <a name="start-azure-stack-hub"></a>Запуск Azure Stack Hub

Чтобы запустить Azure Stack Hub, сделайте следующее. Выполните следующие действия, независимо от того, как остановлена работа Azure Stack Hub.

1. Включите питание на всех физических узлах в вашей среде Azure Stack Hub. Убедитесь, что питание для физических узлов включено по инструкциям изготовителя оборудования, поставившего оборудование для Azure Stack Hub.

2. Дождитесь запуска служб инфраструктуры Azure Stack Hub. Для завершения запуска служб инфраструктуры Azure Stack Hub может потребоваться два часа. Вы можете проверить состояние запуска Azure Stack Hub с помощью командлета [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack-hub).

3. Убедитесь, что все ресурсы клиента вернулись в состояние, в котором они находились до завершения работы. Рабочие нагрузки, выполняющиеся на ресурсах клиента, могут потребовать повторной настройки в диспетчере рабочих нагрузок после запуска.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Получение сведений о состоянии запуска для Azure Stack Hub

Чтобы получить сведения о запуске для подпрограммы запуска Azure Stack Hub, выполните следующие действия.

1. Откройте сеанс PEP на компьютере с сетевым доступом к виртуальным машинам ERCS Azure Stack Hub.

2. Из сеанса PEP выполните следующую команду:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Устранение неполадок при запуске и завершении работы Azure Stack Hub

Если через два часа после включения питания в среде Azure Stack Hub не удалось запустить службы инфраструктуры и клиентские службы, выполните следующие действия.

1. Откройте сеанс PEP на компьютере с сетевым доступом к виртуальным машинам ERCS Azure Stack Hub.

2. Выполните команду:

    ```powershell
      Test-AzureStack
      ```

3. Просмотрите выходные данные и устраните ошибки работоспособности. Дополнительные сведения см. в статье [Проверка состояния системы Azure Stack Hub](azure-stack-diagnostic-test.md).

4. Выполните команду:

    ```powershell
      Start-AzureStack
    ```

5. Если команда **Start-AzureStack** завершится ошибкой, обратитесь в службу поддержки Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [средствах диагностики Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
