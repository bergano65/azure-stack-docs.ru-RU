---
title: Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory (AD FS) | Документация Майкрософт
description: Узнайте, как развернуть Kubernetes в Azure Stack с помощью служб федерации Active Directory (AD FS).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: ca0dd74a08ce1abe454cb497a2569aae0b958d7c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311504"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!Note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии. Сейчас в предварительной версии не поддерживаются сценарии работы с Azure Stack в автономном режиме.

Вы можете следовать инструкциям, описанным в этой статье, чтобы развернуть и настроить ресурсы для Kubernetes. Если в качестве службы управления удостоверениями используются службы федерации Active Directory (AD FS), выполните следующие действия.

## <a name="prerequisites"></a>Предварительные требования 

Прежде чем начать работу, убедитесь в наличии необходимых разрешений и в готовности Azure Stack.

1. Создайте пару открытого и закрытого ключей SSH для входа на виртуальную машину Linux в Azure Stack. При создании кластера вам потребуется открытый ключ.

    Инструкции по создания ключа см. в разделе [SSH Key Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation) (Создание ключа SSH).

1. Убедитесь в наличии действующей подписки на портале клиента Azure Stack, а также достаточного количества общедоступных IP-адресов для добавления новых приложений.

    Кластер не может быть развернут в подписке **администратора** Azure Stack. Необходимо использовать подписку **пользователя**. 

1. В подписке Azure Stack вам необходима служба Key Vault.

1. В Marketplace вам потребуется кластер Kubernetes. 

Если у вас отсутствует элемент Marketplace службы Key Vault и кластера Kubernetes, обратитесь к администратору Azure Stack.

## <a name="create-a-service-principal"></a>Создание субъекта-службы

При использовании AD FS в качестве решения для удостоверений необходимо совместно с администратором Azure Stack настроить субъект-службу. Субъект-служба предоставляет приложению доступ к ресурсам Azure Stack.

1. Администратор Azure Stack предоставляет сертификат и данные для субъекта-службы.

   - Данные субъекта-службы должны выглядеть следующим образом.

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - Сертификат —это файл с расширением `.pfx`. Сертификат будет храниться в хранилище ключей как секрет.

2. Назначьте новому субъекту-службе роль в качестве участника в вашей подписке. Инструкции см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md).

