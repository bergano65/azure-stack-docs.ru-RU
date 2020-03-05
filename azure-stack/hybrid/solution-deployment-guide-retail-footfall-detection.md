---
title: Развертывание решения определения посещаемости на основе искусственного интеллекта с использованием Azure и Azure Stack Hub
description: Узнайте, как развернуть решение определения посещаемости с использованием Azure и Azure Stack Hub. Это решение используется для анализа трафика посетителей в розничных магазинах.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 3820d5483a0a7051ea51cc4ce7489d2cfe9f2d42
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77687501"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>Развертывание решения определения посещаемости на основе искусственного интеллекта с использованием Azure и Azure Stack Hub

В статье описывается, как развернуть решение, которое создает полезные сведения на основе реальных действий, используя Azure, Azure Stack Hub и комплект SDK для искусственного интеллекта Пользовательского визуального распознавания.

В этом решении вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - Развертывание пакетов облачных приложений (CNAB) на пограничных устройствах. 
> - Развертывание приложения, охватывающего границы облака.
> - Использование комплекта SDK для искусственного интеллекта Пользовательского визуального распознавания для вывода на пограничных устройствах.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub — это расширение Azure. Azure Stack Hub обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Это решение позволяет использовать единственное гибридное облако, с помощью которого можно создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В статье [Hybrid cloud design patterns for Azure Stack](overview-app-design-considerations.md) (Рекомендации по проектированию гибридных приложений) описаны основные аспекты качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которые следует учитывать при разработке, развертывании и использовании гибридных приложений. Эти рекомендации помогут оптимизировать разработку гибридных приложений и предотвратить появление проблем с рабочими средами.

## <a name="prerequisites"></a>Предварительные требования 

Прежде чем приступить к работе с этим руководством по развертыванию, не забудьте выполнить следующие действия:

- Ознакомьтесь с решением для определения посещаемости [здесь](pattern-retail-footfall-detection.md). 
- Получите пользовательский доступ к Пакету средств разработки Azure Stack (ASDK) или экземпляру интегрированной системы Azure Stack Hub, используя:
  - [Службу приложений Azure в установленном поставщике ресурсов Azure Stack Hub](../operator/azure-stack-app-service-overview.md). Для доступа к экземпляру Azure Stack Hub требуется доступ оператора. Можно также обратиться к администратору для установки.
  - Подписка на предложение, которое предоставляет квоту на Службу приложений и службу хранилища. Для создания предложения необходим доступ оператора.
