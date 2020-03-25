---
title: Основы администрирования Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Основные сведения об администрировании Azure Stack Hub.
author: nicoalba
ms.topic: article
ms.date: 03/02/2020
ms.author: v-nialba
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8f56dafbcc27e3ff4de9adcfbf5de27dea115bb3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295067"
---
# <a name="azure-stack-hub-administration-basics"></a>Основы администрирования Azure Stack Hub

Если вы еще не знакомы с администрированием Azure Stack Hub, важно знать несколько моментов. В этой статье описана роль оператора Azure Stack Hub и перечислено, что нужно сообщить пользователям для повышения эффективности их работы.

## <a name="understand-the-builds"></a>Сведения о сборках

Для интегрированной системы Azure Stack Hub обновленные версии Azure Stack Hub распространяются через пакеты обновлений. Эти пакеты можно импортировать и применить, используя плитку  **Обновления** на портале администрирования.

## <a name="learn-about-available-services"></a>Дополнительные сведения о доступных службах

Будьте в курсе о том, доступ к каким службам вы можете предоставить своим пользователям. Azure Stack Hub поддерживает различные службы Azure. Список поддерживаемых служб будет постоянно увеличиваться.

### <a name="foundational-services"></a>Базовые службы

При развертывании Azure Stack Hub по умолчанию содержит следующие базовые службы:

- Службы вычислений
- Память
- Сеть
- Key Vault

С этими базовыми службами вы можете предложить своим пользователям инфраструктуру как услугу (IaaS) с минимальной конфигурацией.

### <a name="additional-services"></a>Дополнительные службы

Мы поддерживаем следующие дополнительные службы платформы как услуги (PaaS):

- Служба приложений
- Функции Azure
- Базы данных SQL и MySQL
- Kubernetes
- Центр Интернета вещей
- Концентратор событий

