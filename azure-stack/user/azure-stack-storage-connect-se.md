---
title: Подключение Обозревателя службы хранилища к учетной записи хранения или подписке Azure Stack Hub
description: Сведения о подключении Обозревателя службы хранилища к подписке Azure Stack Hub
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ecf05c089ca193cca3554fc6a8e52e406dce3da2
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704999"
---
# <a name="connect-storage-explorer-to-an-azure-stack-hub-subscription-or-a-storage-account"></a>Подключение Обозревателя службы хранилища к учетной записи хранения или подписке Azure Stack Hub

Из этой статьи вы узнаете, как подключаться к учетным записям хранения и подпискам Azure Stack Hub с помощью [Обозревателя службы хранилища Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer). Обозреватель службы хранилища — это автономное приложение, упрощающее работу с данными хранилища Azure Stack Hub на платформах Windows, macOS и Linux.

> [!NOTE]  
> Для перемещения данных в хранилище Azure Stack Hub и из него доступно несколько инструментов. См. сведения о [средствах для перемещения данных в хранилище Azure Stack Hub и из него](azure-stack-storage-transfer.md).

[Скачайте Обозреватель хранилища](https://www.storageexplorer.com/) и установите его (если он еще не установлен).

Когда вы подключитесь к учетной записи хранения или подписке Azure Stack Hub, для работы с данными Azure Stack Hub можно обращаться к сведениям об [Обозревателе службы хранилища Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer). 

## <a name="prepare-for-connecting-to-azure-stack-hub"></a>Подготовка к подключению к Azure Stack Hub

Чтобы получить доступ к подписке Azure Stack Hub, Обозревателю службы хранилища требуется прямой доступ или VPN-подключение к Azure Stack Hub. См. сведения о том, как [настроить VPN-подключение в Azure Stack Hub](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

> [!Note]  
> Если вы подключаетесь к ASDK через VPN-подключение, не используйте корневой сертификат (CA.cer), созданный во время настройки VPN.  Это DER-шифрованный сертификат, который запрещает Обозревателю службы хранилища получать подписки Azure Stack Hub. Выполните приведенные ниже действия, чтобы экспортировать сертификат в кодировке Base-64 для использования с Обозревателем службы хранилища.

Для неподключенных интегрированных систем и ASDK рекомендуется использовать внутренний центр сертификации предприятия, чтобы экспортировать корневой сертификат в формате Base-64, а затем импортировать его в Обозреватель службы хранилища Azure.  

### <a name="export-and-then-import-the-azure-stack-hub-certificate"></a>Экспорт и импорт сертификата Azure Stack Hub

Экспортируйте и импортируйте сертификат Azure Stack Hub для неподключенных интегрированных систем и ASDK. Для подключенных интегрированных систем сертификат подписывается публично, и этот шаг не является обязательным.

1. Откройте `mmc.exe` на хост-компьютере или локальном компьютере Azure Stack Hub, установив VPN-подключение к Azure Stack Hub. 

2. В меню **Файл** выберите **Добавить или удалить оснастку**. Выберите **Сертификаты** в разделе с доступными оснастками. 

3. Выберите **учетную запись компьютера** и нажмите кнопку **Далее**. Выберите **Локальный компьютер** и нажмите кнопку **Готово**.

4.  Найдите **AzureStackSelfSignedRootCert** в разделе **Console Root (Корень консоли)\Certificated (Local Computer) (Cертифицированный (локальный компьютер))\Trusted Root Certification Authorities (Доверенные корневые центры сертификации)\Сертификаты**.

    ![Загрузка корневого сертификата Azure Stack Hub с помощью mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Щелкните правой кнопкой мыши сертификат, выберите **All Tasks** (Все задачи) > **Export** (Экспорт), а затем следуйте инструкциям, чтобы экспортировать сертификат в виде **файла X.509 (CER) в кодировке Base-64**.

    Экспортированный сертификат будет использоваться на следующем шаге.

6. Запустите Обозреватель хранилища. Закройте диалоговое окно **Подключение к службе хранилища Azure**, если оно отобразится.

7. В меню **Изменить** последовательно выберите **SSL-сертификаты**, **Импорт сертификатов**. Используйте диалоговое окно выбора файла, чтобы найти и открыть сертификат, изученный на предыдущем шаге.

    После импорта сертификата появится запрос на перезапуск Обозревателя службы хранилища.

    ![Импорт сертификата в Обозреватель службы хранилищ](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Перезапустив Обозреватель службы хранилища, откройте меню **Изменить** и проверьте, выбран ли параметр **Целевые API Azure Stack Hub**. Если он не выбран, установите флажок **Целевые API Azure Stack Hub** и перезапустите Обозреватель службы хранилища, чтобы изменения вступили в силу. Это необходимо для обеспечения совместимости со средой Azure Stack Hub.

    ![Проверка параметра "Целевые API Azure Stack Hub"](./media/azure-stack-storage-connect-se/target-azure-stack-new.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-azure-ad"></a>Подключение к подписке Azure Stack Hub с использованием Azure AD

Ниже описано, как подключить Обозреватель хранилища к подписке Azure Stack Hub, которая относится к учетной записи Azure Active Directory (Azure AD).

1. На панели обозревателя хранилища слева выберите **Управление учетными записями**.  
    Отобразятся все подписки Майкрософт, в которые вы вошли.

2. Чтобы подключиться к подписке Azure Stack Hub, щелкните **Добавить учетную запись**.

    ![Добавление учетной записи Azure Stack Hub](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. В диалоговом окне подключения к службе хранилища Azure в разделе **Окружение Azure** выберите **Azure**, **Azure China 21Vianet** (Azure для Китая через 21Vianet), **Azure для Германии**, **Azure для US Gov организаций** или **Добавить новое окружение** в зависимости от используемой учетной записи Azure Stack Hub. Щелкните **Войти**, чтобы войти в учетную запись Azure Stack Hub, связанную по крайней мере с одной активной подпиской Azure Stack Hub.

    ![Подключение к службе хранилища Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Выполнив вход с учетной записью Azure Stack Hub, на панели слева вы увидите подписки Azure Stack Hub, связанные с этой учетной записью. Выберите подписки Azure Stack Hub, с которыми вы планируете работать, а затем щелкните **Применить**. (Чтобы выбрать все подписки Azure Stack Hub или отменить их выбор, используйте флажок **Все подписки**.)

    ![Выбор подписок Azure Stack Hub после заполнения диалогового окна "Пользовательская облачная среда"](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    На панели слева отобразятся все учетные записи хранения, связанные с выбранными подписками Azure Stack Hub.

    ![Список учетных записей хранения, включая учетные записи подписки Azure Stack Hub](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-ad-fs-account"></a>Подключение к подписке Azure Stack Hub с помощью учетной записи AD FS

> [!Note]  
> Для входа в службы федерации Active Directory (AD FS) требуется Обозреватель службы хранилища 1.2.0 и выше с Azure Stack Hub 1804 и выше.
Следуйте приведенным ниже инструкциям, чтобы подключить Обозреватель хранилища к подписке Azure Stack Hub, которая связана с учетной записью AD FS.

1. Выберите **Управление учетными записями**. Отобразится список подписок Майкрософт, в которые вы вошли.
2. Чтобы подключиться к подписке Azure Stack Hub, щелкните **Добавить учетную запись**.

    ![Добавление учетной записи — Обозреватель службы хранилища](media/azure-stack-storage-connect-se/add-an-account.png)

3. Выберите **Далее**. В диалоговом окне подключения к службе хранилища Azure в разделе **Окружение Azure** выберите **Use Custom Environment** (Использовать пользовательское окружение), а затем щелкните **Далее**.

    ![Подключение к службе хранилища Azure](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Укажите необходимые сведения о пользовательском окружении Azure Stack Hub. 

    | Поле | Примечания |
    | ---   | ---   |
    | Имя среды | Это поле может настраивать пользователь. |
    | Конечная точка Azure Resource Manager | Примеры конечной точки ресурсов Azure Resource Manager пакета средств разработки Azure Stack.<br>Для операторов: https://adminmanagement.local.azurestack.external <br> Для пользователей: https://management.local.azurestack.external |

    Если вы работаете в интегрированной системе Azure Stack Hub и не знаете свою конечную точку управления, обратитесь к оператору.

    ![Добавление учетной записи — пользовательские среды](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Щелкните **Войти**, чтобы подключиться к учетной записи Azure Stack Hub, связанной как минимум с одной активной подпиской Azure Stack Hub.



6. Выберите подписки Azure Stack Hub, с которыми вы планируете работать, а затем щелкните **Применить**.

    ![Управление учетными записями](./media/azure-stack-storage-connect-se/account-management.png)

    На панели слева отобразятся все учетные записи хранения, связанные с выбранными подписками Azure Stack Hub.

    ![Список связанных подписок](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-hub-storage-account"></a>Подключение к учетной записи хранения Azure Stack Hub

Для подключения к учетной записи хранения Azure Stack Hub также можно использовать имя учетной записи хранения и пару ключей.

1. На панели обозревателя хранилища слева выберите "Управление учетными записями". Отобразятся учетные записи Майкрософт, в которые вы вошли.

    ![Добавление учетной записи — Обозреватель службы хранилища](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Чтобы подключиться к подписке Azure Stack Hub, щелкните **Добавить учетную запись**.

    ![Добавление учетной записи — подключение к службе хранилища Azure](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. В диалоговом окне "Подключения к службе хранилища Azure" выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения).

4. Введите имя учетной записи в текстовое поле **Имя учетной записи**, а ключ учетной записи — в поле **Ключ учетной записи**. Затем щелкните **Другое (введите ниже)** в разделе **Домен конечных точек хранилища** и укажите конечную точку Azure Stack Hub.

    Конечная точка Azure Stack Hub содержит две части: имя региона и домен Azure Stack Hub. В Пакете средств разработки Azure Stack конечной точкой по умолчанию является **local.azurestack.external**. Если у вас нет сведений о своей конечной точке, обратитесь к администратору облака.

    ![Подключение имени и ключа учетной записи](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Выберите **Подключиться**.
6. После успешного подключения учетная запись хранения появится в списке, а к ее имени будет добавлен текст (**External, Other**) (Внешняя, другая).

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Начало работы с Обозревателем службы хранилища](/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Хранилище Azure Stack Hub. Отличия и рекомендации](azure-stack-acs-differences.md)
* Дополнительные сведения о службе хранилища Azure см. в статье [Введение в хранилище Microsoft Azure](/azure/storage/common/storage-introduction).
