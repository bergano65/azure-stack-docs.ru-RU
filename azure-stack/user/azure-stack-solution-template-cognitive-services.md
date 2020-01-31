---
title: Развертывание Azure Cognitive Services в Azure Stack Hub
description: Сведения о развертывании Azure Cognitive Services в Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: bc8a6745b9c3e4323e37bc3b4a46291955d227a1
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883189"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>Развертывание Azure Cognitive Services в Azure Stack Hub

> [!Note]  
> Azure Cognitive Services предоставляются в Azure Stack Hub в режиме предварительной версии.

В Azure Stack Hub можно использовать Azure Cognitive Services с поддержкой контейнеров. Поддержка контейнеров в Azure Cognitive Services позволяет использовать все многофункциональные интерфейсы API, которые доступны в Azure. Использование контейнеров позволяет гибко выбирать расположения для развертывания и размещения служб, поставляемых в [контейнерах Docker](https://www.docker.com/what-container). Сейчас поддержка контейнеров доступна в виде предварительной версии для нескольких служб Azure Cognitive Services, включая [Компьютерное зрение](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Распознавание лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) и [Распознавание речи](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Контейнеризацией называется такой подход к распространению программного обеспечения, при котором приложение или служба упаковывается в образ контейнера вместе со всеми зависимостями и конфигурациями. Такой образ можно развернуть на узле контейнера с минимальными изменениями. Каждый контейнер изолирован от других контейнеров и базовой операционной системы. Сама система содержит только те компоненты, которые необходимы для запуска образа. Узел контейнера имеет меньший размер, чем виртуальная машина. Из образов вы также можете создавать контейнеры для краткосрочных задач и удалять их сразу, когда они становятся ненужными.

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>Использование контейнеров для Cognitive Services в Azure Stack Hub

- **Контроль над данными**  
  Предоставьте пользователям приложения полный контроль над данными при работе с Cognitive Services. Cognitive Services можно предоставить тем пользователям приложений, у которых нет возможности отправлять данные в глобальную среду Azure или в общедоступное облако.

- **Управление обновлениями модели**  
  Предоставьте пользователям приложений обновленные версии моделей, развернутых в их решении.

- **Переносимая архитектура**  
  Реализуйте возможность создавать переносимую архитектуру приложения, что позволит развернуть решение в общедоступном облаке, локальном частном облаке или пограничной зоне. Контейнеры можно развертывать в Службе Azure Kubernetes, Экземплярах контейнеров Azure или кластере Kubernetes, развернутом в Azure Stack Hub. Дополнительные сведения см. в статье [Развертывание Kubernetes в Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

- **Высокая пропускная способность и минимальные задержки**  
   Предоставьте пользователям приложения возможность масштабировать ресурсы в соответствии с пиковыми колебаниями трафика, чтобы сохранять высокую пропускную способность и низкий уровень задержек. Предоставьте возможность выполнять Cognitive Services в Службе Azure Kubernetes, размещенной в непосредственной физической близости к логике и данным приложений.

Используйте Azure Stack Hub для развертывания контейнеров Cognitive Services в кластере Kubernetes вместе с контейнерами приложений, чтобы обеспечить высокий уровень доступности и гибкое масштабирование. При разработке приложений вы можете сочетать Cognitive Services с компонентами, созданными в Службе приложений и Функциях Azure, или из хранилища BLOB-объектов и баз данных SQL или MySQL.

См. дополнительные сведения о [поддержке контейнеров в Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Развертывание API распознавания лиц Azure

В этой статье описано, как развернуть API распознавания лиц Azure в кластере Kubernetes в Azure Stack Hub. Этот подход можно использовать для развертывания контейнеров других служб Cognitive Services в кластерах Kubernetes в Azure Stack Hub.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы сделайте следующее:

1.  Запросите доступ к реестру контейнеров для извлечения образов контейнеров Распознавания лиц из Реестра контейнеров Azure Cognitive Services. Подробные сведения см. в разделе о [запросе доступа к частному реестру контейнеров](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Подготовьте кластер Kubernetes в Azure Stack Hub. См. дополнительные сведения о [развертывании Kubernetes в Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Создание ресурсов Azure

Создайте ресурс Cognitive Service в Azure для предварительного просмотра контейнеров Распознавания лиц, Распознавания речи или Распознавания текста. Вам потребуется указать ключ подписки и URL-адрес конечной точки из ресурса, чтобы создать экземпляры контейнеров Cognitive Service.

1. Создайте ресурс Azure на портале Azure. Если вы хотите просмотреть контейнеры Распознавания лиц, необходимо сначала создать ресурс Распознавания лиц на портале Azure. Дополнительные сведения см. в [кратком руководстве Создание учетной записи Cognitive Services на портале Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  Ресурс Распознавания лиц или Компьютерного зрения должен использовать ценовую категорию F0.

2. Получите URL-адрес конечной точки и ключ подписки для ресурса Azure. После создания ресурса Azure используйте ключ подписки и URL-адрес конечной точки этого ресурса, чтобы создать экземпляр соответствующего контейнера Распознавания лиц, Распознавания речи или Распознавания текста для предварительной версии.

## <a name="create-a-kubernetes-secret"></a>Создание секрета Kubernetes 

Примените команду Kubectl create secret, чтобы получить доступ к частному реестру контейнеров. Замените `<username>` на имя пользователя, а `<password>` — на пароль из учетных данных, полученных по запросу от команды Azure Cognitive Services.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Подготовка файла конфигурации YAML

Используйте файл конфигурации YAML для простого развертывания службы Cognitive Services в кластере Kubernetes.

Ниже приведен пример YAML-файла, который настраивает развертывание службы распознавания лиц в Azure Stack Hub.

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

В этот YAML-файле конфигурации нужно добавить секрет, который вы использовали для получения образов контейнеров службы Cognitive Services из Реестра контейнеров Azure. Файл секрета используется для развертывания определенной реплики в контейнере. Вы также можете создать подсистему балансировки нагрузки, через которую внешние пользователи смогут обращаться к этой службе.

Сведения о ключевых полях конфигурации:

| Поле | Примечания |
| --- | --- |
| replicaNumber | Определяет исходное количество создаваемых реплик экземпляров. Вы сможете изменить его после развертывания. |
| ImageLocation | Указывает расположение образа контейнера определенной службы Cognitive Services в Реестре контейнеров Azure. Например, для Распознавания лиц: `aicpppe.azurecr.io/microsoft/cognitive-services-face`. |
| BillingURL |URL-адрес конечной точки, который вы получили на шаге [Создание ресурса Azure](#create-azure-resources). |
| ApiKey | Ключ подписки, который вы получили на шаге [Создание ресурса Azure](#create-azure-resources). |
| SecretName | Имя секрета, созданного на шаге [Создание секрета Kubernetes](#create-a-kubernetes-secret). |

## <a name="deploy-the-cognitive-service"></a>Развертывание службы Cognitive Services

Используйте следующую команду для развертывания контейнеров Cognitive Services:

```bash  
    Kubectl apply -f <yamlFineName>
```
Используйте следующую команду, чтобы отслеживать ход развертывания: 
```bash  
    Kubectl get pod - watch
```

## <a name="test-the-cognitive-service"></a>Тестирование службы Cognitive Services

Перейдите на страницу [спецификации OpenAPI](https://swagger.io/docs/specification/about/), используя относительный URI **/swagger** для этого контейнера. Эта спецификация, ранее известная как спецификация Swagger, описывает операции, поддерживаемые созданным контейнером. Например, следующий URI представляет доступ к спецификации OpenAPI контейнеру анализа тональности, который создан в предыдущем примере:

```HTTP  
http:<External IP>:5000/swagger
```

Внешний IP-адрес можно получить с помощью следующей команды: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Работа со службой с помощью Python

Попробуйте проверить работу службы Cognitive Services в Azure Stack Hub, выполнив некоторые простые скрипты Python. Вы можете воспользоваться официальными примерами Python, которые позволяют начать работу со службами [Компьютерное зрение](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Распознавание лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview), [Анализ текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) и [Распознавание речи](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

При использовании приложения Python для проверки служб, запущенных в контейнерах, следует учитывать два важных аспекта: 
1. Службы Cognitive Services в контейнерах не требуют дополнительных ключей для аутентификации, но для пакета SDK необходимо, чтобы этот параметр имел любую строку в качестве значения. 
2. Замените строку base_URL фактическим IP-адресом конечной точки службы.

Ниже представлен пример скрипта Python, в котором используется пакет SDK Python для Распознавания лиц, чтобы обнаружить и выделить лица на изображении:

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Дальнейшие действия

[Установка и запуск контейнеров](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers).

[Создание ресурса распознавания лиц в Azure](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers).

[Установка и запуск контейнеров](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers).

[Install and run LUIS docker containers](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (Установка и запуск контейнеров Docker для LIUS)
