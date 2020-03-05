---
title: Развертывание Kubernetes в Azure Stack Hub с помощью служб федерации Active Directory (AD FS)
description: Узнайте, как развернуть Kubernetes в Azure Stack Hub с помощью служб федерации Active Directory (AD FS).
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/20192
ms.openlocfilehash: 5a1b36f7640f3259a4b18f087ebf4bad49f3c4ed
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703678"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Развертывание Kubernetes в Azure Stack Hub с помощью служб федерации Active Directory

> [!Note]  
> Используйте элемент Kubernetes Azure Stack Marketplace для развертывания кластеров в качестве проверки концепции. Для поддерживаемых кластеров Kubernetes в Azure Stack используйте [обработчик AKS](azure-stack-kubernetes-aks-engine-overview.md).

Вы можете следовать инструкциям, описанным в этой статье, чтобы развернуть и настроить ресурсы для Kubernetes. Если в качестве службы управления удостоверениями используются службы федерации Active Directory (AD FS), выполните следующие действия.

## <a name="prerequisites"></a>Предварительные требования 

Прежде чем начать работу, убедитесь в наличии необходимых разрешений и в готовности Azure Stack Hub.

1. Создайте пару открытого и закрытого ключей SSH для входа на виртуальную машину Linux в Azure Stack Hub. При создании кластера вам потребуется открытый ключ.

    Инструкции по создания ключа см. в разделе [SSH Key Generation](azure-stack-dev-start-howto-ssh-public-key.md) (Создание ключа SSH).

1. Убедитесь в наличии действующей подписки на портале клиента Azure Stack Hub, а также достаточного количества общедоступных IP-адресов для добавления новых приложений.

    Кластер не может быть развернут в подписке **администратора** Azure Stack Hub. Необходимо использовать подписку **пользователя**. 

1. Если в Marketplace нет кластера Kubernetes, обратитесь к администратору Azure Stack Hub.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

При использовании AD FS в качестве решения для удостоверений необходимо совместно с администратором Azure Stack Hub настроить субъект-службу. Субъект-служба предоставляет приложению доступ к ресурсам Azure Stack Hub.

1. Администратор Azure Stack Hub предоставляет данные для субъекта-службы. Данные субъекта-службы должны выглядеть следующим образом.

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Назначьте новому субъекту-службе роль в качестве участника в вашей подписке. Инструкции см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Развертывание Kubernetes

1. Откройте [портал Azure Stack Hub](https://portal.local.azurestack.external).

1. Выберите **+Создать ресурс** > **Служба вычислений** > **Кластер Kubernetes**. Нажмите кнопку **создания**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Основы

1. В колонке "Создание кластера Kubernetes" выберите **Основные сведения**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Выберите идентификатор **подписки**.

1. Введите имя новой группы ресурсов или выберите существующую. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре.

1. Выберите **расположение** группы ресурсов. Это регион, выбранный для установки Azure Stack Hub.

### <a name="2-kubernetes-cluster-settings"></a>2. Параметры кластера Kubernetes

1. В колонке "Создание кластера Kubernetes" выберите **Kubernetes Cluster Settings** (Параметры кластера Kubernetes).

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Введите **имя администратора виртуальной машины Linux**. Это имя пользователя для виртуальных машин Linux, которые являются частью кластера Kubernetes и динамического административного представления.

1. Введите **открытый ключ SSH**, используемый для авторизации на всех компьютерах Linux, созданных как часть кластера Kubernetes и динамического административного представления.

1. Введите **префикс DNS главного профиля**, уникальный для региона. Это уникальное для региона имя, например `k8s-12345`. Рекомендуем выбрать то же имя, что и для группы ресурсов.

    > [!Note]  
    > Для каждого кластера используйте новый уникальный префикс DNS главного профиля.

1. Выберите **Kubernetes master pool profile count** (Счетчик профилей главного пула Kubernetes). Счетчик содержит количество узлов в главном пуле. Здесь можно указать значение от 1 до 7, но оно должно быть нечетным.

1. Выберите **The VMSize of the Kubernetes master VMs** (Размер главных виртуальных машин Kubernetes).

1. Выберите **Kubernetes node pool profile count** (Счетчик профилей узлов Kubernetes). Счетчик содержит число агентов в кластере. 

1. Выберите **The VMSize of the Kubernetes master VMs** (Размер виртуальных машин узлов Kubernetes). Так указывается размер виртуальных машин узлов Kubernetes. 

1. Выберите **ADFS** в качестве **системы удостоверений Azure Stack Hub** для установки Azure Stack Hub.

1. Введите **идентификатор клиента субъекта-службы**. Он используется поставщиком облачных служб Azure для Kubernetes. Идентификатор клиента определяется как идентификатор приложения, когда администратор Azure Stack Hub создает субъект-службу.

1. Введите **секрет клиента субъекта-службы**. Это секрет клиента, который вам предоставил администратор Azure Stack Hub, для субъекта-службы AD FS.

1. Введите **версию Kubernetes**. Это версия поставщика Azure для Kubernetes. Azure Stack Hub выпускает специальную сборку Kubernetes для каждой версии Azure Stack Hub.

### <a name="3-summary"></a>3. Сводка

1. Выберите "Сводка". В колонке отобразится сообщение проверки параметров конфигурации кластера Kubernetes.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Проверьте настройки.

3. Щелкните **ОК**, чтобы развернуть кластер.

> [!TIP]  
>  Если у вас есть вопросы о развертывании, вы можете разместить свой вопрос или поискать ответы на вопрос на [форуме Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Дальнейшие действия

[Подключение к кластеру](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Получение доступа к панели мониторинга Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-dashboard.md)