3. Создайте хранилище ключей для сохранения сертификата для развертывания. Используйте следующие скрипты PowerShell вместо портала.

   - Вам понадобятся следующие сведения:

       | Значение | ОПИСАНИЕ |
       | ---   | ---         |
       | Конечная точка Azure Resource Manager | Microsoft Azure Resource Manager — это платформа управления, которая позволяет администраторам развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.<br>Конечная точка в Пакете средств разработки Azure Stack (ASDK) по ссылке: `https://management.local.azurestack.external/`<br>Конечная точка в интегрированных системах по ссылке: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Идентификатор подписки | [Идентификатор подписки](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) для доступа к предложениям в Azure Stack. |
       | Имя пользователя | Используйте только имя пользователя, а не доменное имя и имя пользователя, например `username` вместо `azurestack\username`. |
       | Имя группы ресурсов.  | Имя новой группы ресурсов; вы также можете выбрать имеющуюся. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре. |
       | Имя хранилища ключей | Имя хранилища.<br> Шаблон регулярного выражения: `^[a-zA-Z0-9-]{3,24}$` |
       | Расположение группы ресурсов | Расположение группы ресурсов. Это регион, выбранный для установки Azure Stack. |

   - Откройте PowerShell с помощью командной строки с повышенными привилегиями и [подключитесь к Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Выполните следующий скрипт, используя параметры, обновленные в соответствии с вашими значениями:

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. Передайте сертификат в хранилище ключей.

   - Вам понадобятся следующие сведения:

       | Значение | ОПИСАНИЕ |
       | ---   | ---         |
       | Путь к сертификату | Полное доменное имя или путь файла к сертификату. |
       | Пароль сертификата | Пароль сертификата. |
       | Имя секрета | Имя секрета, которое используется для ссылки на сертификат, хранимый в хранилище. |
       | Имя хранилища ключей | Имя хранилища ключей, созданного в предыдущем шаге. |
       | Конечная точка Azure Resource Manager | Конечная точка в Пакете средств разработки Azure Stack (ASDK) по ссылке: `https://management.local.azurestack.external/`<br>Конечная точка в интегрированных системах по ссылке: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Идентификатор подписки | [Идентификатор подписки](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) для доступа к предложениям в Azure Stack. |

   - Откройте PowerShell с помощью командной строки с повышенными привилегиями и [подключитесь к Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Выполните следующий скрипт, используя параметры, обновленные в соответствии с вашими значениями:

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>Развертывание Kubernetes

1. Откройте [портал Azure Stack](https://portal.local.azurestack.external).

1. Выберите **+Создать ресурс** > **Служба вычислений** > **Кластер Kubernetes**. Нажмите кнопку **Создать**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Основы

1. В колонке "Создание кластера Kubernetes" выберите **Основные сведения**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Выберите идентификатор **подписки**.

1. Введите имя новой группы ресурсов или выберите существующую. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре.

1. Выберите **расположение** группы ресурсов. Это регион, выбранный для установки Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Параметры кластера Kubernetes

1. В колонке "Создание кластера Kubernetes" выберите **Kubernetes Cluster Settings** (Параметры кластера Kubernetes).

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Введите **имя пользователя администратора виртуальной машины Linux**. Это имя пользователя для виртуальных машин Linux, которые являются частью кластера Kubernetes и динамического административного представления.

1. Введите **открытый ключ SSH**, используемый для авторизации на всех компьютерах Linux, созданных как часть кластера Kubernetes и динамического административного представления.

1. Введите **префикс DNS главного профиля**, уникальный для региона. Это уникальное для региона имя, например `k8s-12345`. Рекомендуем выбрать то же имя, что и для группы ресурсов.

    > [!Note]  
    > Для каждого кластера используйте новый уникальный префикс DNS главного профиля.

1. Выберите **Kubernetes Master Pool Profile Count** (Счетчик профилей главного пула Kubernetes). Счетчик содержит количество узлов в главном пуле. Здесь можно указать значение от 1 до 7, но оно должно быть нечетным.

1. Выберите **The VMSize of the Kubernetes master VMs** (Размер главных виртуальных машин Kubernetes).

1. Выберите **Kubernetes Node Pool Profile** (Счетчик профилей узлов Kubernetes). Счетчик содержит число агентов в кластере. 

1. Выберите **профиль хранилища**. Можно выбрать **Blob Disk** (Диск больших двоичных объектов) или **Managed Disk** (Управляемый диск). Так указывается размер виртуальных машин узлов Kubernetes. 

1. Выберите **ADFS** в качестве **системы удостоверений Azure Stack** для установки Azure Stack.

1. Введите **идентификатор клиента субъекта-службы**. Он используется поставщиком облачных служб Azure для Kubernetes. Идентификатор клиента определяется как идентификатор приложения, когда администратор Azure Stack создает субъект-службу.

1. Введите **группу ресурсов Key Vault**, в которой находится хранилище ключей, содержащее сертификат.

1. Введите **имя Key Vault**, имя хранилища ключей, которое содержит сертификат как секрет. 

1. Введите **секрет Key Vault**. Имя секрета, которое ссылается на ваш сертификат.

1. Введите **версию поставщика облачных служб Azure для Kubernetes**. Это версия поставщика Azure для Kubernetes. Azure Stack выпускает специальную сборку Kubernetes для каждой версии Azure Stack.

### <a name="3-summary"></a>3. Сводка

1. Выберите "Сводка". В колонке отобразится сообщение проверки параметров конфигурации кластера Kubernetes.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Проверьте настройки.

3. Щелкните **ОК**, чтобы развернуть кластер.

> [!TIP]  
>  Если у вас есть вопросы о развертывании, вы можете разместить свой вопрос или поискать ответы на вопрос на [форуме Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Дополнительная информация

[Подключение к кластеру](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Получение доступа к панели мониторинга Kubernetes в Azure Stack](azure-stack-solution-template-kubernetes-dashboard.md)