Эти службы нужно дополнительно настроить, прежде чем вы сможете сделать их доступными для пользователей. Дополнительные сведения см. в разделах **Учебники** и **Практические руководства** > **Предложение служб** [документации для оператора Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

### <a name="service-roadmap"></a>Стратегия развития службы

Для Azure Stack Hub будет расширяться поддержка служб Azure. Сведения о предполагаемой стратегии развития см. в техническом документе [Azure Stack Hub. Расширение Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). Кроме того, можно отслеживать [записи блога об Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview), чтобы не пропустить важные новости.

## <a name="what-account-should-i-use"></a>Какую учетную запись следует использовать?

Существуют несколько рекомендаций по использованию учетной записи, на которые следует обратить внимание при управлении Azure Stack Hub. В особенности в развертываниях с использованием служб федерации Windows Server Active Directory в качестве поставщика удостоверений вместо Azure Active Directory (Azure AD).

| **Учетная запись** | **Azure** | **AD FS** |
|---|---|---|
| Локальный администратор (.\Administrator) |   |
| Глобальный администратор Azure AD | Используется в процессе установки. <br> Владелец поставщика по умолчанию | Неприменимо. |
| Учетная запись для расширенного хранилища|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Какие средства необходимо использовать для управления

Для управления Azure Stack Hub можно использовать [портал администрирования](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002) или PowerShell. Ознакомиться с основными принципами проще всего с помощью портала. Если вы хотите использовать PowerShell, необходимо выполнить действия по подготовке. Перед началом работы узнайте о том, как PowerShell используется в Azure Stack Hub. Подробные сведения см. в статье [Начало работы с PowerShell в Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-overview?view=azs-2002).

Azure Stack Hub использует Azure Resource Manager в качестве основного механизма развертывания, организации и управления. Если вы собираетесь управлять Azure Stack Hub и оказывать поддержку пользователям, вам следует узнать о Resource Manager. См. технический документ [Начало работы с Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Ваши основные обязанности

Ваши пользователи хотят использовать службы. Они считают, что предоставить им эти службы — ваша главная обязанность. Решите, какие службы предлагать, и сделайте их доступными, создав планы, предложения и квоты. См. [общие сведения о предложении служб в Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-2002).

Кроме того, необходимо добавить элементы в [Azure Stack Hub Marketplace](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-marketplace?view=azs-2002). Для этого проще всего [скачать элементы из Azure Marketplace в Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2002).

Протестировать планы, предложения и службы можно на [пользовательском портале](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002), а не на портале администрирования.

Кроме предоставления служб, вам необходимо выполнять все обычные обязанности оператора для поддержания работоспособности Azure Stack Hub. В число обязанностей входят следующие задачи.

- Добавление учетных записей пользователей для развертывания [Azure AD](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-add-new-user-aad?view=azs-2002).
- [Настройка прав доступа с помощью управления доступом на основе ролей](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-permissions?view=azs-2002). (Эта задача не ограничивается администраторами.)
- [Мониторинг работоспособности инфраструктуры](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-monitor-health?view=azs-2002).
- Управление ресурсами [сети](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-viewing-public-ip-address-consumption?view=azs-2002) и [хранилища](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-storage-accounts?view=azs-2002).
- [Запуск и остановка Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-start-and-stop?view=azs-2002&branch=release-tzl).
- [Работа с расширенным хранилищем](https://review.docs.microsoft.com/en-us/azure-stack/tdc/extended-storage-operator-guide?view=azs-2002&branch=release-tzl).
- [Управление Центром Интернета вещей](https://review.docs.microsoft.com/en-us/azure-stack/operator/iot-hub-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&.bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Управление концентратором событий](https://review.docs.microsoft.com/en-us/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Управление Службой приложений](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Замена неисправного оборудования Вот список [сменных компонентов](https://review.docs.microsoft.com/en-us/azure-stack/tdc/cru-replaceable-parts?view=azs-2002&branch=release-tzl).
- [Получение поддержки](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Задачи оператора

Вот список ежедневных, еженедельных и ежемесячных задач оператора:

# <a name="daily"></a>[Ежедневно](#tab/daily)

1. Проверка оповещений.
2. Проверка состояния резервного копирования.
3. Обновление сигнатуры Защитника (отключенные системы).
4. Проверка работоспособности системы Isilon и событий в OneFS.
5. Проверка емкости Isilon.

# <a name="weekly"></a>[Еженедельно](#tab/weekly)

1. Проверка емкости.
2. Выполнение `isi status –verbose` при подключении Avocent.

# <a name="monthly"></a>[Ежемесячно](#tab/monthly)

1. Применение ежемесячных пакетов обновлений (Майкрософт и OEM).
2. Проверка резервного копирования с помощью ASDK.
3. Управление Azure Stack Hub Marketplace (поддержка актуальности).
4. Обновление встроенного ПО коммутатора и Avocent.
5. Освобождение емкости хранилища.

# <a name="ondemand"></a>[По требованию](#tab/ondemand)

1. Смена секретов.
2. Создание и обновление предложений, планов и квот.
3. Применение пакетов исправлений.
4. Применение пакетов исправлений.
5. Расширение емкости (узлы и диапазон IP-адресов).
6. Выполнение `isi status –verbose` при подключении Avocent.
7. Восстановление учетных записей хранения.
8. Остановка системы.
9. Сбор журналов диагностики.

---

## <a name="what-to-tell-your-users"></a>Что нужно сообщить пользователям

Необходимо проинформировать пользователей о том, как работать со службами Azure Stack Hub, как подключиться к среде и как подписаться на предложения. Помимо собственной пользовательской документации, которую вы будете предоставлять пользователям, вы можете направить их на сайт с [документацией для пользователей Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/).

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Сведения о работе со службами Azure Stack Hub

Пользователям необходимо узнать некоторые сведения, прежде чем использовать службы и создавать приложения в Azure Stack Hub. Например, есть определенные требования к версии PowerShell и API. Кроме того, функции службы в Azure и эквивалентной службы в Azure Stack Hub несколько отличаются. Пользователи должны ознакомиться со следующими статьями:

- [Различия между Azure и Azure Stack Hub при использовании служб и создании приложений](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-considerations?view=azs-2002)
- [Возможности виртуальных машин Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-vm-considerations?view=azs-2002)
- [Хранилище Azure Stack Hub. Отличия и рекомендации](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-acs-differences?view=azs-2002).

В следующих статьях приведены различия между службой в Azure и Azure Stack Hub. Они дополняют сведения, доступные для службы Azure в глобальной документации Azure.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Подключение к Azure Stack Hub в качестве пользователя

Ваши пользователи захотят узнать, как [получить доступ к пользовательскому порталу](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-use-portal?view=azs-2002) или подключиться через PowerShell. В среде интегрированных систем адрес пользовательского портала зависит от развертывания. Вам понадобится предоставить пользователям правильный URL-адрес.

При использовании PowerShell пользователям может понадобиться зарегистрировать поставщиков ресурсов, чтобы они смогли воспользоваться службами. Поставщик ресурсов управляет службой. Например, поставщик сетевых ресурсов управляет такими ресурсами, как виртуальные сети, сетевые интерфейсы и подсистемы балансировки нагрузки. Им необходимо [установить](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-install?view=azs-2002) PowerShell, [загрузить](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-download?view=azs-2002) дополнительные модули и [настроить](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-configure-user?view=azs-2002) PowerShell (для этого также необходимо зарегистрировать поставщик ресурсов).

### <a name="subscribe-to-an-offer"></a>Подписка на предложение

Прежде чем пользователи смогут получить доступ к службам, им необходимо [подписаться на предложение](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-2002), созданное вами с правами оператора.

## <a name="where-to-get-support"></a>Где можно получить поддержку

Сведения о поддержке ранних выпусков Azure Stack Hub (до версии 1905) см. в статье [Политика обслуживания Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-servicing-policy?view=azs-2002).

В интегрированной системе используется скоординированный процесс рассмотрения и разрешения проблем между корпорацией Майкрософт и нашими партнерами OEM (изготовителями оборудования).

Если проблема возникла с облачными службами, поддержка предоставляется через службу поддержки корпорации Майкрософт (CSS). Чтобы подать запрос на поддержку, нажмите на значок справки и поддержки (вопросительный знак) в правом верхнем углу окна портала администрирования. Затем выберите элемент **Справка + поддержка**, а после этого — элемент **Новый запрос на поддержку** в разделе **Поддержка**.

Если проблема возникла с развертыванием, исправлением, обновлением, оборудованием (в том числе с модулями, заменяемыми на месте) и любым программным обеспечением поставщика оборудования (например, ПО в узле поддержки жизненного цикла оборудования), сначала обратитесь к поставщику оборудования OEM.

При возникновении других проблем обратитесь в службу поддержки корпорации Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление регионами в Azure Stack Hub](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-region-management?view=azs-2002)
