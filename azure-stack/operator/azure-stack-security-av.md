---
title: Обновление антивирусной программы "Защитник Windows"
titleSuffix: Azure Stack Hub
description: Сведения об обновлении антивирусной программы "Защитник Windows" в Azure Stack Hub
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 8f0ad292f8d9772c53c332d2cad7af8bd606594a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697745"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>Обновление антивирусной программы "Защитник Windows" в Azure Stack Hub

[Антивирусная программа "Защитник Windows"](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) — это решение для защиты от вредоносных программ, которое обеспечивает безопасность и защиту от вирусов. Каждый компонент инфраструктуры в Azure Stack Hub (узлы Hyper-V и виртуальные машины) защищен с помощью антивирусной программы "Защитник Windows". Для актуальной защиты требуется периодически обновлять определения, модуль и платформу антивирусной программы "Защитник Windows". Применение обновлений зависит от конфигурации.

## <a name="connected-scenario"></a>Сценарий с подключением

[Поставщик ресурсов "Обновление"](azure-stack-updates.md#the-update-resource-provider) в Azure Stack Hub загружает обновления определений и модуля защиты от вредоносных программ несколько раз в день. Каждый компонент инфраструктуры в Azure Stack Hub получает обновление из поставщика ресурсов "Обновление" и применяет его автоматически.

Для развертываний Azure Stack Hub c подключением к общедоступному Интернету примените [ежемесячное обновление Azure Stack Hub](azure-stack-apply-updates.md). Ежемесячное обновление Azure Stack Hub содержит обновления платформы антивирусной программы "Защитник Windows" за текущий месяц.

## <a name="disconnected-scenario"></a>Сценарии без подключения

Для тех развертываний Azure Stack Hub, которые не подключены к общедоступному Интернету (например, к центрам обработки данных, отключенным от Интернета), начиная с выпуска 1910, клиенты могут применять определения и обновления антивредоносных программ и обновлений подсистемы по мере их публикации. 

Чтобы применить обновления к решению Azure Stack Hub, сначала необходимо скачать их с сайта Майкрософт (по ссылкам ниже), а затем импортировать их в контейнер BLOB-объектов хранилища в *updateadminaccount*. Запланированная задача сканирует контейнер BLOB-объектов каждые 30 минут и, если обнаружены новые определения и обновления модуля Защитника, они применяются к инфраструктуре Azure Stack Hub. 

Для таких отключенных развертываний, которые еще не включены в выпуск 1910 или более позднюю версию, или которые не могут ежедневно скачивать определения и обновления модуля Защитника, ежемесячное обновление Azure Stack Hub включает в себя определения и обработчики вирусов Защитника Windows и обновления платформы за месяц. 


### <a name="set-up-windows-defender-for-manual-updates"></a>Настройка Защитника Windows для обновления вручную 

В выпуске 1910 в привилегированную конечную точку были добавлены два новых командлета для настройки ручного обновления Защитника Windows в Azure Stack Hub. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

В следующей процедуре показано, как настроить обновление Защитника Windows вручную. 

1. Подключитесь к привилегированной конечной точке и выполните следующий командлет, чтобы указать имя контейнера больших двоичных объектов хранилища, в который будут отправляться обновления Защитника. 

   > [!NOTE] 
   > Описанный ниже процесс обновления вручную работает только в отключенных средах, где доступ к go.microsoft.com запрещен. Попытка выполнить командлет Set-AzsDefenderManualUpdate в подключенных средах приведет к ошибке. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Скачайте два пакета обновления Защитника Windows и сохраните их в расположении, доступном на портале администрирования Azure Stack Hub.  

   * mpam-fe.exe с сайта [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full.exe с сайта [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > Эти два файла необходимо скачивать **каждый раз** при обновлении сигнатур Защитника. 

3. На портале администратора щелкните **Все службы**. После этого в категории **Данные+хранилище** выберите **Учетные записи хранения**. (Или начните вводить текст **учетные записи хранения** в поле ввода и выберите найденный элемент.) 

   ![Защитник Azure Stack Hub — все службы](./media/azure-stack-security-av/image1.png)  

4. В поле фильтра введите текст **update** и выберите учетную запись хранения **updateadminaccount**. 

5. В разделе **Службы** сведений об учетной записи хранения выберите **BLOB-объекты**. 

   ![Защитник Azure Stack Hub — большой двоичный объект](./media/azure-stack-security-av/image2.png) 

6. В разделе **Служба BLOB-объектов** выберите **+ Контейнер**, чтобы создать контейнер. Введите имя, указанное в команде Set-AzsDefenderManualUpdate (в этом примере *defenderupdates*), а затем нажмите кнопку **ОК**. 

   ![Защитник Azure Stack Hub — контейнер больших двоичных объектов](./media/azure-stack-security-av/image3.png) 

7. После создания контейнера щелкните его имя и нажмите кнопку **Отправить**, чтобы отправить в контейнер файлы пакета. 

   ![Защитник Azure Stack Hub — передача](./media/azure-stack-security-av/image4.png) 

8. В разделе **Отправить BLOB-объект** щелкните значок папки, перейдите к файлам обновления Защитника Windows *mpam-fe.exe* и щелкните **Открыть** в окне обозревателя файлов. 

9. В разделе **Отправка большого двоичного объекта** щелкните **Отправить**. 

   ![Защитник Azure Stack Hub — передача blob1](./media/azure-stack-security-av/image5.png) 

1. Повторите шаги 8–9 для файла *nis_full.exe*. 

   ![Защитник Azure Stack Hub — передача blob2](./media/azure-stack-security-av/image6.png)

Запланированная задача сканирует контейнер больших двоичных объектов каждые 30 минут и применяет новый пакет Защитника Windows.  

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о системе безопасности Azure Stack Hub](azure-stack-security-foundations.md)
