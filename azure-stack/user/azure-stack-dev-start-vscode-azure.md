---
title: Подключение к Azure Stack Hub с помощью расширения учетной записи Azure в Visual Studio Code
description: Узнайте, как подключаться к Azure Stack Hub с помощью расширения учетной записи Azure в Visual Studio Code.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: cbcb238e644295e1a66f4eb061d1327fdba3fd13
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884870"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>Подключение к Azure Stack Hub с помощью расширения учетной записи Azure в Visual Studio Code

В этой статье описано, как подключиться к Azure Stack Hub с помощью расширения учетной записи Azure. Вам нужно обновить параметры Visual Studio Code (VS Code).

VS Code — это упрощенный редактор для создания и отладки облачных приложений и веб-приложений. VS Code используют разработчики ASP.NET Core, Python, NodeJS, Go и др. С помощью расширения учетной записи Azure можно использовать единый вход Azure с фильтрацией подписок для дополнительных расширений Azure. Эти расширения позволяют использовать Azure Cloud Shell в интегрированном терминале VS Code. Используя это расширение, вы можете подключить свою подписку Azure Stack Hub с помощью Azure AD (Azure AD) и служб федерации Active Directory (AD FS) для диспетчера удостоверений. Вы можете войти в Azure Stack Hub, выбрать свою подписку и открыть новую командную строку в Cloud Shell. 

> [!Note]  
> Приведенные здесь инструкции можно использовать в среде служб федерации Active Directory (AD FS). Вам нужно использовать учетные данные AD FS и конечные точки.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Предварительные требования для расширения учетной записи Azure

1. Среда Azure Stack Hub, начиная с версии 1904.
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Расширение учетной записи Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Подписка Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).

## <a name="steps-to-connect-to-azure-stack-hub"></a>Подключение к Azure Stack Hub

1. Запустите скрипт **Identity** с помощью средств Azure Stack Hub в GitHub.

    - Прежде чем выполнить скрипт, нужно установить и настроить PowerShell для вашей среды. Инструкции приведены в статье [Установка PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md).

    - Сам скрипт **Identity** и инструкции по нему см. в репозитории [AzureStack-Tools/Identity](https://aka.ms/aa6z611).

    - В том же сеансе выполните следующее:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Откройте VS Code.

3. Выберите **Расширения** в области слева.

4. В поле поиска введите `Azure Account`.

5. Выберите **Учетная запись Azure** и щелкните **Установить**.

      ![Использование Visual Studio Code в Azure Stack Hub](media/azure-stack-dev-start-vscode-azure/image1.png)

6. Перезапустите VS Code, чтобы загрузить расширение.

7. Получите метаданные для подключения к Azure Resource Manager в Azure Stack Hub. 
    
    Microsoft Azure Resource Manager — это платформа управления, которая позволяет развертывать, администрировать и отслеживать ресурсы Azure.
    - URL-адрес Resource Manager для Пакета средств разработки Azure Stack (ASDK): `https://management.local.azurestack.external/`. 
    - URL-адрес Resource Manager для интегрированной системы: `https://management.region.<fqdn>/`, где `<fqdn>` — полное доменное имя.
    - Добавьте к URL-адресу следующий текст для доступа к метаданным: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

    Например, URL-адрес для получения метаданных для конечной точки Azure Resource Manager может выглядеть так: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`.

    Сохраните полученные данные JSON. Эти значения потребуются для свойств `loginEndpoint` и `audiences`.

8. Нажмите клавиши **CTRL+SHIFT+P** и выберите **Предпочтения: открыть параметры пользователя (JSON)** .

9. В редакторе кода замените приведенный ниже фрагмент JSON значениями для своей среды и вставьте его в блок параметров.

    - Значения:

        | Параметр | Описание |
        | --- | --- |
        | `tenant-ID` | Значение [идентификатора клиента](../operator/azure-stack-identity-overview.md) Azure Stack Hub. |
        | `activeDirectoryEndpointUrl` | Это URL-адрес из свойства loginEndpoint. |
        | `activeDirectoryResourceId` | Это URL-адрес из свойства audiences.
        | `resourceManagerEndpointUrl` | Это корневой URL-адрес Azure Resource Manager для Azure Stack Hub. | 

    - Во фрагменте кода JSON сделайте следующее:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

10. Сохраните параметры пользователя и еще раз нажмите клавиши **CTRL+SHIFT+P**. Выберите **Azure: войти в облако Azure**. Новый параметр **AzurePPE** отобразится в списке целевых объектов.

11. Выберите **AzurePPE**. В браузере загрузится страница аутентификации. Войдите в конечную точку.

12. Чтобы убедиться в том, что вы вошли в подписку Azure Stack Hub, нажмите клавиши **CTRL+SHIFT+P** и выберите **Azure: выбрать подписку**. Вы увидите, доступна ли ваша подписка.

## <a name="commands"></a>Команды

| Azure войти | Войдите в свою подписку Azure. |
| --- | --- |
| Azure войти с помощью кода устройства | Войдите в подписку Azure с помощью кода устройства. Используйте его в ситуациях, когда команда "Войти" не работает. |
| Azure войти в облако Azure | Войдите в подписку Azure в одном из национальных облаков. |
| Azure выхода поставщика удостоверений | Выйдите из подписки Azure. |
| Azure Выбор пункта "Подписки" | Выберите подписки, с которыми вы будете работать. Расширение отображает только ресурсы в отфильтрованных подписках. |
| Azure Создание учетной записи | Если у вас нет учетной записи Azure, вы можете [зарегистрировать ее](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) и получить 200 долл. США в виде бесплатного кредита. |
| Azure открыть Bash в Cloud Shell | Откройте новый терминал с Bash в Cloud Shell. |
| Azure открыть PowerShell в Cloud Shell | Откройте новый терминал с PowerShell в Cloud Shell. |
| Azure загрузить в Cloud Shell | Загрузите файл в учетную запись хранения Cloud Shell. |

## <a name="next-steps"></a>Дальнейшие действия

[Настройка среды разработки в Azure Stack Hub](azure-stack-dev-start.md)
