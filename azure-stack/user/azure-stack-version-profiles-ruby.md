---
title: Использование профилей версий API с помощью Ruby в Azure Stack | Документация Майкрософт
description: Узнайте, как использовать профили версий API с помощью Ruby в Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: d9ef8ab09031db59311317693f72433b63737c34
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814472"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Использование профилей версий API с помощью Ruby в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Использование профилей версии API с помощью Ruby

Пакет SDK Ruby для Resource Manager Azure Stack предоставляет средства для создания и администрирования инфраструктуры. Поставщики ресурсов в пакете SDK включают в себя вычисления, виртуальные сети и хранилища на языке Ruby. Профили API в пакете SDK Ruby позволяют выполнять разработку гибридных облаков приложений, помогая переключаться между глобальными ресурсами Azure и ресурсами в Azure Stack.

Профиль API состоит из поставщиков ресурсов и версий службы. Профиль API можно использовать для объединения разных типов ресурсов.

- Чтобы получить последние версии всех служб, используйте **последний** профиль накопительного пакета Azure SDK.
- Чтобы использовать службы, совместимые с Azure Stack, используйте профиль версии **V2019_03_01_Hybrid** или **V2018_03_01** накопительного пакета Azure SDK.
- Чтобы использовать последнюю **версию API** службы, используйте **последний** профиль определенного пакета. Например, чтобы использовать только последнюю **версию API** службы вычислений, используйте профиль **Latest** пакета **Compute**.
- Чтобы использовать определенную **версию API** для службы, используйте нужные версии API, определенные в пакете.

> [!NOTE]
> Можно объединить все параметры в одном приложении.

## <a name="install-the-azure-ruby-sdk"></a>Установка пакета Azure SDK

