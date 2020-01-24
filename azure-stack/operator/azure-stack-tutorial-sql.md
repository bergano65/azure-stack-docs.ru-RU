---
title: Предоставление баз данных SQL с высоким уровнем доступности в Azure Stack Hub
description: Узнайте, как создать компьютер для размещения поставщика ресурсов SQL Server и высокодоступные базы данных SQL AlwaysOn с помощью Azure Stack Hub.
services: azure-stack
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 408508119a7535467756b0b45fbfeb59fba7fbf1
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023008"
---
# <a name="offer-highly-available-sql-databases"></a>Предоставление высокодоступных баз данных SQL

Оператор Azure Stack Hub может настроить виртуальные машины сервера для размещения баз данных сервера SQL. После успешного создания сервера размещения SQL, который управляется Azure Stack Hub, пользователи, подписанные на службы SQL, могут легко создавать базы данных SQL.

В этой статье показано, как использовать шаблон быстрого запуска Azure Stack Hub для создания [группы доступности AlwaysOn SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), добавить его в качестве сервера размещения SQL Azure Stack Hub, а затем создать базу данных SQL с высоким уровнем доступности.

Освещаются следующие темы:

> [!div class="checklist"]
> * Создание группы доступности AlwaysOn для SQL Server из шаблона.
> * Создание сервера размещения SQL Azure Stack Hub.
> * Создание высокодоступной базы данных SQL

Вы создадите и настроите группу доступности AlwaysOn SQL Server с двумя виртуальными машинами с помощью компонентов, доступных в Azure Stack Hub Marketplace. 

Прежде чем начать, убедитесь, что [поставщик ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md) успешно установлен и что приведенные ниже компоненты доступны в Azure Stack Hub Marketplace.

> [!IMPORTANT]
> Все следующие элементы необходимы для использования шаблона быстрого запуска Azure Stack Hub.

