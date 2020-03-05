---
title: Создание высокодоступных баз данных SQL
titleSuffix: Azure Stack Hub
description: Узнайте, как создать компьютер для размещения поставщика ресурсов SQL Server и высокодоступные базы данных SQL AlwaysOn с помощью Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: bd62be6a7a2990a7a405dd5c5e1ff44e64007b6f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696821"
---
# <a name="create-highly-available-sql-databases-with-azure-stack-hub"></a>Создание баз данных SQL с высоким уровнем доступности в Azure Stack Hub

Оператор Azure Stack Hub может настроить виртуальные машины сервера для размещения баз данных сервера SQL. Создав сервер для размещения SQL, управление которым осуществляется через Azure Stack Hub, пользователи с подпиской на службы SQL могут легко создавать базы данных SQL.

В этой статье показано, как использовать шаблон быстрого запуска Azure Stack Hub для создания [группы доступности AlwaysOn SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), добавить его в качестве сервера размещения SQL Azure Stack Hub, а затем создать базу данных SQL с высоким уровнем доступности.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание группы доступности AlwaysOn для SQL Server на основе шаблона.
> * Создание сервера для размещения SQL Azure Stack Hub.
> * Создание базы данных SQL с высоким уровнем доступности.

Вы создадите и настроите группу доступности AlwaysOn SQL Server с двумя виртуальными машинами на основе компонентов, доступных в Azure Stack Marketplace.

Прежде чем начать, убедитесь, что [поставщик ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md) успешно установлен и что приведенные ниже компоненты доступны в Azure Stack Marketplace.

> [!IMPORTANT]
> Все следующие элементы необходимы для использования шаблона быстрого запуска Azure Stack Hub.

