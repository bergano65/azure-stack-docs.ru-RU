---
title: Развертывание решения аналитики промежуточных данных в Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как развернуть решение аналитики промежуточных данных в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 37ffb494ab6ae124c6a0eb460da4569f660cd283
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159358"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>развертыванию решения аналитики промежуточных данных в Azure Stack

В этой статье описано, как развернуть решение для сбора данных, анализ которых необходимо выполнять в точке сбора таким образом, чтобы можно было быстро принимать решения. Часто этот сбор данных происходит без доступа к Интернету. Когда подключение будет установлено, возможно, потребуется выполнить ресурсоемкий анализ данных, чтобы получить дополнительные сведения.

В этом решении вы создадите среду, чтобы затем выполнить в ней следующие действия:

> [!div class="checklist"]
> - Создание большого двоичного объекта для хранения необработанных данных.
> - Создание функции Azure Stack для перемещения очищенных данных из Azure Stack в Azure.
> - Создание функции для триггеров хранилища BLOB-объектов.
> - Создание учетной записи хранения Azure Stack, содержащей большой двоичный объект и очередь.
> - Создание функции, активируемой очередью.
> - Тестирование функции, активируемой очередью.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Только это гибридное облако позволяет создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В статье [Hybrid cloud design patterns for Azure Stack](azure-stack-edge-pattern-overview.md) (Рекомендации по проектированию гибридных приложений) описаны основные аспекты качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которые следует учитывать при разработке, развертывании и использовании гибридных приложений. Эти рекомендации помогут оптимизировать разработку гибридных приложений и предотвратить появление проблем с рабочими средами.

## <a name="architecture-for-staged-data-analytics"></a>Архитектура аналитики промежуточных данных

![Аналитика промежуточных данных](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Предварительные требования для аналитики промежуточных данных

  - Подписка Azure.
  - Субъект-служба Azure Active Directory (Azure AD) с разрешениями для подписки клиента в Azure и Azure Stack. Если в Azure Stack и подписке Azure используются разные клиенты AAD, возможно, потребуется создать два субъекта-службы. Дополнительные сведения о создании субъекта-службы для Azure Stack см. в статье [Предоставление приложениям доступа к Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Запомните или запишите идентификатор приложения каждого субъекта-службы, секрет клиента, идентификатор клиента Azure AD и имя клиента (xxxxx.onmicrosoft.com).**
  - Необходимо предоставить набор данных для анализа. Образец данных предоставляется.
  - Приложение [Docker для Windows](https://docs.docker.com/docker-for-windows/), установленное на локальном компьютере.

## <a name="get-the-docker-image"></a>Получение образа Docker

Использование образов Docker для каждого развертывания позволяет устранить проблемы с зависимостями разных версий Azure PowerShell.
1.  Убедитесь, что Docker для Windows использует контейнеры Windows.
2.  Выполните следующую команду в командной строке с повышенными привилегиями для получения контейнера Docker с помощью скриптов развертывания.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Развертывание решения

1.  После извлечения образа контейнера запустите его.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  После запуска контейнера откроется терминал PowerShell с повышенными привилегиями в контейнере. Измените каталоги, чтобы получить скрипт развертывания.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Запустите развертывание. Укажите учетные данные и имена ресурсов. HA указывает на экземпляр Azure Stack, в котором развертывается высокодоступный кластер, а DR — на экземпляр Azure Stack, в котором развертывается кластер аварийного восстановления.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  При появлении соответствующего запроса введите регион развертывания Azure и Application Insights.

2.  Введите Y, чтобы разрешить установку поставщика NuGet для запуска устанавливаемых модулей профиля API 2018-03-01-hybrid. Это позволит выполнить развертывание в Azure и Azure Stack.

3.  После развертывания ресурсов проверьте, создаются ли данные для Azure Stack и Azure.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Чтобы просмотреть обрабатываемые данные, перейдите к веб-приложениям, развернутым в Azure или Azure Stack.

### <a name="azure-web-app"></a>Веб-приложение Azure
 
![Решения для аналитики промежуточных данных](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Веб-приложение Azure Stack
 
![Решения аналитики промежуточных данных для Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Дополнительная информация

  - Узнайте больше о [гибридных облачных приложениях](https://aka.ms/azsdevtutorials).

  - Используйте собственные данные или измените код на основе примера на сайте [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
