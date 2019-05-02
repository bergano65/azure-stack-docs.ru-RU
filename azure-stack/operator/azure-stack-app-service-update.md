---
title: Обновление Службы приложений Azure в Azure Stack | Документация Майкрософт
description: Подробные инструкции по обновлению Службы приложений в Azure Stack
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: f451d59d11c79198a3bce3ec358769d423948d29
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308870"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Обновление Службы приложений Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!IMPORTANT]  
> Прежде чем развертывать Службу приложений Azure 1.5, примените обновление 1901 к интегрированной системе Azure Stack или разверните последний пакет средств разработки Azure Stack.

Выполнив инструкции в этой статье, вы обновите [поставщик ресурсов Службы приложений](azure-stack-app-service-overview.md), развернутый в среде Azure Stack с подключением к Интернету.

> [!IMPORTANT]  
> Перед выполнением обновления [разверните Службу приложений Azure в поставщике ресурсов Azure Stack](azure-stack-app-service-deploy.md) и ознакомьтесь с [заметками](azure-stack-app-service-release-notes-update-five.md), которые прилагаются к выпуску 1.5, чтобы узнать о новых функциях, исправлениях и любых известных проблемах, которые могут повлиять на развертывание.

## <a name="run-the-app-service-resource-provider-installer"></a>Запуск установщика поставщика ресурсов службы приложений

В ходе обновления выполняются следующие действия:

* Обнаружение предыдущего развертывания Службы приложений.
* Подготовка всех пакетов обновления и новых версий всех библиотек ОС для развертывания.
* Отправка в службу хранилища.
* обновление всех ролей службы приложений (контроллеры, управление, внешний интерфейс, издатель и рабочие роли);
* обновление определений масштабируемого набора службы приложений;
* Обновление манифеста поставщика ресурсов Службы приложений.

> [!IMPORTANT]
> Установщик Службы приложений необходимо запустить на компьютере с доступом к конечной точке Azure Resource Manager учетной записи администратора Azure Stack.
>
>

Чтобы обновить развертывание Службы приложений Azure в Azure Stack, сделайте следующее:

1. Скачайте [установщик Службы приложений](https://aka.ms/appsvcupdate5installer).

2. Запустите файл appservice.exe от имени администратора.

    ![Установщик службы приложений][1]

3. Щелкните **Развернуть Службу приложений или выполнить обновление до последней версии**.

4. Просмотрите и примите условия лицензионного соглашения об использовании программного обеспечения Майкрософт, а затем нажмите кнопку **Далее**.

5. Просмотрите и примите условия лицензии сторонних поставщиков и нажмите кнопку **Далее**.

6. Убедитесь, что конечная точка Azure Resource Manager инфраструктуры Azure Stack и сведения о клиенте Active Directory указаны правильно. Если во время развертывания Пакета средств разработки Azure Stack использовались значения по умолчанию, здесь также можно принять значения по умолчанию. Но если вы указали другие значения при развертывании Azure Stack, в этом окне необходимо изменить значения соответствующим образом. Например, если используется суффикс домена *mycloud.com*, для конечной точки Azure Resource Manager инфраструктуры Azure Stack нужно указать значение *management.region.mycloud.com*. Проверьте сведения и нажмите кнопку **Далее**.

    ![Сведения об облаке Azure Stack][2]

7. На следующей странице:

   1. Нажмите кнопку **Подключить** рядом с полем **Azure Stack Subscriptions** (Подписки Azure Stack).
        * При использовании Azure Active Directory (Azure AD) введите учетную запись администратора Azure AD и пароль, предоставленный при развертывании Azure Stack. Щелкните **Sign In** (Войти).
        * Если вы используете службы федерации Active Directory (AD FS), предоставьте соответствующую учетную запись администратора. Например: *cloudadmin\@azurestack.local*. Введите пароль и нажмите кнопку **Войти**.
   2. В поле **Azure Stack Subscriptions** (Подписки Azure Stack) выберите **Default Provider Subscription** (Подписка поставщика по умолчанию).
   3. В поле **Azure Stack Locations** (Расположения Azure Stack) выберите расположение, соответствующее региону, в который выполняется развертывание. Например, **локальный** регион при развертывании в Пакете средств разработки Azure Stack.
   4. Если обнаружено существующее развертывание службы приложений, поля группы ресурсов и учетной записи хранения заполняются и выделяются серым цветом.
   5. Нажмите кнопку **Далее**, чтобы просмотреть сводку обновления.

      ![Обнаруженная установка службы приложений][3]

8. На странице "Сводка" выполните следующие действия.
   1. Проверьте выбранные параметры. Чтобы внести изменения, используйте кнопки **Назад** для перехода на предыдущие страницы.
   2. Если настройки указаны правильно, установите флажок.
   3. Чтобы начать обновление, нажмите кнопку **Далее**.

       ![Сводка обновления службы приложений][4]

9. На странице хода обновления:
    1. Отслеживайте ход обновления. Длительность обновления службы приложений в Azure Stack зависит от количества развернутых экземпляров ролей.
    2. После успешного обновления щелкните **Выход**.

        ![Ход обновления службы приложений][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Дополнительная информация

Можно также попробовать другие [службы PaaS (платформа как услуга)](azure-stack-offer-services-overview.md).

* [Use SQL databases on Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) (Использование баз данных SQL в Microsoft Azure Stack)
* [Use MySQL databases on Microsoft Azure Stack](azure-stack-mysql-resource-provider-deploy.md) (Использование баз данных MySQL в Microsoft Azure Stack)
