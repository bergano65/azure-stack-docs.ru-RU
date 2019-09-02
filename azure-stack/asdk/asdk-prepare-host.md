---
title: Подготовка главного компьютера ASDK | Документация Майкрософт
description: Узнайте, как подготовить главный компьютер для установки Пакета средств разработки Azure Stack (ASDK).
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
ms.openlocfilehash: 23cacc7a9005e1695f7394d1e441298f3f90bca8
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025942"
---
# <a name="prepare-the-asdk-host-computer"></a>Подготовка главного компьютера для ASDK
Перед установкой Пакета средств разработки Azure Stack на главном компьютере необходимо подготовить узел ASDK. После подготовки этот узел загрузится с жесткого диска виртуальной машины CloudBuilder.vhdx, чтобы начать развертывание ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Подготовка компьютера, на котором будет размещен пакет средств разработки
Перед установкой ASDK на главном компьютере необходимо подготовить среду ASDK. Чтобы подготовить эту среду, выполните следующие действия.

1. Войдите на главный компьютер ASDK с учетной записью локального администратора.
2. Убедитесь, что файл CloudBuilder.vhdx перемещен в корень диска C:\ (`C:\CloudBuilder.vhdx`).
3. Запустите следующий сценарий, чтобы скачать файл установщика ASDK (asdk-installer.ps1) из [репозитория инструментов Azure Stack на GitHub](https://github.com/Azure/AzureStack-Tools) в папку **C:\AzureStack_Installer** на главном компьютере ASDK.

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

    ![Подготовка среды в ASDK](media/asdk-prepare-host/1.PNG) 

5. На странице **Select Cloudbuilder vhdx** (Выбор файла Cloudbuilder.vhdx) установщика найдите и выберите файл **Cloudbuilder.vhdx**, скачанный и извлеченный на [предыдущих шагах](asdk-download.md). На этой странице вы также можете установить флажок **Add drivers** (Добавление драйверов), если вам нужно добавить драйверы на главный компьютер ASDK. Щелкните **Далее**.  

    ![Выбор файла cloudbuilder.vhdx и добавление драйверов в ASDK](media/asdk-prepare-host/2.PNG)

6. На странице **Optional settings** (Дополнительные параметры) укажите данные учетной записи локального администратора для главного компьютера ASDK и нажмите кнопку **Next** (Далее).

    Если на этом шаге не предоставить учетные данные локального администратора, вам понадобится прямой доступ или KVM-доступ к главному компьютеру после его перезагрузки в рамках настройки ASDK.

   ![Необязательные параметры в ASDK: ввод данных учетной записи локального администратора](media/asdk-prepare-host/3.PNG)

    Можно также задать значения для следующих дополнительных параметров:
    - **Computername** (Имя компьютера). Этот параметр задает имя узла ASDK. Имя должно соответствовать требованиям к полному доменному имени, а его размер не должен превышать 15 символов. Значение по умолчанию — случайное имя компьютера, созданное Windows.
    - **Static IP configuration** (Конфигурация статических IP-адресов). Задает использование статического IP-адреса в развертывании. В противном случае при перезагрузке установщика в файл cloudbuilder.vhdx сетевые интерфейсы настраиваются с помощью протокола DHCP. Если вы решили использовать статическую конфигурацию IP, откроется окно дополнительных параметров, где необходимо также выполнить следующие действия.
      - Выберите сетевой адаптер. Проверьте, можете ли вы подключиться к адаптеру, а затем нажмите кнопку **Далее**.
      - Проверьте отображаемые значения параметров **IP-адрес**, **Шлюз** и **DNS**, а затем нажмите кнопку **Далее**.

   > [!TIP]
   > Чтобы найти IP-адрес сервера времени, перейдите на сайт [ntppool.org](https://www.ntppool.org/) или выполните проверку связи с сервером time.windows.com.

7. Нажмите кнопку **Далее**, чтобы запустить процесс подготовки.
8. Когда состояние подготовки изменится на **Завершено**, нажмите кнопку **Далее**.

    ![Подготовка среды в ASDK](media/asdk-prepare-host/4.PNG)

9. Нажмите кнопку **Reboot now** (Перезагрузить сейчас), чтобы загрузить главный компьютер ASDK с виртуального жесткого диска cloudbuilder.vhdx и [продолжить процесс развертывания](asdk-install.md).

    ![Перезагрузка главного компьютера ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Дополнительная информация
[Установка ASDK](asdk-install.md)
