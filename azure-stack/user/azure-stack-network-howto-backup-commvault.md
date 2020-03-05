---
title: Как выполнять резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault
description: Узнайте как выполнять резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault.
author: mattbriggs
ms.topic: how-to
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: ec02ccd45773c97df71369b1e325120452ce7da1
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702471"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>Резервное копирование виртуальной машины в Azure Stack Hub с помощью Commvault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Общие сведения о резервном копировании виртуальной машины с помощью Commvault

В этой статье описано, как настроить Commvault Live Sync для обновления виртуальной машины восстановления, расположенной в отдельной единице масштабирования Azure Stack Hub. В этой статье подробно описано, как настроить общее партнерское решение для защиты и восстановления данных и состояния системы виртуальных машин, развернутых в Azure Stack Hub.

На следующей схеме показано общее решение при использовании Commvault для резервного копирования виртуальных машин.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

В этой статье вы узнаете:

1. Создание виртуальной машины, выполняющей программное обеспечение Commvault на исходном экземпляре Azure Stack Hub.

2. Создание учетной записи хранения во вторичном расположении. В статье предполагается, что вы создадите контейнер больших двоичных объектов в учетной записи хранения в отдельном экземпляре Azure Stack Hub (целевом) и что целевой объект Azure Stack Hub доступен из источника Azure Stack Hub.

3. Настройте Commvault на исходном экземпляре Azure Stack Hub и добавьте виртуальные машины в источник Azure Stack Hub в группу виртуальных машин.

4. Настройте Commvault LifeSync.

Вы также можете загрузить и предложить совместимые образы виртуальных машин партнеров для защиты своих виртуальных машин Azure Stack Hub в Azure Cloud или другом Azure Stack Hub. В этой статье показано, как защитить виртуальные машины с помощью Commvault Live Sync.

Топология этого подхода будет выглядеть, как на следующей схеме:

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>Создание виртуальной машины Commvault в виде элемента Commvault Marketplace

1. Откройте портал пользователя Azure Stack Hub.

2. Последовательно выберите **Создать ресурс** > **Вычисления** > **Commvault**.

    > [!Note]  
    > Если Commvault недоступен, обратитесь к оператору облака.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Настройте основные параметры в **Создать виртуальную машину, 1 Основы**:

    а. Введите **Имя**.

    b. Выберите **"Стандартный" HHD**.
    
    c. Введите**имя пользователя**.
    
    d. Введите **пароль**.
    
    д) Подтвердите пароль.
    
    е) Выберите **подписку** для резервной копии.
    
    ж. Выберите **группу ресурсов**.
    
    h. Выберите **расположение** Azure Stack Hub. Если вы используете ASDK, выберите **локальный**.
    
    i. Щелкните **ОК**.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Выберите размер виртуальной машины Commvault. Размер виртуальной машины для резервного копирования должен составлять не менее 10 ГБ ОЗУ и 100 ГБ памяти.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Выберите параметры виртуальной машины Commvault.

    а. Установите для уровня доступности значение **НЕТ**.
    
    b. Выберите **Да** для использования управляемых дисков.
    
    c. Выберите виртуальную сеть по умолчанию для пункта **Виртуальная сеть**.
    
    d. Выберите **Подсеть** по умолчанию.
    
    д) Выберите **Общедоступный IP-адрес** по умолчанию.
    
    е) Оставьте виртуальную машину в **базовой** группе безопасности сети.
    
    ж. Откройте порты HTTP (80), HTTPS (443), SSH (22) и RDP (3389).
    
    h. Выберите **Нет расширений**.
    
    i. Выберите **Включено** для **Диагностики загрузки**.
    
    j. Оставьте для параметра **Диагностика гостевой ОС** значение **Отключено**.
    
    k. Оставьте **учетную запись для хранения диагностических данных** по умолчанию.
    
    l. Щелкните **ОК**.

6. Проверьте сводку виртуальной машины Commvault после выполнения проверки. Щелкните **ОК**.

## <a name="get-your-service-principal"></a>Получение субъекта-службы

Необходимо знать, является ли ваш менеджер идентификатора Azure AD или AD DFS. В следующей таблице содержатся сведения, необходимые для настройки Commvault в Azure Stack Hub.

| Элемент | Описание | Источник |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| URL-адрес Azure Resource Manager | Конечная точка Resource Manager для Azure Stack Hub. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-hub-resource-manager-endpoint |
| имя приложения; |  |  |
| Идентификатор приложения | Идентификатор приложения субъекта-службы, сохраненный во время создания субъекта-службы в предыдущем разделе этой статьи. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| Идентификатор подписки | Идентификатор подписки используется для доступа к предложениям в Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| Идентификатор клиента (идентификатор каталога) | Идентификатор клиента Azure Stack Hub. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Пароль приложения | Секрет приложения субъекта-службы, сохраненный во время создания субъекта-службы. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>Настройка резервного копирования с помощью консоли Commvault

1. Откройте клиент RDP и подключитесь к виртуальной машине Commavult в Azure Stack Hub. Введите свои учетные данные.