- Выполните официальные инструкции по установке Git, указанные [здесь](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Выполните официальные инструкции по установке Ruby, указанные в [этой статье](https://www.ruby-lang.org/en/documentation/installation/).
  - При установке выберите **Добавить Ruby в переменную PATH**.
  - В ходе установки Ruby установите комплект разработки, когда появится соответствующий запрос.
  - Затем установите средство увязки, выполнив следующую команду. 

       ```Ruby
       Gem install bundler
       ```

- Создайте подписку, если ее еще нет, и сохраните ее идентификатор для дальнейшего использования. Инструкции по созданию подписки см. в статье [Создание подписок для предложений в Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
- Создайте субъект-службу и сохраните его идентификатор и секрет. Инструкции по созданию субъекта-службы для Azure Stack см. в [этой статье](../operator/azure-stack-create-service-principals.md).
- Убедитесь, что субъекту-службе назначена роль участника или владельца в вашей подписке. Инструкции по назначению роли субъекту-службе см. в [этой статье](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Установка пакетов RubyGem

Можно установить пакеты Azure RubyGem напрямую.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

Или укажите их в Gemfile.

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Пакет SDK Ruby для Azure Resource Manager доступен в режиме предварительной версии, следовательно, в будущих выпусках ожидаются критические важные изменения интерфейса. Большое число в дополнительном номере версии может указывать на критически важные изменения.

## <a name="use-the-azure_sdk-gem"></a>Использование пакета azure_sdk

Пакет **azure_sdk** — это коллекция всех поддерживаемых пакетов в составе SDK для Ruby. Этот пакет включает  **последний**  профиль, который поддерживает последнюю версию всех служб. Он включает профиль версий  **V2017_03_09** и **V2019_03_01_Hybrid**, созданный для Azure Stack.

Чтобы установить накопительный пакет azure_sdk, выполните следующую команду:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать пакет SDK Azure для Ruby и Azure Stack, укажите следующие значения и задайте значения для переменных среды. Чтобы настроить переменные среды, воспользуйтесь инструкциями, которые указаны после таблицы для используемой операционной системы.

| Значение | Переменные среды | ОПИСАНИЕ |
| --- | --- | --- |
| Tenant ID | `AZURE_TENANT_ID` | [Идентификатор клиента](../operator/azure-stack-identity-overview.md) Azure Stack. |
| Идентификатор клиента | `AZURE_CLIENT_ID` | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи.  |
| Идентификатор подписки | `AZURE_SUBSCRIPTION_ID` | [Идентификатор подписки](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) используется для доступа к предложениям в Azure Stack. |
| Секрет клиента | `AZURE_CLIENT_SECRET` | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы. |
| Конечная точка Resource Manager | `ARM_ENDPOINT` | Дополнительные сведения см. в разделе [Конечная точка Resource Manager для Azure Stack](#the-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Конечная точка Resource Manager для Azure Stack

Microsoft Azure Resource Manager — это платформа управления, которая дает администраторам возможность развертывать, администрировать и отслеживать ресурсы Azure. Azure Resource Manager может обрабатывать эти задачи в рамках одной операции как группы, а не по отдельности.

Получить метаданные можно из конечной точки Resource Manager. Конечная точка возвращает JSON-файл со сведениями, необходимыми для запуска вашего кода.

 > [!NOTE]  
 > **ResourceManagerUrl** в Пакете средств разработки Azure Stack (ASDK): `https://management.local.azurestack.external/`. **ResourceManagerUrl** в интегрированных системах: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.  
 > Чтобы получить необходимые метаданные, используйте: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.
  
 Пример JSON-файла:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Настройка переменных среды

#### <a name="microsoft-windows"></a>Microsoft Windows

Используйте приведенный ниже формат, чтобы настроить переменные среды в командной строке Windows.

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>MacOS, Linux и системы на основе Unix

В системах на основе Unix используйте такую команду:

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Существующие профили API

Накопительный пакет **Azure_sdk** содержит следующие 3 профиля.

- **V2019_03_01_Hybrid**. Профиль, созданный для Azure Stack. Используйте этот профиль для всех последних версий служб, доступных в Azure Stack с версией 1904 или более поздней.
- **V2017_03_09**. Профиль, созданный для Azure Stack. Используйте этот профиль, чтобы обеспечить наибольшую совместимость служб и Azure Stack с версией 1808 или более ранней.
- **Latest**: Этот профиль включает последние версии всех служб. Используйте последние версии всех служб.

Получите дополнительные сведения о профилях API и Azure Stack, ознакомившись с разделом [Сводка по профилям API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Использование профиля API пакета SDK Azure для Ruby

Чтобы создать экземпляр профиля клиента, используйте следующий код. Этот параметр требуется только для Azure Stack и других частных облаков. В глобальной среде Azure эти параметры используются по умолчанию.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

Профиль клиента можно использовать для доступа к отдельным поставщикам ресурсов, включая вычисления, хранилище и сеть.

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Определение функций параметров среды Azure Stack

Для проверки подлинности субъекта-службы в среде Azure Stack определите конечные точки с помощью `get_active_directory_settings()`. Этот метод использует переменную среды **ARM_Endpoint**, которую вы задали ранее.

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Примеры с профилями API

Вы можете использовать следующие примеры из репозитория GitHub в качестве рекомендаций при создании решений с профилями API Azure Stack и Ruby.

- [Управление ресурсами и группами ресурсов Azure на Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).
- [Управление виртуальными машинами с помощью Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (Пример, использующий версию 2019-03-01-hybrid, которая предназначена для последних версий API, поддерживаемых Azure Stack).
- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment).

### <a name="sample-resource-manager-and-groups"></a>Пример с Resource Manager и группами

Для запуска примера необходимо установить Ruby. При использовании Visual Studio Code также скачайте пакет SDK для Ruby в качестве расширения.

> [!NOTE]  
> Этот пример размещен в репозитории [Hybrid-Resource-Manager-Ruby-Resources-And-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. Клонируйте репозиторий.

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Установите зависимости с помощью пакета.

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Создайте субъект-службу Azure с помощью PowerShell и получите необходимые значения.

   См. дополнительные сведения о [создании субъекта-службы с сертификатом с помощью Azure PowerShell](../operator/azure-stack-create-service-principals.md).

   Требуемые значения:

   - Tenant ID
   - Идентификатор клиента
   - Секрет клиента
   - Идентификатор подписки
   - Конечная точка Resource Manager

   Настройте следующие переменные среды, используя информацию, полученную от созданного субъекта-службы.

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`

   > [!NOTE]  
   > В ОС Windows используйте `set` вместо `export`.

4. Убедитесь, что в качестве переменной расположения задано расположение Azure Stack, например `LOCAL="local"`.

5. Чтобы использовать правильные активные конечные точки при работе с Azure Stack или другими частными облаками, добавьте следующую строку кода.

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. В переменную `options` добавьте параметры Active Directory и базовый URL-адрес для работы с Azure Stack.

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Создайте профиль клиента, который предназначен для профиля Azure Stack.

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Для аутентификации субъекта-службы в среде Azure Stack определите конечные точки с помощью **get_active_directory_settings()** . Этот метод использует переменную среды **ARM_Endpoint**, которую вы задали ранее.

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Запустите пример.

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
- [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)  
