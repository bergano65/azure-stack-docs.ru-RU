---
title: Развертывание высокодоступного решения MongoDB в Azure и Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как развернуть высокодоступное решение MongoDB в Azure и Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 25be3914f58abad44b64870f9da94610a7498d52
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603041"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Развертывание высокодоступного решения MongoDB в Azure и Azure Stack

В этой статье описано, как автоматически развернуть базовый высокодоступный (HA) кластер MongoDB с сайтом аварийного восстановления (DR) в двух средах Azure Stack. Подробные сведения о MongoDB и высоком уровне доступности см. в руководстве по [элементам набора реплик](https://docs.mongodb.com/manual/core/replica-set-members/).

В этом решении вы создадите среду, чтобы затем выполнить в ней следующие действия:

> [!div class="checklist"]
> - Оркестрация развертывания в двух экземплярах Azure Stack
> - Использование Docker для предотвращения возникновения проблем с зависимостями с помощью профилей API Azure
> - Развертывание основных высокодоступных кластеров MongoDB с сайта аварийного восстановления


> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Только это гибридное облако позволяет создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В статье [Hybrid cloud design patterns for Azure Stack](azure-stack-edge-pattern-overview.md) (Рекомендации по проектированию гибридных приложений) описаны основные аспекты качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которые следует учитывать при разработке, развертывании и использовании гибридных приложений. Эти рекомендации помогут оптимизировать разработку гибридных приложений и предотвратить появление проблем с рабочими средами.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Архитектура MongoDB для использования с Azure Stack

![Использование высокодоступного решения MongoDB с Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Требования к MongoDB для использования с Azure Stack

  - Две подключенные интегрированные системы Azure Stack (это развертывание не поддерживает Пакеты средств разработки Azure Stack (ASDK)). Подробные сведения см. в статье об [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
      - Подписка клиента в каждом экземпляре Azure Stack.    
      - **Запомните или запишите идентификатор каждой подписки и конечной точки Azure Resource Manager для каждого экземпляра Azure Stack.**
  - Субъект-служба Azure Active Directory (Azure AD) с разрешениями для подписки клиента в каждом экземпляре Azure Stack. Вам может потребоваться создать два субъекта-службы, если экземпляры Azure Stack развертываются в разных клиентах Azure AD. Подробные сведения о создании субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Запомните или запишите идентификатор приложения каждого субъекта-службы, секрет клиента и имя клиента (xxxxx.onmicrosoft.com).**
  - Для Ubuntu 16.04 выполняется синдикация со всеми экземплярами Marketplace в Azure Stack. Подробные сведения о синдикации Marketplace см. в статье [Скачивание элементов Marketplace из Azure в Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - Приложение [Docker для Windows](https://docs.docker.com/docker-for-windows/), установленное на локальном компьютере.

## <a name="get-the-docker-image"></a>Получение образа Docker

Использование образов Docker для каждого развертывания позволяет устранить проблемы с зависимостями разных версий Azure PowerShell.
1.  Убедитесь, что Docker для Windows использует контейнеры Windows.
2.  Выполните следующую команду в командной строке с повышенными привилегиями для получения контейнера Docker с помощью скриптов развертывания.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Развертывание кластеров

1.  После извлечения образа контейнера запустите его.

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  После запуска контейнера откроется терминал PowerShell с повышенными привилегиями в контейнере. Измените каталоги, чтобы получить скрипт развертывания.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Запустите развертывание. Укажите учетные данные и имена ресурсов. HA указывает на экземпляр Azure Stack, в котором развертывается высокодоступный кластер, а DR — на экземпляр Azure Stack, в котором развертывается кластер аварийного восстановления.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
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

5.  Сначала развертываются высокодоступные ресурсы. Отслеживая развертывание, дождитесь его завершения. После этого развернутые ресурсы можно проверить на портале Azure Stack (HA). 

6.  Вы можете продолжить развертывать ресурсы аварийного восстановления и решить, если вы хотите включить сервер перехода (jump box) в Azure Stack (DR) для взаимодействия с кластером.

7.  Дождитесь завершения развертывания ресурса аварийного восстановления.

8.  После завершения развертывания ресурсов аварийного восстановления выйдите из контейнера.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Дополнительная информация

  - Если вы включили виртуальную машину сервера перехода в Azure Stack (DR), вы можете подключиться по протоколу SSH для взаимодействия с кластером MongoDB, установив Mongo CLI. Подробные сведения о взаимодействии с MongoDB с помощью Mongo см. в статье об [оболочке Mongo](https://docs.mongodb.com/manual/mongo/).

  - Подробные сведения о приложениях гибридного облака см. в статье о [гибридных облачных решениях](https://aka.ms/azsdevtutorials).

  - Измените код на основе примера на сайте [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
