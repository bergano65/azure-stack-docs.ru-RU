---
title: Предварительные условия для развертывания Службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте о том, что нужно выполнить перед развертыванием Службы приложений в Azure Stack.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: a12aceff00cf5be2d6ab70c4957ef04ea1c135d5
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271710"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack"></a>Предварительные условия для развертывания Службы приложений в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Перед развертыванием Службы приложений Azure в Azure Stack необходимо выполнить предварительные действия, описанные в этой статье.

> [!IMPORTANT]
> Прежде чем развернуть Службу приложений Azure 1.6, примените обновление 1904 к интегрированной системе Azure Stack или разверните последний Пакет средств разработки Azure Stack (ASDK).

## <a name="download-the-installer-and-helper-scripts"></a>Скачивание установочного и вспомогательного скриптов

1. Скачайте [службу приложений Azure для вспомогательных скриптов Azure Stack](https://aka.ms/appsvconmashelpers).
2. Скачайте [установщик для развертывания службы приложений Azure в Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Извлеките файлы из ZIP-файла вспомогательных скриптов. Должны быть извлечены следующие файлы и папки:

   - Common.ps1;
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1;
   - Remove-AppService.ps1;
   - Папка для модулей
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Синдикация расширения пользовательских сценариев из Marketplace

Для Службы приложений Azure в Azure Stack требуется расширение пользовательских сценариев версии 1.9.1.  Для этого расширения нужно [выполнить синдикацию из Marketplace](azure-stack-download-azure-marketplace-item.md) до запуска развертывания или обновления Службы приложений Azure в Azure Stack.

## <a name="get-certificates"></a>Получение сертификатов

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Корневой сертификат Azure Resource Manager для Azure Stack

Откройте сеанс PowerShell с повышенными привилегиями на компьютере с доступом к привилегированной конечной точке в интегрированной системе Azure Stack или на узле ASDK.

Выполните скрипт *Get-AzureStackRootCert.ps1* из папки, в которую были извлечены вспомогательные скрипты. Сценарий создает корневой сертификат в той же папке, где расположен сценарий, используемый службой приложений при создании сценариев.

При выполнении приведенной ниже команды PowerShell необходимо указать привилегированную конечную точку и учетные данные для AzureStack\CloudAdmin.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Параметры скрипта Get-AzureStackRootCert.ps1

| Параметр | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Обязательно | AzS-ERCS01 | Привилегированная конечная точка |
| CloudAdminCredential | Обязательно | AzureStack\CloudAdmin | Учетные данные домена администратора облака Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Сертификаты, необходимых для развертывания Службы приложений Azure с помощью ASDK

Скрипт *Create-AppServiceCerts.ps1* работает с центром сертификации Azure Stack для создания четырех сертификатов, необходимых Службе приложений.

| Имя файла | Использование |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | SSL-сертификат по умолчанию службы приложений |
| api.appservice.local.azurestack.external.pfx | SSL-сертификат API службы приложений |
| ftp.appservice.local.azurestack.external.pfx | SSL-сертификат издателя службы приложений |
| sso.appservice.local.azurestack.external.pfx | Сертификат приложения для удостоверения службы приложений |

Чтобы создать сертификаты, выполните указанные ниже действия.

1. Войдите на узел ASDK, используя учетную запись AzureStack\AzureStackAdmin.
2. Откройте сеанс PowerShell с повышенными привилегиями.
3. Выполните скрипт *Create-AppServiceCerts.ps1* из папки, в которую были извлечены вспомогательные скрипты. Этот скрипт создает четыре сертификата в той же папке, где расположен скрипт, который требуется Службе приложений для создания сертификатов.
4. Введите пароль для защиты PFX-файлов и запишите его. Его необходимо ввести в программе установки Azure Stack в Службе приложений.

#### <a name="create-appservicecertsps1-script-parameters"></a>Параметры скрипта Create-AppServiceCerts.ps1

| Параметр | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| pfxPassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата |
| DomainName | Обязательно | local.azurestack.external | Суффикс региона и домена для Azure Stack |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Сертификаты, необходимые для рабочего развертывания Службы приложений Azure в Azure Stack

Для работы поставщика ресурсов в рабочей среде необходимо предоставить следующие сертификаты:

- Сертификат домена по умолчанию
- Сертификат API
- Сертификат для публикации
- Сертификат удостоверения

#### <a name="default-domain-certificate"></a>Сертификат домена по умолчанию

Сертификат домена по умолчанию размещается в роли внешнего интерфейса. Он используется пользовательскими приложениями для групповых запросов или запросов домена по умолчанию к Службе приложений Azure. Сертификат также применяется для операций системы управления версиями (Kudu).

Сертификат должен иметь формат PFX и должен быть групповым сертификатом трех субъектов. Это требование позволяет использовать один сертификат для домена по умолчанию и конечной точки SCM для операций управления версиями.

| Формат | Пример |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>Сертификат API

Сертификат API помещается в роль управления. Он используется поставщиком ресурсов для защиты вызовов API. Сертификат для публикации должен содержать субъект, соответствующий записи API DNS.

| Формат | Пример |
| --- | --- |
| api.appservice.\<регион\>.\<доменное_имя\>.\<расширение\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Сертификат для публикации

Сертификат для роли издателя защищает трафик FTPS для владельцев приложений при загрузке содержимого. Сертификат для публикации должен содержать субъект, соответствующий записи FTPS DNS.

| Формат | Пример |
| --- | --- |
| ftp.appservice.\<регион\>.\<имя домена\>.\<расширение\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Сертификат удостоверения

Сертификат для приложения удостоверения позволяет поддерживает следующие возможности:

- интеграцию между каталогами Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS), Azure Stack и службой приложений для интеграции с поставщиком вычислительных ресурсов;
- сценарии единого входа для расширенных средств разработки в службе приложений Azure в Azure Stack.

Сертификат для идентификации должен содержать субъект, соответствующий приведенному ниже формату.

| Формат | Пример |
| --- | --- |
| sso.appservice.\<регион\>.\<имя домена\>.\<расширение\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Проверка сертификатов

Прежде чем развертывать поставщик ресурсов Службы приложений, [проверьте сертификаты, которые будут использоваться](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation), с помощью средства проверки готовности Azure Stack, которое доступно в [коллекции PowerShell](https://aka.ms/AzsReadinessChecker). Средство проверки готовности Azure Stack проверяет, подходят ли созданные сертификаты PKI для развертывания Службы приложений.

При работе с любым требуемым [сертификатом Azure Stack PKI](azure-stack-pki-certs.md) рекомендуется все спланировать так, чтобы у вас было достаточно времени на тестирование и повторную выдачу сертификатов, если это потребуется.

## <a name="virtual-network"></a>Виртуальная сеть

> [!NOTE]
> Предварительно создавать пользовательскую виртуальную сеть не обязательно, так как Служба приложений Azure в Azure Stack может создать необходимую виртуальную сеть. Но это предполагает обмен данными с SQL Server и файловым сервером через общедоступные IP-адреса.

Служба приложений Azure в Azure Stack позволяет развертывать поставщик ресурсов в имеющейся виртуальной сети или создавать виртуальную сеть в процессе развертывания. Существующая виртуальная сеть позволяет Службе приложений Azure в Azure Stack подключаться к файловому серверу и SQL Server через внутренние IP-адреса. Перед установкой Службы приложений Azure в Azure Stack в виртуальной сети необходимо настроить следующий диапазон адресов и подсети:

Виртуальная сеть /16

Подсети

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Вопросы лицензирования для необходимого файлового сервера и SQL Server

Для работы Службы приложений Azure в Azure Stack требуются файловый сервер и SQL Server.  Вы можете свободно использовать доступные ресурсы, расположенные за пределами развертывания Azure Stack, или развернуть ресурсы в рамках подписки поставщика Azure Stack по умолчанию.

Если вы решили развернуть ресурсы в подписке поставщика Azure Stack по умолчанию, лицензии на эти ресурсы (лицензии Windows Server и SQL Server) будут включены в стоимость Службы приложений Azure в Azure Stack при условии соблюдения следующих ограничений:

- Инфраструктура развернута в **подписке поставщика по умолчанию**.
- Инфраструктура используется исключительно Службой приложений Azure в поставщике ресурсов Azure Stack.  Другие рабочие нагрузки, администраторы (другие поставщики ресурсов, например SQL-RP) или клиенты (например, клиентские приложения, для которых требуется база данных) не могут использовать эту инфраструктуру.

## <a name="prepare-the-file-server"></a>Подготовка файлового сервера

Служба приложений Azure требует использования файлового сервера. Для развертываний в рабочей среде файловый сервер должен быть настроен так, чтобы обеспечивать высокую доступность и обрабатывать сбои.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Шаблон быстрого запуска файлового сервера для развертываний Службы приложений Azure на базе ASDK.

Этот [пример шаблона развертывания Azure Resource Manager](https://aka.ms/appsvconmasdkfstemplate) для развертывания настроенного файлового сервера на одном узле можно использовать только для развертываний ASDK. В рабочей группе будет файловый сервер с одним узлом.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Шаблон быстрого запуска файлового сервера и SQL Server с высоким уровнем доступности

Теперь вам доступен [шаблон быстрого запуска эталонной архитектуры](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha),который развертывает файловый сервер и SQL Server. Этот шаблон поддерживает инфраструктуру Active Directory в виртуальной сети, настроенной для развертывания Службы приложений Azure в Azure Stack с высоким уровнем доступности.

### <a name="steps-to-deploy-a-custom-file-server"></a>Инструкции по развертыванию пользовательского файлового сервера

>[!IMPORTANT]
> Чтобы развернуть Службу приложений в существующей виртуальной сети, файловый сервер необходимо развернуть в отдельной подсети.

>[!NOTE]
> Если вы решили развернуть файловый сервер, используя один из упомянутых выше шаблонов быстрого запуска, этот раздел можно пропустить, так как файловые серверы настроены как часть развертывания шаблона.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Подготовка групп и учетных записей в Active Directory

1. Создайте следующие группы глобальной безопасности Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Создайте следующие учетные записи Active Directory в качестве учетных записей службы:

   - FileShareOwner
   - FileShareUser

   Из соображений безопасности пользователи этих учетных записей (и всех веб-ролей) должны быть уникальными и иметь надежные имена пользователей и пароли. Задайте пароли со следующими условиями:

   - Включите параметр **Срок действия пароля не ограничен**.
   - Включите параметр **Запретить смену пароля пользователем**.
   - Отключите параметр **Требовать смены пароля при следующем входе в систему**.

3. Добавьте учетные записи в группы следующим образом:

   - Добавьте **FileShareOwner** в группу **FileShareOwners**.
   - Добавьте **FileShareUser** в группу **FileShareUsers**.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Подготовка групп и учетных записей в рабочей группе

>[!NOTE]
> При настройке файлового сервера выполните все указанные ниже команды в **окне командной строки с правами администратора**. <br>***Не используйте PowerShell.***

Если вы используете указанный выше шаблон Azure Resource Manager, пользователи уже созданы.

1. Выполните следующие команды, чтобы создать учетные записи FileShareOwner и FileShareUser. Замените `<password>` собственными значениями.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Задайте неограниченный срок действия паролей для учетных записей, выполнив следующие команды WMIC:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Создайте локальные группы FileShareUsers и FileShareOwners и добавьте в них учетные записи из первого шага:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Подготовка общей папки содержимого

В общей папке содержимого размещается содержимое веб-сайта клиента. Процедура подготовки общей папки содержимого на одном файловом сервере одинакова для сред Active Directory и рабочей группы. Однако для отказоустойчивого кластера в Active Directory эта процедура отличается.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Подготовка общей папки содержимого на одном файловом сервере (Active Directory или рабочей группы)

На одном файловом сервере выполните следующие команды в командной строке с повышенными привилегиями. Замените значение `C:\WebSites` соответствующими путями в своей среде.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Настройка управления доступом к общим папкам

Выполните указанные ниже команды в командной строке с повышенными привилегиями на файловом сервере или на узле отказоустойчивого кластера, который является текущим владельцем кластерного ресурса. Замените значения, выделенные курсивом, значениями для конкретной среды.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Рабочая группа

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Подготовка экземпляра SQL Server

>[!NOTE]
> Чтобы развернуть файловый сервер и SQL Server с высоким уровнем доступности, используя шаблон быстрого запуска, этот раздел можно пропустить, так как шаблон развертывает и настраивает SQL Server в конфигурации, обеспечивающей высокую доступность.

Для службы приложений Azure в базах данных размещения и измерений Azure Stack необходимо подготовить экземпляр SQL Server, чтобы разместить базы данных службы приложений.

Чтобы развернуть ASDK, можно использовать SQL Server Express 2014 с пакетом обновления 2 (SP2) или более поздней версии. SQL Server должен поддерживать **смешанный режим** проверки подлинности, так как Служба приложений в Azure Stack **НЕ ПОДДЕРЖИВАЕТ** проверку подлинности Windows.

Для рабочих сред и с целью обеспечения высокого уровня доступности следует использовать полную версию SQL Server 2014 с пакетом обновления 2 (SP2) или более поздней версии, включить аутентификацию в смешанном режиме и выполнить развертывание в [конфигурации, обеспечивающей высокий уровень доступности](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Экземпляр SQL Server для службы приложений Azure в Azure Stack должен быть доступен из всех ролей службы приложений. Вы можете развернуть SQL Server в стандартной подписке поставщика в Azure Stack. Либо можно использовать существующую инфраструктуру в организации (при наличии подключения к Azure Stack). Если вы используете образ Azure Marketplace, не забудьте соответствующим образом настроить брандмауэр.

> [!NOTE]
> Некоторые образы виртуальных машины SQL IaaS доступны через функцию управления Marketplace. Перед развертыванием виртуальной машины, используя элемент Marketplace, всегда скачивайте последнюю версию расширения SQL IaaS. Образы SQL совпадают с виртуальными машинами SQL, которые доступны в Azure. Для виртуальных машин SQL, созданных на основе этих образов, расширение IaaS и соответствующие усовершенствования портала предоставляют функции, такие как автоматическая установка исправлений и резервное копирование.
>
> Для любой роли SQL Server можно использовать экземпляр по умолчанию или именованный экземпляр. Если используется именованный экземпляр, вручную запустите службу обозревателя SQL Server и откройте порт 1434.

Установщик Службы приложений выполнит проверку на наличие автономности базы данных в SQL Server. Чтобы включить автономность базы данных в SQL Server, где будут размещаться базы данных Службы приложений, выполните следующие команды SQL:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> Чтобы развернуть Службу приложений в существующей виртуальной сети, SQL Server нужно развернуть в отдельной подсети.
>

## <a name="create-an-azure-active-directory-app"></a>Создание приложения Azure Active Directory

Настройте субъект-службу Azure AD для поддержки следующих операций:

- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала Функций Azure и расширенные средства разработчика.

Эти шаги применимы только к средам Azure Stack, защищенным с помощью Azure AD.

Администраторы должны настроить единый вход, чтобы:

- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\AzureStackAdmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](azure-stack-app-service-before-you-get-started.md).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4. Запустите скрипт **Create-AADIdentityApp.ps1**. Когда появится запрос, введите идентификатор клиента Azure AD, используемый для развертывания Azure Stack. Например, введите **myazurestack.onmicrosoft.com**.
5. В окне **Учетные данные** введите учетную запись администратора службы Azure AD и пароль. Нажмите кнопку **ОК**.
6. Введите путь к файлу сертификата и пароль для [сертификата, созданного ранее](azure-stack-app-service-before-you-get-started.md). Для этого шага по умолчанию создается сертификат **sso.appservice.local.azurestack.external.pfx**.
7. Этот скрипт создаст приложение в экземпляре клиента Azure AD. Запишите идентификатор приложения, который возвращается в выходных данных PowerShell. Он вам понадобится при установке.
8. Откройте новое окно в браузере и войдите на портал Azure в качестве [администратора службы Azure Active Directory](https://portal.azure.com).
9. Откройте поставщик ресурсов Azure AD.
10. Выберите **Регистрация приложений**.
11. Выполните поиск идентификатора приложения, возвращенного на шаге 7. Вы увидите приложение службы приложений.
12. Выберите **Приложение** в списке.
13. Выберите элемент **Параметры**.
14. Последовательно выберите **Необходимые разрешения** > **Предоставление разрешений** > **Да**.

```powershell
    Create-AADIdentityApp.ps1
```

| Параметр | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| DirectoryTenantName | Обязательно | Null | Идентификатор клиента Azure AD. Введите идентификатор GUID или строку. Пример: myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager администратора. Пример: adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager клиента. Пример: management.local.azurestack.external. |
| AzureStackAdminCredential | Обязательно | Null | Учетные данные администратора службы Azure AD. |
| CertificateFilePath | Обязательно | Null | **Полный путь** к файлу сертификата приложения идентификации, созданному ранее. |
| CertificatePassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата. |
| Среда | Необязательно | AzureCloud; | Имя поддерживаемой облачной среды, в которой доступна целевая служба Azure Active Directory Graph.  Допустимые значения: AzureCloud, AzureChinaCloud, AzureUSGovernment, AzureGermanCloud.|

## <a name="create-an-active-directory-federation-services-app"></a>Создание приложения служб федерации Active Directory

В средах Azure Stack, защищенных с помощью AD FS, необходимо настроить субъект-службу AD FS для поддержки следующих операций:

- интеграция масштабируемых наборов виртуальных машин на уровнях рабочих ролей;
- единый вход для портала Функций Azure и расширенные средства разработчика.

Администраторы должны настроить единый вход, чтобы:

- настроить субъект-службу для интеграции масштабируемого набора виртуальных машин на уровнях рабочих ролей;
- включить расширенные средства разработчиков в рамках службы приложений (Kudu);
- включить использование портала Функций Azure.

Выполните следующие действия.

1. Откройте экземпляр PowerShell с правами azurestack\AzureStackAdmin.
2. Перейдите к расположению скриптов, скачанных и извлеченных на [этапе подготовки](azure-stack-app-service-before-you-get-started.md).
3. [Установите PowerShell для Azure Stack](azure-stack-powershell-install.md).
4. Запустите скрипт **Create-ADFSIdentityApp.ps1**.
5. В окне **Учетные данные** укажите учетную запись администратора облака AD FS и пароль. Нажмите кнопку **ОК**.
6. Предоставьте путь к файлу сертификата и пароль для [сертификата, созданного ранее](azure-stack-app-service-before-you-get-started.md). Для этого шага по умолчанию создается сертификат **sso.appservice.local.azurestack.external.pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Параметр | Обязательный или необязательный | Значение по умолчанию | ОПИСАНИЕ |
| --- | --- | --- | --- |
| AdminArmEndpoint | Обязательно | Null | Конечная точка Azure Resource Manager администратора. Пример: adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Обязательно | Null | Привилегированная конечная точка. Пример: AzS-ERCS01. |
| CloudAdminCredential | Обязательно | Null | Учетные данные домена администратора облака Azure Stack. Пример: Azurestack\CloudAdmin. |
| CertificateFilePath | Обязательно | Null | **Полный путь** к PFX-файлу сертификата приложения идентификации. |
| CertificatePassword | Обязательно | Null | Пароль, который помогает защитить закрытый ключ сертификата. |

## <a name="next-steps"></a>Дополнительная информация

[Установка поставщика ресурсов Службы приложений Azure](azure-stack-app-service-deploy.md)
