---
title: Подготовка главного компьютера для размещения Пакета средств разработки Azure Stack (ASDK) | Документация Майкрософт
description: В этой статье объясняется, как подготовить главный компьютер для установки Пакета средств разработки Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 291042e0a7af78ed2431c901901e7e44b1f05de1
ms.sourcegitcommit: fc7da38321736e952b2cc6d5d07f276d095dc8d1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887159"
---
# <a name="prepare-the-asdk-host-computer"></a>Подготовка главного компьютера для ASDK
Перед установкой ASDK на главном компьютере необходимо подготовить узел ASDK. После подготовки данные главного компьютера, на котором будет размещен пакет средств разработки, загружаются с виртуального жесткого диска CloudBuilder.vhdx, чтобы начать развертывание ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Подготовка главного компьютера для размещения пакета средств разработки
Перед установкой ASDK на главном компьютере необходимо подготовить среду ASDK.
1. Войдите на главный компьютер, где будет размещен пакет средств разработки, от имени локального администратора.
2. Переместите файл CloudBuilder.vhdx в корень диска C:\ (C:\CloudBuilder.vhdx).
3. Запустите следующий скрипт, чтобы скачать файл установщика пакета разработчика (asdk-installer.ps1) из репозитория инструментов [Azure Stack на GitHub](https://github.com/Azure/AzureStack-Tools) в папку **C:\AzureStack_Installer** на главном компьютере разработки:

   > [!IMPORTANT]
   > Каждый раз при установке ASDK скачивайте файл asdk-installer.ps1. В этот сценарий часто вносятся изменения, и для каждого развертывания ASDK следует использовать текущую версию. Старые версии сценария могут не работать с последним выпуском.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Откройте консоль PowerShell с повышенными привилегиями, выполните скрипт **C:\AzureStack_Installer\asdk-installer.ps1** и щелкните **Prepare Environment** (Подготовка среды).

    ![Снимок экрана: подготовка среды](media/asdk-prepare-host/1.PNG) 

5. На странице **Select Cloudbuilder vhdx** (Выбор файла Cloudbuilder.vhdx) установщика найдите и выберите файл **Cloudbuilder.vhdx**, скачанный и извлеченный на [предыдущих шагах](asdk-download.md). На этой странице вы также можете установить флажок **Add drivers** (Добавление драйверов), если вам нужно добавить драйверы на главный компьютер разработки. Щелкните **Далее**.  

    ![Снимок экрана: выбор Cloudbuilder.vhdx](media/asdk-prepare-host/2.PNG)

6. На странице **дополнительных параметров** укажите сведения об учетной записи локального администратора для главного компьютера, где размещается пакет средств разработки, и нажмите кнопку **Next** (Далее).<br><br>Если на этом шаге не предоставить учетные данные локального администратора, вам понадобится прямой доступ или доступ KVM к главному компьютеру после перезагрузки компьютера в рамках настройки пакета средств разработки.

   ![Снимок экрана: дополнительные параметры](media/asdk-prepare-host/3.PNG)

    Можно также задать значения для следующих дополнительных параметров:
    - **Computername** (Имя компьютера). Задает имя узла комплекта разработки. Имя должно соответствовать требованиям к полному доменному имени, а его размер не должен превышать 15 символов. Значение по умолчанию — случайное имя компьютера, созданное Windows.
    - **Static IP configuration** (Конфигурация статических IP-адресов). Задает использование статического IP-адреса в развертывании. В противном случае при перезагрузке установщика в файл cloudbuilder.vhdx сетевые интерфейсы настраиваются с помощью протокола DHCP. Если вы решили использовать статическую конфигурацию IP, откроется окно дополнительных параметров, где необходимо также выполнить следующие действия.
      - Выберите сетевой адаптер. Проверьте, можете ли вы подключиться к адаптеру, а затем нажмите кнопку **Далее**.

        ![Снимок экрана: параметры сетевого адаптера](media/asdk-prepare-host/step-four-network-adapter.png)

      - Проверьте правильность значений параметров **IP-адрес**, **Шлюз** и **DNS**, укажите действительное значение параметра **Time server IP address** (IP-адрес сервера времени), а затем нажмите кнопку **Далее**.

        >[!TIP]
        >Чтобы найти IP-адрес сервера времени, перейдите на сайт [ntppool.org](https://www.ntppool.org/) или выполните проверку связи с сервером time.windows.com. 

        ![Снимок экрана: параметры IP-конфигурации](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Нажмите кнопку **Далее**, чтобы запустить процесс подготовки.
8. Когда состояние подготовки изменится на **Завершено**, нажмите кнопку **Далее**.

    ![Снимок экрана: завершение подготовки](media/asdk-prepare-host/4.PNG)

9. Нажмите кнопку **Reboot now** (Перезагрузить сейчас), чтобы загрузить данные главного компьютера для размещения пакета средств разработки на виртуальный жесткий диск Cloudbuilder.vhdx и [продолжить процесс развертывания](asdk-install.md).

    ![Снимок экрана: перезагрузка](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Дополнительная информация
[Установка ASDK](asdk-install.md)
