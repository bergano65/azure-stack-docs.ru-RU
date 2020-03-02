---
title: Развертывание высокодоступного решения MongoDB в Azure и Azure Stack Hub
description: Узнайте, как развернуть высокодоступное решение MongoDB в Azure и Azure Stack Hub
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7611387047d80ad7c60f4402c7399d3d0d4e8b09
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568340"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>Развертывание высокодоступного решения MongoDB в Azure и Azure Stack Hub

В этой статье описано, как автоматически развернуть базовый высокодоступный кластер MongoDB с сайтом аварийного восстановления в двух средах Azure Stack Hub. Подробные сведения о MongoDB и высоком уровне доступности см. в руководстве по [элементам набора реплик](https://docs.mongodb.com/manual/core/replica-set-members/).

В этом решении вы создадите среду, чтобы затем выполнить в ней следующие действия:

> [!div class="checklist"]
> - Оркестрация развертывания в двух экземплярах Azure Stack Hub.
> - Использование Docker для предотвращения возникновения проблем с зависимостями с помощью профилей API Azure
> - Развертывание основных высокодоступных кластеров MongoDB с сайта аварийного восстановления


> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub — это расширение Azure. Azure Stack Hub обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Это решение позволяет использовать единственное гибридное облако, с помощью которого можно создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В статье [Hybrid cloud design patterns for Azure Stack](overview-app-design-considerations.md) (Рекомендации по проектированию гибридных приложений) описаны основные аспекты качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которые следует учитывать при разработке, развертывании и использовании гибридных приложений. Эти рекомендации помогут оптимизировать разработку гибридных приложений и предотвратить появление проблем с рабочими средами.



## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>Архитектура MongoDB для использования с Azure Stack Hub

![Использование высокодоступного решения MongoDB с Azure Stack Hub](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>Требования к MongoDB для использования с Azure Stack Hub

  - Две подключенные интегрированные системы Azure Stack Hub (это развертывание не поддерживает пакеты средств разработки Azure Stack (ASDK)). См. сведения об [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
      - Подписка клиента в каждом экземпляре Azure Stack Hub.    
      - **Запомните или запишите идентификатор каждой подписки и конечной точки Azure Resource Manager для каждого экземпляра Azure Stack Hub.**
  - Субъект-служба Azure Active Directory (Azure AD) с разрешениями для подписки клиента в каждом экземпляре Azure Stack Hub. Вам может потребоваться создать два субъекта-службы, если экземпляры Azure Stack Hub развертываются в разных клиентах Azure AD. См. сведения о создании субъекта-службы для Azure Stack Hub в руководстве по [предоставлению приложениям доступа к Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Запомните или запишите идентификатор приложения каждого субъекта-службы, секрет клиента и имя клиента (xxxxx.onmicrosoft.com).**
  - Для Ubuntu 16.04 выполняется синдикация со всеми экземплярами Marketplace в Azure Stack Hub. См. сведения о синдикации Marketplace в [руководстве по скачиванию элементов Marketplace из Azure в Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
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

3.  Запустите развертывание. Укажите учетные данные и имена ресурсов. HA указывает на экземпляр Azure Stack Hub, в котором развертывается высокодоступный кластер, а DR — на экземпляр Azure Stack Hub, в котором развертывается кластер аварийного восстановления.

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

5.  Сначала развертываются высокодоступные ресурсы. Отслеживая развертывание, дождитесь его завершения. После этого развернутые ресурсы можно проверить на портале Azure Stack Hub (HA). 

6.  Вы можете продолжить развертывать ресурсы аварийного восстановления и решить, хотите ли вы включить инсталляционный сервер в Azure Stack Hub DR для взаимодействия с кластером.

7.  Дождитесь завершения развертывания ресурса аварийного восстановления.

8.  После завершения развертывания ресурсов аварийного восстановления выйдите из контейнера.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Дальнейшие действия

  - Если вы включили виртуальную машину инсталляционного сервера в Azure Stack Hub DR, вы можете подключиться по протоколу SSH для взаимодействия с кластером MongoDB, установив Mongo CLI. Подробные сведения о взаимодействии с MongoDB с помощью Mongo см. в статье об [оболочке Mongo](https://docs.mongodb.com/manual/mongo/).

  - Подробные сведения о приложениях гибридного облака см. в статье о [гибридных облачных решениях](https://aka.ms/azsdevtutorials).

  - Измените код на основе примера на сайте [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
