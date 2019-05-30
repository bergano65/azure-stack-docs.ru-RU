---
title: Настройка источников развертывания для служб приложений в Azure Stack | Документация Майкрософт
description: Действия администратора службы для настройки источников развертывания (Git, GitHub, Bitbucket, Dropbox и OneDrive) для служб приложений в Azure Stack
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8512568c709770f736d6740d83578dee7391adff
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269262"
---
# <a name="configure-deployment-sources"></a>Настройка источников развертывания

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Служба приложений в Azure Stack поддерживает развертывание по требованию из систем управления версиями разных поставщиков. Эта возможность позволяет разработчикам развертывать приложения напрямую из репозиториев систем управления версиями. Если пользователям необходимо настроить службу приложений для подключения к своим репозиториям, оператор облака должен сначала настроить интеграцию между службой приложений в Azure Stack и поставщиком системы управления версиями.  

Кроме локальной системы Git поддерживаются следующие поставщики систем управления версиями:

* GitHub
* Bitbucket;
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Просмотр источников развертывания в средстве администрирования службы приложений

1. Войдите на портал администрирования Azure Stack (https://adminportal.local.azurestack.external) с правами администратора служб.
2. Выберите **Все службы**, **Служба приложений**.
    ![Администрирование поставщика ресурсов службы приложений][1]
3. Щелкните **Source control configuration** (Настройка системы управления версиями). Вы можете просмотреть список всех настроенных источников развертывания.
    ![Администрирование поставщика ресурсов службы приложений — Настройка системы управления версиями][2]

## <a name="configure-github"></a>Настройка GitHub

Для выполнения этой задачи вам потребуется учетная запись GitHub. Возможно, правильнее будет использовать корпоративную учетную запись, а не личную.

1. Войдите на сайт GitHub, перейдите к странице https://www.github.com/settings/developers и щелкните **Register a new application** (Зарегистрировать новое приложение).
    ![GitHub — регистрация нового приложения][3]
2. Введите имя в поле **Application name** (Имя приложения), например **Служба приложений в Azure Stack**.
3. Введите значение в поле **Homepage URL** (URL-адрес домашней страницы). URL-адресом домашней страницы должен быть адрес портала Azure Stack. Например, https://portal.local.azurestack.external.
4. Введите текст в поле **Application Description** (Описание приложения).
5. Введите значение в поле **Authorization callback URL** (URL-адрес обратного вызова авторизации). В развертывании Azure Stack по умолчанию используется URL-адрес в формате https://portal.local.azurestack.external/TokenAuthorize. Если вы используете другой домен, введите его имя вместо домена local.azurestack.external.
6. Щелкните **Register application** (Зарегистрировать приложение). Откроется страница, на которой вы увидите значения **идентификатора клиента** и **секрета клиента** для приложения.
    ![Приложение зарегистрировано на GitHub][5]
7.  На новой вкладке или в новом окне браузера войдите на портал Azure Stack (https://adminportal.local.azurestack.external) с правами администратора служб.
8.  Перейдите в раздел **Поставщики ресурсов** и выберите элемент **App Service Resource Provider Admin** (Администрирование поставщика ресурсов службы приложений).
9. Щелкните **Source control configuration** (Настройка системы управления версиями).
10. Скопируйте и вставьте **идентификатор клиента** и **секрет клиента** для GitHub в соответствующие поля ввода.
11. Выберите команду **Сохранить**.

## <a name="configure-bitbucket"></a>Настройка Bitbucket

Для выполнения этой задачи вам потребуется учетная запись Bitbucket. Возможно, правильнее будет использовать корпоративную учетную запись, а не личную.

1. Войдите в систему Bitbucket и перейдите к разделу **Integrations** (Интеграция) в вашей учетной записи.
    ![Панель мониторинга Bitbucket — раздел Integrations (Интеграция)][7]
2. Щелкните **OAuth** в списке управления доступом и выберите **Add consumer** (Добавить потребителя).
    ![Добавление потребителя OAuth для Bitbucket][8]
3. Введите имя потребителя в поле **Name** (Имя), например **Служба приложений в Azure Stack**.
4. Введите текст в поле **Description** (Описание) для приложения.
5. Введите значение в поле **Callback URL** (URL-адрес обратного вызова). В развертывании Azure Stack по умолчанию используется URL-адрес обратного вызова в формате https://portal.local.azurestack.external/TokenAuthorize. Если вы используете другой домен, введите его имя вместо домена azurestack.local. Чтобы интеграция с Bitbucket прошла успешно, в точности соблюдайте приведенное здесь написание URL-адреса с учетом регистра.
6. Введите **URL-адрес**. Это должен быть URL-адрес портала Azure Stack, например https://portal.local.azurestack.external.
7. В поле **Permissions** (Разрешения) необходимо выбрать:
    - **Репозитории**: *чтение*
    - **Веб-перехватчики**: *чтение и запись*
8. Выберите команду **Сохранить**. Вы увидите новое зарегистрированное приложение, а также **ключ** и **секрет** для него в разделе **OAuth consumers** (Потребители OAuth).
    ![Список приложений в Bitbucket][9]
9.  На новой вкладке или в новом окне браузера войдите на портал Azure Stack (https://adminportal.local.azurestack.external) с правами администратора служб.
10.  Перейдите в раздел **Resource Providers** (Поставщики ресурсов) и выберите элемент **App Service Resource Provider Admin** (Администрирование поставщика ресурсов службы приложений).
11. Щелкните **Source control configuration** (Настройка системы управления версиями).
12. Скопируйте и вставьте **ключ** для Bitbucket в поле **идентификатора клиента**, а **секрет** — в поле **секрета клиента**.
13. Выберите команду **Сохранить**.

## <a name="configure-onedrive"></a>Настройка OneDrive

Для выполнения этой задачи вам потребуется учетная запись Майкрософт, связанная с учетной записью OneDrive.  Возможно, правильнее будет использовать корпоративную учетную запись, а не личную.

> [!NOTE]
> Учетные записи OneDrive для бизнеса сейчас не поддерживаются.

1. Перейдите к https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm и войдите с учетной записью Майкрософт.
2. В разделе **My applications** (Мои приложения) щелкните **Add an app** (Добавить приложение).
![Приложения OneDrive][10]
3. Введите значение в поле **Name** (Имя) в разделе New Application Registration (Регистрация нового приложения), например **Служба приложений Azure Stack**, а затем щелкните **Создать приложение**.
4. На следующем экране вы увидите список свойств нового приложения. Сохраните **идентификатор приложения** во временное расположение.
![Свойства приложения OneDrive][11]
5. В разделе **Application Secrets** (Секреты приложения) щелкните **Generate New Password** (Создать новый пароль). Запишите **новый созданный пароль**. Это секрет приложения, который не извлекается после нажатия кнопки **ОК**.
6. В разделе **Платформы** щелкните **Добавление платформы** и выберите **Web** (Веб).
7. Введите **URI перенаправления**. В развертывании Azure Stack по умолчанию используется URI перенаправления в формате https://portal.local.azurestack.external/TokenAuthorize. Если вы используете другой домен, введите его имя вместо домена azurestack.local.
![Приложение OneDrive — добавление веб-платформы][12]
8. Добавьте разрешения в разделе **Microsoft Graph Permissions (Разрешения Microsoft Graph)**  - **Delegated Permissions (Делегированные разрешения)** .
    - **Files.ReadWrite.AppFolder**
    - **User. Read**  
      ![Разрешения Graph для приложения OneDrive][13]
9. Выберите команду **Сохранить**.
10.  На новой вкладке или в новом окне браузера войдите на портал Azure Stack (https://adminportal.local.azurestack.external) с правами администратора служб.
11.  Перейдите в раздел **Resource Providers** (Поставщики ресурсов) и выберите элемент **App Service Resource Provider Admin** (Администрирование поставщика ресурсов службы приложений).
12. Щелкните **Source control configuration** (Настройка системы управления версиями).
13. Скопируйте и вставьте **идентификатор приложения** для OneDrive в поле ввода **идентификатора клиента**, а **пароль** — в поле **секрета клиента**.
14. Выберите команду **Сохранить**.

## <a name="configure-dropbox"></a>Настройка Dropbox

> [!NOTE]
> Для выполнения этой задачи вам потребуется учетная запись Dropbox. Возможно, правильнее будет использовать корпоративную учетную запись, а не личную.

1. Перейдите к https://www.dropbox.com/developers/apps и войдите с учетной записью DropBox.
2. Нажмите **Создать приложение**.

    ![Приложения Dropbox][14]

3. Выберите **Dropbox API**.
4. Установите уровень доступа **App Folder** (Папка приложения).
5. Введите значение **Name** (Имя) для приложения.
![Регистрация приложения Dropbox][15]
6. Нажмите кнопку **Create App** (Создать приложение). Вы увидите страницу со списком параметров для приложения, включая **Ключ приложения** и **Секрет приложения**.
7. Убедитесь, что параметр **App folder name** (Имя папки приложения) имеет значение **App Service on Azure Stack** (Служба приложений в Azure Stack).
8. Задайте значение **OAuth 2 Redirect URI** (URI перенаправления OAuth 2), а затем щелкните **Добавить**. В развертывании Azure Stack по умолчанию используется URI перенаправления в формате https://portal.local.azurestack.external/TokenAuthorize. Если вы используете другой домен, введите его вместо домена azurestack.local.
![Настройка приложения Dropbox][16]
9.  На новой вкладке или в новом окне браузера войдите на портал Azure Stack (https://adminportal.local.azurestack.external) с правами администратора служб.
10.  Перейдите в раздел **Resource Providers** (Поставщики ресурсов) и выберите элемент **App Service Resource Provider Admin** (Администрирование поставщика ресурсов службы приложений).
11. Щелкните **Source control configuration** (Настройка системы управления версиями).
12. Скопируйте и вставьте **ключ приложения** для DropBox в поле ввода **идентификатора клиента**, а **секрет приложения** — в поле **секрета клиента**.
13. Выберите команду **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация

Теперь пользователи могут использовать источники развертывания для таких операций, как [непрерывное развертывание](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [локальное развертывание Git](https://docs.microsoft.com/azure/app-service/deploy-local-git) и [синхронизация облачных папок](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