- Образ Marketplace [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer).
- Образ сервера SQL Server 2016 SP1 или SP2 (Enterprise, Standard и Developer) в Windows Server 2016. В этой статье описывается использование образа Marketplace [SQL Server 2016 SP2 Enterprise на базе Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [Расширение IaaS для SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) версии 1.2.30 или более поздней. Расширение IaaS для SQL устанавливает необходимые компоненты, которые требуются элементам Marketplace SQL Server, для всех версий Windows. Оно обеспечивает настройку параметров SQL на виртуальных машинах SQL. Если расширение не установлено в локальной версии Marketplace, подготовка SQL завершится ошибкой.
- [Расширение пользовательских сценариев для Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) версии 1.9.1 или более поздней. Расширение пользовательских сценариев — это инструмент, который можно использовать для автоматического запуска задач настройки после развертывания виртуальных машин.
- [Настройка требуемого состояния PowerShell](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) версии 2.76.0.0 или более поздней. DSC — это платформа для управления в Windows PowerShell, которая предоставляет возможности развертывания данных конфигурации и управления ими для программных служб. Также она позволяет управлять средой, в которой выполняются эти службы.

Сведения о том, как добавлять элементы в Azure Stack Marketplace, см. в статье [Общие сведения об Azure Stack Hub Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Создание группы доступности AlwaysOn для SQL Server

Следуйте инструкциям этого раздела, чтобы развернуть группу доступности SQL Server AlwaysOn с помощью [шаблона быстрого запуска Azure Stack Hub sql-2016-alwayson](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Этот шаблон развертывает два экземпляра SQL Server (Enterprise, Standard или Developer) в группе доступности AlwaysOn. Он создает перечисленные ниже ресурсы.

- Группа безопасности сети.
- Виртуальная сеть.
- Четыре учетные записи хранения (одна для Active Directory (AD), вторая для SQL, третья для файлового ресурса-свидетеля и последняя для диагностики виртуальной машины).
- Четыре общедоступных IP-адреса (один для AD, по одному для каждой из двух виртуальных машин SQL и один для общедоступной подсистемы балансировки нагрузки, привязанной к прослушивателю SQL AlwaysOn).
- Один внешний балансировщик нагрузки для виртуальных машин SQL с общедоступным IP-адресом, привязанным к прослушивателю SQL AlwaysOn.
- Одна виртуальная машина Windows Server 2016, настроенная в качестве контроллера домена для нового леса с одним доменом.
- две виртуальные машины (Windows Server 2016), кластеризованные и настроенные с SQL Server 2016 SP1 или SP2 Enterprise, Standard или Developer Edition (это должны быть образы Marketplace);
- Одна виртуальная машина (Windows Server 2016) в роли файлового ресурса-свидетеля для кластера.
- Одна группа доступности с виртуальными машинами файлового ресурса-свидетеля и SQL.

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Последовательно выберите **\+** **Создать ресурс** > **Пользовательский**, **Развертывание шаблона**.

   ![Развертывание пользовательского шаблона с помощью портала администрирования Azure Stack Hub](media/azure-stack-tutorial-sqlrp/1.png)

3. В колонке **Настраиваемое развертывание** выберите **Изменить шаблон** > **Шаблон быстрого запуска**, а затем используйте список доступных шаблонов из раскрывающейся списка, чтобы выбрать шаблон **sql-2016-alwayson**. Нажмите кнопку **ОК** и щелкните **Сохранить**.

   [![Изменение шаблона на портале администрирования Azure Stack Hub](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Выбор шаблона быстрого запуска")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. В колонке **Настраиваемое развертывание** выберите **Изменить параметры** и проверьте значения по умолчанию. При необходимости измените значения, чтобы предоставить все требуемые сведения о параметрах, а затем щелкните **ОК**.

    Сделайте как минимум следующее:
    - Укажите сложные пароли для параметров ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD и SQLAUTHPASSWORD.
    - Введите DNS-суффикс для обратного просмотра строчных букв в параметре DNSSUFFIX (**azurestack.external** для установки ASDK).
    
   [![Изменение параметров на портале администрирования Azure Stack Hub](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Изменение параметров настраиваемого развертывания")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. В колонке **Настраиваемое развертывание** выберите подписку для использования и создайте группу ресурсов или выберите имеющуюся группу ресурсов для настраиваемого развертывания.

    Затем выберите расположение группы ресурсов (**локальное** для установки ASDK) и нажмите кнопку **Создать**. Сначала произойдет проверка параметров настраиваемого развертывания, а затем начнется само развертывание.

    [![Выбор подписки на портале администрирования Azure Stack Hub](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Создание настраиваемого развертывания")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)

6. На портале администрирования выберите **Группы ресурсов**, а затем щелкните имя группы ресурсов, созданной для настраиваемого развертывания (в нашем примере это **resource-group**). Просмотрите состояние развертывания, чтобы убедиться, что все развертывания выполнены успешно.
    
    Затем проверьте элементы группы ресурсов и выберите элемент общедоступного IP-адреса **SQLPIPsql\<имя группы ресурсов\>** . Запишите общедоступный IP-адрес и полное доменное имя общедоступного IP-адреса подсистемы балансировки нагрузки. Эти сведения необходимо предоставить оператору Azure Stack Hub, чтобы он мог создать сервер для размещения SQL с использованием этой группы доступности SQL AlwaysOn.

   > [!NOTE]
   > Развертывание шаблона может занять несколько часов.

   ![Настраиваемое развертывание на портале администрирования Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Включение автоматического заполнения

После успешного развертывания и настройки шаблона группы доступности SQL AlwaysON необходимо включить [автоматическое заполнение](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) на каждом экземпляре SQL Server в группе доступности.

Когда создается группа доступности с автоматическим заполнением, SQL Server автоматически создает вторичные реплики каждой базы данных в группе, не требуя никаких действий вручную. Эта мера гарантирует высокий уровень доступности для баз данных AlwaysOn.

Используйте следующие команды SQL, чтобы настроить автоматическое заполнение для группы доступности AlwaysOn. Замените `<InstanceName>` именем основного экземпляра SQL Server, а `<availability_group_name>` — именем соответствующей группы доступности AlwaysOn.

На основном экземпляре SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

![Сценарий основного экземпляра SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

На дополнительных экземплярах SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

![Сценарий дополнительного экземпляра SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Настройка проверки подлинности автономной базы данных

Перед добавлением автономной базы данных в группу доступности необходимо присвоить параметру сервера contained database authentication значение 1 в каждом экземпляре сервера, в котором размещается реплика доступности для группы доступности. Дополнительные сведения см. в статье [Параметр конфигурации сервера "проверка подлинности автономной базы данных"](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Чтобы задать параметр сервера аутентификации автономной базы данных для каждого экземпляра SQL Server в группе доступности, используйте следующие команды:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

![Настройка аутентификации автономной базы данных](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>Создание сервера размещения SQL Azure Stack Hub.

После создания и правильной настройки группы доступности AlwaysOn SQL Server оператор Azure Stack Hub должен создать сервер для размещения SQL Azure Stack Hub. Этот сервер для размещения SQL предоставляет дополнительную емкость для создания баз данных пользователями.

Обязательно используйте общедоступный IP-адрес или полное доменное имя для общедоступного IP-адреса подсистемы балансировки нагрузки SQL, которые были записаны ранее при создании группы ресурсов для группы доступности SQL AlwaysOn (**SQLPIPsql\<\>имя группы ресурсов**). Кроме того, вам нужны учетные данные аутентификации SQL Server, используемые для доступа к экземплярам SQL в группе доступности AlwaysOn.

> [!NOTE]
> Оператор Azure Stack Hub должен выполнить этот шаг на портале администрирования Azure Stack Hub.

Используя общедоступный IP-адрес прослушивателя подсистемы балансировки нагрузки, созданного для группы доступности SQL AlwaysOn и сведения для аутентификации в SQL, оператор Azure Stack Hub может [создать сервер для размещения SQL с использованием группы доступности SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Кроме того, убедитесь, что вы создали планы и предложения, чтобы сделать создание базы данных SQL AlwaysOn доступным для пользователей. Оператору необходимо добавить службу **Microsoft.SqlAdapter** к плану и создать квоту специально для высокодоступных баз данных. Дополнительные сведения о создании планов см. в статье [Service, plan, offer, subscription overview](service-plan-offer-subscription-overview.md) (Обзор служб, планов, предложений и подписок).

> [!TIP]
> Службу **Microsoft.SqlAdapter** нельзя добавить в планы, пока не [завершится развертывание поставщика ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Создание высокодоступной базы данных SQL

Когда оператор Azure Stack Hub завершит создание, настройку и добавление группы доступности SQL AlwaysOn в качестве сервера для размещения SQL Azure Stack Hub, пользователь клиента с подпиской, поддерживающей возможности базы данных SQL Server, может создавать базы данных SQL с поддержкой AlwaysOn. Для создания таких баз данных нужно выполнить действия, описанные далее в этом разделе.

> [!NOTE]
> Выполните эти шаги из пользовательского портала Azure Stack Hub в качестве пользователя клиента с подпиской, обеспечивающей возможности SQL Server (службу Microsoft.SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Выберите **\+** **Создать ресурс** > **Данные \+ Хранилище**, а затем **База данных SQL**.

    Укажите необходимые сведения о свойствах базы данных. Сюда входят имя, параметры сортировки, максимальный размер, а также подписка, группа ресурсов и расположение для развертывания.

   ![Создание базы данных SQL на портале пользователя Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Выберите **SKU**, а затем соответствующий номер SKU сервера размещения SQL. В нашем примере оператор Azure Stack Hub создал SKU **Enterprise-HA** с поддержкой высокого уровня доступности для групп доступности SQL AlwaysOn.

   ![Выбор SKU на пользовательском портале Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Выберите **Вход** > **Create a new login** (Создать новое имя для входа) и укажите учетные данные аутентификации SQL, которые будут использоваться для новой базы данных. По завершении щелкните **ОК**, а затем выберите **Создать**, чтобы запустить развертывание базы данных.

   ![Создание имени для входа на пользовательском портале Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. После успешного завершения развертывания базы данных SQL просмотрите свойства базы данных, чтобы найти строку подключения, которая будет использоваться для соединения с новой высокодоступной базой данных.

   ![Просмотр строки подключения на пользовательском портале Azure Stack Hub](./media/azure-stack-tutorial-sqlrp/createdb4.png)

## <a name="next-steps"></a>Дальнейшие действия

[Обновление поставщика ресурсов SQL](azure-stack-sql-resource-provider-update.md)