2. Установите Azure Stack Hub PowerShell и средства Azure Stack Hub на виртуальной машине Commvault.

    а. Инструкции по установке PowerShell для Azure Stack Hub см. в [этой статье](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Инструкции по установке средств Azure Stack Hub приведены в статье [Скачивание средств Azure Stack Hub из GitHub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. После установки Commvault на виртуальной машине Commvault откройте консоль Commcell. На начальной консоли выберите **Commvault** > **Commvault Commcell Console** (Консоль Commvault Commcell).

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Настройте репозитории файлов резервной копии, чтобы использовать внешнее хранилище для Azure Stack Hub в консоли Commvault Commcell. В браузере CommCell выберите Ресурсы хранилища > Пулы носителей. Щелкните правой кнопкой мыши и выберите **Добавить пул носителей.** Выберите **Облако**.

5. Добавьте имя пула носителей. Выберите **Далее**.

6. Выберите **Создать** > **Облачное хранилище**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Выберите поставщика облачных услуг. В этой процедуре мы будем использовать вторую Azure Stack Hub в другом расположении. Выберите Службу хранилища Microsoft Azure.

8. Выберите виртуальную машину Commvault в качестве MediaAgent.

9. Введите сведения для доступа к вашей учетной записи хранения. Инструкции по настройке учетной записи Службы хранения Azure можно найти здесь. Сведения о доступе:

    -  **Узел службы**. Получите имя URL-адреса из свойств контейнера больших двоичных объектов в вашем ресурсе. Например, мой URL-адрес имел вид https:\//backuptest.blob.westus.stackpoc.com/mybackups, и мной использовался blob.westus.stackpoc.com в узле службы.
    
    -   **Имя учетной записи**: Введите имя учетной записи хранения. Его можно найти в колонке "Ключи доступа" в ресурсе хранилища.
    
    -   **Ключ доступа**. Получите ключ доступа из колонки "Ключи доступа" в ресурсе хранилища.
    
    -   **Контейнер.** Имя контейнера. В данном случае это "mybackups".
    
    -   **Класс хранения**: Оставьте класс хранения по умолчанию для контейнера пользователя.

10. Создайте клиент Microsoft Azure Stack Hub, следуя инструкциям на [этой странице](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm).

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Выберите виртуальные машины или группы ресурсов для защиты и присоедините политику резервного копирования.

12. Настройте расписание резервного копирования в соответствии с требованиями к RPO для восстановления.

13. Выполните первое полное резервное копирование.

## <a name="configure-commvault-live-sync"></a>Настройте Commvault Live Sync 

Доступны два параметра. Вы можете реплицировать изменения с основной копии резервных копий или с вторичной копии на виртуальную машину восстановления. Репликация из резервного набора данных исключает влияние операции ввода-вывода на чтение с исходного компьютера.

1. Во время настройки Live Sync необходимо указать источник Azure Stack Hub (агент виртуального сервера) и сведения о целевом объекте Azure Stack Hub.

2. Действия по настройке Commvault Live Sync см. на странице [Live Sync Replication for Microsoft Azure Stack Hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm) (Репликация Live Sync для Microsoft Azure Stack Hub).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Во время настройки Live Sync (динамической синхронизации) необходимо указать целевой объект Azure Stack Hub и детали об агенте виртуального сервера.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Продолжайте настройку и добавьте целевую учетную запись хранения, в которой будут размещаться диски реплики, группы ресурсов, в которых будут размещены реплики виртуальных машин, и имя, которое вы хотите прикрепить к виртуальным машинам реплики.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. Кроме того, можно изменить размер виртуальной машины и настроить параметры сети, выбрав **Настроить** рядом с каждой виртуальной машиной.

6. Установка частоты репликации целевого объекта Azure Stack Hub

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Проверьте параметры, чтобы сохранить конфигурацию. После этого будет создана среда восстановления, и репликация начнется с выбранным интервалом.


## <a name="set-up-failover-behavior-using-live-sync"></a>Настройка режима отработки отказа с помощью Live Sync

Commvault Live Sync позволяет выполнять отработку отказа компьютеров с одного Azure Stack Hub на другой и восстановление размещения, чтобы возобновить операции на исходном Azure Stack Hub. Рабочий процесс автоматизирован и регистрируется в журнале.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Выберите виртуальные машины, для которых требуется выполнить отработку отказа для восстановления Azure Stack Hub, и выберите плановую или внеплановую отработку отказа. Плановая отработка отказа уместна, когда перед возобновлением работы на сайте восстановления есть время для правильного завершения работы рабочей среды. Плановая отработка отказа завершает работу рабочих виртуальных машин, реплицирует окончательные изменения на сайт восстановления, переводит виртуальные машины восстановления в оперативный режим с последними данными и применяет размер виртуальной машины и конфигурацию сети, указанные во время настройки Live Sync. Внеплановая отработка отказа попытается завершить работу виртуальных машин, но продолжит работу, если рабочая среда недоступна, и просто переведет виртуальные машины восстановления в оперативный режим, используя последний полученный набор данных репликации, примененный к виртуальной машине, а также ранее выбранные размер и конфигурацию сети. На приведенных ниже изображениях показана внеплановая отработка отказа, в которой виртуальные машины восстановления были переведены в оперативный режим с помощью Commvault Live Sync.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Дальнейшие действия

[Сети Azure Stack Hub: различия и рекомендации](azure-stack-network-differences.md)  