- Образ Marketplace [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer).
- Образ сервера SQL Server 2016 SP1 или SP2 (Enterprise, Standard и Developer) в Windows Server 2016. В этой статье описывается использование образа Marketplace [SQL Server 2016 SP2 Enterprise на базе Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [Расширение IaaS для SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) версии 1.2.30 или более поздней. Расширение IaaS для SQL устанавливает необходимые компоненты, которые требуются элементам Marketplace SQL Server, для всех версий Windows. Оно обеспечивает настройку параметров SQL на виртуальных машинах SQL. Если расширение не установлено в локальной версии Marketplace, подготовка SQL завершится ошибкой.
- [Расширение пользовательских сценариев для Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) версии 1.9.1 или более поздней. Расширение пользовательских сценариев — это инструмент, который можно использовать для автоматического запуска задач настройки после развертывания виртуальных машин.
- [Настройка требуемого состояния PowerShell](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) версии 2.76.0.0 или более поздней. DSC — это платформа для управления в Windows PowerShell, которая предоставляет возможности развертывания данных конфигурации и управления ими для программных служб, а также управления средой, в которой они выполняются.

Сведения о том, как добавлять элементы в Azure Stack Hub Marketplace, см. в [этой обзорной статье](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Создание группы доступности AlwaysOn для SQL Server
Следуйте инструкциям этого раздела, чтобы развернуть группу доступности SQL Server AlwaysOn с помощью [шаблона быстрого запуска Azure Stack Hub sql-2016-alwayson](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Этот шаблон развертывает два экземпляра SQL Server (Enterprise, Standard или Developer) в группе доступности AlwaysOn. Он создает перечисленные ниже ресурсы.

- группу безопасности сети;
- виртуальную сеть;
- четыре учетные записи хранения (одна для Active Directory (AD), вторая для SQL, третья для файлового ресурса-свидетеля и последняя для диагностики виртуальной машины);
- четыре общедоступные IP-адреса (один для AD, два для каждой виртуальной машины SQL и один для общедоступной подсистемы балансировки нагрузки, привязанной к прослушивателю SQL AlwaysOn);
- один внешний балансировщик нагрузки для виртуальных машин SQL с общедоступным IP-адресом, привязанным к прослушивателю SQL AlwaysOn;
- одну виртуальную машину (Windows Server 2016), настроенную в качестве контроллера домена для нового леса с одним доменом;
- две виртуальные машины (Windows Server 2016), кластеризованные и настроенные с SQL Server 2016 SP1 или SP2 Enterprise, Standard или Developer Edition (это должны быть образы Marketplace);
- одну виртуальную машину (Windows Server 2016), настроенную в качестве файлового ресурса-свидетеля для кластера;
- одну группу доступности с виртуальными машинами файлового ресурса-свидетеля и SQL.  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Последовательно выберите **\+** **Создать ресурс** > **Пользовательский**, **Развертывание шаблона**.

   ![Развертывание настраиваемого шаблона](media/azure-stack-tutorial-sqlrp/1.png)


3. В колонке **Настраиваемое развертывание** выберите **Изменить шаблон** > **Шаблон быстрого запуска**, а затем используйте список доступных шаблонов из раскрывающейся списка, чтобы выбрать шаблон **sql-2016-alwayson**, а потом нажмите кнопку **ОК** и щелкните **Сохранить**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Select quickstart template")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. В колонке **Настраиваемое развертывание** выберите **Изменить параметры** и проверьте значения по умолчанию. При необходимости измените значения, чтобы предоставить все требуемые сведения о параметрах, а затем нажмите кнопку **ОК**.<br><br> Сделайте как минимум следующее:

    - Укажите сложные пароли для параметров ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD и SQLAUTHPASSWORD.
    - Введите DNS-суффикс для обратного просмотра строчных букв в параметре DNSSUFFIX (**azurestack.external** для установки ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Edit custom deployment parameters")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. В колонке **Настраиваемое развертывание** выберите подписку для использования и создайте группу ресурсов или выберите имеющуюся группу ресурсов для настраиваемого развертывания.<br><br> Затем выберите расположение группы ресурсов (**локальное** для установки ASDK) и нажмите кнопку **Создать**. Сначала произойдет проверка параметров настраиваемого развертывания, а затем начнется само развертывание.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Create custom deployment")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. На портале администрирования выберите **Группы ресурсов**, а затем имя группы ресурсов, созданной для настраиваемого развертывания (в этом примере **resource-group**). Просмотрите состояние развертывания, чтобы убедиться, что все развертывания выполнены успешно.<br><br>Затем проверьте элементы группы ресурсов и выберите элемент общедоступного IP-адреса **SQLPIPsql\<имя группы ресурсов\>** . Запишите общедоступный IP-адрес и полное доменное имя общедоступного IP-адреса подсистемы балансировки нагрузки. Эти сведения необходимо будет предоставить оператору Azure Stack Hub, чтобы он мог создать сервер размещения SQL с использованием этой группы доступности SQL AlwaysOn.

   > [!NOTE]
   > Развертывание шаблона может занять несколько часов.

   ![Настраиваемое развертывание завершено](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Включение автоматического заполнения
После успешного развертывания и настройки шаблона группы доступности SQL AlwaysON необходимо включить [автоматическое заполнение](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) на каждом экземпляре SQL Server в группе доступности. 

При создании группы доступности с автоматическим заполнением SQL Server автоматически создает вторичные реплики каждой базы данных в группе. При этом для обеспечения высокого уровня доступности баз данных AlwaysOn не требуется вмешательство вручную.

Используйте следующие команды SQL, чтобы настроить автоматическое заполнение для группы доступности AlwaysOn. Замените \<InstanceName\> именем основного экземпляра SQL Server и <availability_group_name> именем соответствующей группы доступности AlwaysOn. 

На основном экземпляре SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Сценарий основного экземпляра SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

На дополнительных экземплярах SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Сценарий дополнительного экземпляра SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Настройка проверки подлинности автономной базы данных
Перед добавлением автономной базы данных в группу доступности необходимо присвоить параметру сервера contained database authentication значение 1 в каждом экземпляре сервера, в котором размещается реплика доступности для группы доступности. Дополнительные сведения см. в статье [Параметр конфигурации сервера "проверка подлинности автономной базы данных"](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Чтобы задать параметр сервера аутентификации автономной базы данных для каждого экземпляра SQL Server в группе доступности, используйте следующие команды:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Настройка аутентификации автономной базы данных](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>Создание сервера размещения SQL Azure Stack Hub
После создания и правильной настройки группы доступности AlwayOn SQL Server оператор Azure Stack Hub должен создать сервер размещения SQL Azure Stack Hub, чтобы предоставить дополнительную емкость для пользователей, которые создают базы данных. 

Обязательно используйте общедоступный IP-адрес или полное доменное имя для общедоступного IP-адреса подсистемы балансировки нагрузки SQL, которые были записаны ранее при создании группы ресурсов для группы доступности SQL AlwaysOn (**SQLPIPsql\<\>имя группы ресурсов**). Кроме того, вам нужны учетные данные аутентификации SQL Server, используемые для доступа к экземплярам SQL в группе доступности AlwaysOn.

> [!NOTE]
> Оператор Azure Stack Hub должен выполнить этот шаг на портале администрирования.

С помощью прослушивателя общедоступного IP-адреса подсистемы балансировки нагрузки группы доступности SQL AlwaysOn и сведений об имени входа для аутентификации SQL оператор Azure Stack Hub может [создать сервер размещения SQL с использованием группы доступности SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Кроме того, убедитесь, что вы создали планы и предложения, чтобы сделать создание базы данных SQL AlwaysOn доступным для пользователей. Оператору необходимо добавить службу **Microsoft.SqlAdapter** к плану и создать квоту специально для высокодоступных баз данных. Дополнительные сведения о создании планов см. в статье [Service, plan, offer, subscription overview](service-plan-offer-subscription-overview.md) (Обзор служб, планов, предложений и подписок).

> [!TIP]
> Службу **Microsoft.SqlAdapter** не можно добавить в планы до [завершения развертывания поставщика ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Создание высокодоступной базы данных SQL
После создания, настройки и добавления группы доступности SQL AlwaysOn в качестве сервера размещения SQL Azure Stack Hub пользователь клиента с подпиской, обеспечивающей возможности базы данных SQL Server, может создать базы данных SQL с поддержкой AlwaysOn, выполнив действия, описанные в этом разделе. 

> [!NOTE]
> Выполните эти шаги из пользовательского портала Azure Stack Hub в качестве пользователя клиента с подпиской, обеспечивающей возможности SQL Server (службу Microsoft.SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Выберите **\+** **Создать ресурс** > **Данные \+ Хранилище**, а затем **База данных SQL**.<br><br>Предоставьте необходимую информацию о свойствах базы данных, включая имя, параметры сортировки, максимальный размер, а также подписку, группу ресурсов и местоположение, используемое для развертывания. 

   ![Создание базы данных SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Выберите **SKU**, а затем соответствующий номер SKU сервера размещения SQL. В этом примере оператор Azure Stack Hub создал номер SKU **Enterprise-HA** для поддержки высокодоступности групп доступности SQL AlwaysOn.

   ![Выбор номера SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Выберите **Вход** > **Create a new login** (Создать новое имя для входа) и укажите учетные данные аутентификации SQL, которые будут использоваться для новой базы данных. По завершении нажмите кнопку **ОК**, а затем — **Создать** для запуска процесса развертывания базы данных.

   ![Создание имени входа](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. После успешного завершения развертывания базы данных SQL просмотрите свойства базы данных, чтобы найти строку подключения, которая будет использоваться для соединения с новой высокодоступной базой данных. 

   ![Просмотр строки подключения](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Дальнейшие действия

[Обновление поставщика ресурсов SQL](azure-stack-sql-resource-provider-update.md)