- Получите доступ к подписке Azure.
  - Если у вас еще нет подписки Azure, [подпишитесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Создайте два субъекта-службы в каталоге:
  - Один, настроенный для использования с ресурсами Azure, с доступом в области подписки Azure. 
  - Другой, настроенный для использования с ресурсами Azure Stack Hub, с доступом в области подписки Azure Stack Hub. 
  - Дополнительные сведения о создании субъектов-служб и авторизации доступа см. в статье [Использование удостоверения приложения для доступа к ресурсам](../operator/azure-stack-create-service-principals.md). Если вы предпочитаете использовать Azure CLI, ознакомьтесь со статьей [Создание субъекта-службы Azure с помощью Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Разверните Azure Cognitive Services в Azure или Azure Stack Hub.
  - Сначала [узнайте больше о Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).
  - Затем перейдите к статье [Развертывание Azure Cognitive Services в Azure Stack](../user/azure-stack-solution-template-cognitive-services.md), чтобы развернуть Cognitive Services в Azure Stack Hub. Сначала необходимо зарегистрироваться для доступа к предварительной версии.
- Клонируйте или скачайте ненастроенный комплект SDK для искусственного интеллекта Пользовательского визуального распознавания Azure. Дополнительные сведения см. на странице со сведениями о [концепции комплекта SDK для искусственного интеллекта](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Зарегистрируйтесь для использования учетной записи Power BI.
- Ключ подписки API Распознавания лиц Azure Cognitive Services и URL-адрес конечной точки. На странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api) можно получить бесплатную пробную версию. Можно также следовать инструкциям в статье [Create a Cognitive Services resource using the Azure portal](/azure/cognitive-services/cognitive-services-apis-create-account) (Создание ресурса Cognitive Services на портале Azure).
- Установите следующие ресурсы для разработки:
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community)
  - [Porter](https://porter.sh/). Porter используется для развертывания облачных приложений с помощью предоставленных манифестов пакета CNAB.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Azure IoT Tools для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
  - [Расширение Python для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>Развертывание гибридного облачного приложения

Сначала с помощью Porter CLI создайте набор учетных данных, а затем разверните облачное приложение.  

1. Клонируйте или скачайте пример кода решения по ссылке https://github.com/azure-samples/azure-intelligent-edge-patterns. 

1. Porter создаст набор учетных данных, которые будут автоматизировать развертывание приложения. Перед выполнением команды создания учетных данных убедитесь, что у вас есть следующее:

    - Субъект-служба для доступа к ресурсам Azure, включая идентификатор субъекта-службы, ключ и DNS клиента.
    - Идентификатор вашей подписки Azure.
    - Субъект-служба для доступа к ресурсам Azure Stack Hub, включая идентификатор субъекта-службы, ключ и DNS клиента.
    - Идентификатор вашей подписки Azure Stack Hub.
    - Ключ API Распознавания лиц Azure Cognitive Services и URL-адрес конечной точки ресурса.

1. Запустите процесс создания учетных данных Porter и следуйте инструкциям на экране.

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Для выполнения Porter также требуется набор параметров. Создайте текстовый файл параметров и введите приведенные ниже пары "имя — значение". Обратитесь к администратору Azure Stack Hub, если вам нужна помощь по какому-либо из требуемых значений.

   > [!NOTE] 
   > Значение `resource suffix` используется для того, чтобы у ресурсов развертывания были уникальные имена в Azure. Это должна быть уникальная строка из букв и цифр, не длиннее 8 символов.

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Сохраните текстовый файл и запишите его путь.

1. Теперь вы готовы к развертыванию гибридного облачного приложения с помощью Porter. Выполните команду установки и понаблюдайте, как ресурсы развертываются в Azure и Azure Stack Hub:

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. После завершения развертывания запишите следующие значения:
    - строка подключения камеры;
    - строка подключения учетной записи хранилища изображений;
    - имена групп ресурсов.

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>Подготовка комплекта SDK для искусственного интеллекта Пользовательского визуального распознавания

Затем настройте комплект SDK для искусственного интеллекта Пользовательского визуального распознавания, как показано в этом [кратком руководстве](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/). Кроме того, необходимо настроить и протестировать камеру, используя строку подключения, полученную на предыдущем шаге.

## <a name="deploy-the-camera-application"></a>Развертывание приложения камеры

С помощью Porter CLI создайте набор учетных данных, а затем разверните приложение камеры.

1. Porter создаст набор учетных данных, которые будут автоматизировать развертывание приложения. Перед выполнением команды создания учетных данных убедитесь, что у вас есть следующее:
    
    - Субъект-служба для доступа к ресурсам Azure, включая идентификатор субъекта-службы, ключ и DNS клиента.
    - Идентификатор вашей подписки Azure.
    - Строка подключения учетной записи хранилища изображений, полученная при развертывании облачного приложения.

1. Запустите процесс создания учетных данных Porter и следуйте инструкциям на экране.

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Для выполнения Porter также требуется набор параметров. Создайте текстовый файл параметров и введите следующий текст. Обратитесь к администратору Azure Stack Hub, если вы не знаете какие-либо из требуемых значений.

    > [!NOTE]
    > Значение `deployment suffix` используется для того, чтобы у ресурсов развертывания были уникальные имена в Azure. Это должна быть уникальная строка из букв и цифр, не длиннее 8 символов.

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Сохраните текстовый файл и запишите его путь.

4. Теперь вы готовы к развертыванию приложения камеры с помощью Porter. Выполните команду установки и понаблюдайте, как будет создано развертывание IoT Edge.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Убедитесь, что развертывание камеры завершено, просмотрев кадры с камеры по адресу `https://<camera-ip>:3000/`, где `<camara-ip>` является IP-адресом камеры. Этот шаг может занять до 10 минут.

## <a name="configure-azure-stream-analytics"></a>Настройка Azure Stream Analytics

Теперь, когда данные передаются в Azure Stream Analytics с камеры, необходимо вручную авторизовать их для взаимодействия с Power BI.

1.  На портале Azure откройте **Все ресурсы**, а также задание *process-footfall\[ваш_суффикс\]* .

2.  В разделе **Топология задания** области задания Stream Analytics выберите вариант **Выходные данные**.

3.  Выберите приемник выходных данных **traffic-output**.

4.  Щелкните **Обновить авторизацию** и войдите в учетную запись Power BI.
    
    ![запрос на обновление авторизации](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  Сохраните параметры вывода.

6.  Перейдите на панель **Обзор** и выберите **Запуск**, чтобы начать отправку данных в Power BI.

7.  Выберите время начала создания выходных данных задания **Сейчас**, а затем — **Запуск**. Вы можете просматривать состояние задания на панели уведомлений.

## <a name="create-a-power-bi-dashboard"></a>Создание панели мониторинга Power BI

1.  После успешного выполнения задания перейдите на сайт [Power BI](https://powerbi.com/) и войдите с помощью рабочей или учебной учетной записи. Если запрос задания Stream Analytics выдает результаты, то созданный набор данных *footfall-dataset* находится на вкладке **Наборы данных**.

2.  В рабочей области Power BI выберите **+ Создать**, чтобы создать панель мониторинга с именем *Анализ посещаемости*.

3.  В верхней части окна щелкните **Добавить плитку**. Затем выберите **Пользовательские данные потоковой передачи** и нажмите кнопку **Далее**. Выберите **footfall-dataset** в разделе **Ваши наборы данных**. Выберите **Карта** из раскрывающегося списка **Тип визуализации** и добавьте **age** в раздел **Поля**. Нажмите кнопку **Далее**, чтобы ввести имя для плитки, а затем выберите **Применить** для создания плитки.

4.  При необходимости можно добавить дополнительные поля и карты.

## <a name="test-your-solution"></a>Тестирование решения

Обратите внимание, как изменяются данные в картах, созданных в Power BI, когда перед камерой проходят разные люди. Отображение вывода после записи может занять до 20 секунд.

## <a name="remove-your-solution"></a>Удаление решения

Если вы хотите удалить решение, выполните следующие команды с помощью Porter, используя те же файлы параметров, которые были созданы для развертывания: 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [рекомендациях по проектированию гибридных облачных приложений](overview-app-design-considerations.md).
- Изучите и предложите улучшения для [кода в этом примере на сайте GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis).
