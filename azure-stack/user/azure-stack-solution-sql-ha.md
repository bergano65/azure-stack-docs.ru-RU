---
title: Развертывание группы доступности SQL Server 2016 в Azure и Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как развернуть группу доступности SQL Server 2016 в Azure и Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 39b0078345c16b7931f41cd2394476f8258d92dd
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308823"
---
# <a name="tutorial-deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>Руководство по Развертывание группы доступности SQL Server 2016 в Azure и Azure Stack

В этой статье описано, как автоматически развернуть базовый высокодоступный (HA) кластер SQL Server 2016 Enterprise с сайтом асинхронного аварийного восстановления (DR) в двух средах Azure Stack. Подробные сведения об SQL Server 2016 и высоком уровне доступности см. в статье [Группы доступности Always On: решение для обеспечения высокой доступности и аварийного восстановления](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

В рамках этого руководства вы создадите пример среды и выполните в ней следующие действия:

> [!div class="checklist"]
> - Оркестрация развертывания в двух экземплярах Azure Stack
> - Использование Docker для предотвращения возникновения проблем с зависимостями с помощью профилей API Azure
> - Развертывание основных высокодоступных кластеров SQL Server 2016 Enterprise с сайта аварийного восстановления

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Это единственное гибридное облако, которое позволяет создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В [рекомендациях по проектированию гибридных приложений](https://aka.ms/hybrid-cloud-applications-pillars) описаны характеристики качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которых следует придерживаться при разработке, развертывании и использовании гибридных приложений. Рекомендации помогут оптимизировать разработку гибридных приложений и свести к минимуму проблемы в рабочих средах.

## <a name="architecture-for-sql-server-2016"></a>Архитектура для SQL Server 2016

![SQL Server 2016 SQL HA Azure Stack](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Предварительные требования для SQL Server 2016

  - Две подключенные интегрированные системы Azure Stack (это развертывание не поддерживает Пакеты средств разработки Azure Stack (ASDK)). Подробные сведения см. в статье об [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
  - Подписка клиента в каждом экземпляре Azure Stack.    
      - **Запомните или запишите идентификатор каждой подписки и конечной точки Azure Resource Manager для каждого экземпляра Azure Stack.**
  - Субъект-служба Azure Active Directory (Azure AD) с разрешениями для подписки клиента в каждом экземпляре Azure Stack. Вам может потребоваться создать два субъекта-службы, если экземпляры Azure Stack развертываются в разных клиентах Azure AD. Подробные сведения о создании субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Запомните или запишите идентификатор приложения каждого субъекта-службы, секрет клиента и имя клиента (xxxxx.onmicrosoft.com).**
  - Для SQL Server 2016 Enterprise выполняется синдикация со всеми экземплярами Marketplace в Azure Stack. Подробные сведения о синдикации Marketplace см. в статье [Скачивание элементов Marketplace из Azure в Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Убедитесь, что в организации есть соответствующие лицензии SQL**.
  - Приложение [Docker для Windows](https://docs.docker.com/docker-for-windows/), установленное на локальном компьютере.

## <a name="get-the-docker-image"></a>Получение образа Docker

Использование образов Docker для каждого развертывания позволяет устранить проблемы с зависимостями разных версий Azure PowerShell.

1.  Убедитесь, что Docker для Windows использует контейнеры Windows.
2.  Выполните следующую команду в командной строке с повышенными привилегиями для получения контейнера Docker с помощью скриптов развертывания.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Развертывание группы доступности

1.  После извлечения образа контейнера запустите его.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  После запуска контейнера откроется терминал PowerShell с повышенными привилегиями в контейнере. Измените каталоги, чтобы получить скрипт развертывания.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Запустите развертывание. Укажите учетные данные и имена ресурсов. HA указывает на экземпляр Azure Stack, в котором развертывается высокодоступный кластер, а DR — на экземпляр Azure Stack, в котором развертывается кластер аварийного восстановления.

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
      -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
      -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
      -AADTenantName_HA "hatenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_HA "haresourcegroupname" `
      -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
      -AzureStackSubscriptionId_HA "haSubscriptionId" `
      -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
      -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
      -AADTenantName_DR "drtenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_DR "drresourcegroupname" `
      -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
      -AzureStackSubscriptionId_DR "drSubscriptionId"
      ```

4.  Введите `Y`, чтобы разрешить установку поставщика NuGet для запуска устанавливаемых модулей профиля API 2018-03-01-hybrid.

5.  Дождитесь завершения развертывания ресурса.

6.  После завершения развертывания ресурсов аварийного восстановления выйдите из контейнера.

      ```powershell
      exit
      ```

7.  Проверьте развертывание, просмотрев ресурсы на каждом портале Azure Stack. Подключитесь к одному из экземпляров SQL в высокодоступной среде и проверьте группу доступности с использованием SQL Server Management Studio (SSMS).

![SQL Server 2016 SQL HA](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>Дополнительная информация

  - Подробные сведения об использовании SQL Server Management Studio для выполнения отработки отказа кластера вручную в руководстве по [Выполнение принудительного перехода на другой ресурс вручную для группы доступности Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017).
  - Подробные сведения о приложениях гибридного облака см. в статье о [гибридных облачных решениях](https://aka.ms/azsdevtutorials).
  - Используйте собственные данные или измените код на основе примера на сайте [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
