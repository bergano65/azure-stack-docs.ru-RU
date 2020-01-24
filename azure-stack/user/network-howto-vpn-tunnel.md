---
title: Сведения о настройке нескольких VPN-туннелей типа "сеть — сеть" в Azure Stack Hub | Документация Майкрософт
description: Узнайте как настроить несколько VPN-туннелей типа "сеть — сеть" в Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 4593898a1ea70b2001c252f885b12db2f16e922e
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883054"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack-hub"></a>Сведения о настройке нескольких VPN-туннелей типа "сеть — сеть" в Azure Stack Hub

В этой статье показано, как использовать шаблон Resource Manager в Azure Stack Hub для развертывания решения. Решение создает несколько групп ресурсов с соответствующими виртуальными сетями и способами подключения этих систем.

Вы можете найти шаблоны в разделе [Шаблоны интеллектуальных границ Azure](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) репозитория GitHub. Шаблон находится в папке**rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Сценарии

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Создание нескольких VPN-туннелей

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Развертывание приложения трех уровней, веб-приложений, поиска в Интернете и баз данных.

-  Разверните два первых шаблона на отдельных экземплярах Azure Stack Hub.

-  Уровень **WebTier** будет размещен на PPE1, а уровень **AppTier** — на PPE2.

-  Подключите уровни **WebTier** и **AppTier** с помощью туннеля IKE.

-  Подключите**AppTier** к локальной системе, которая будет вызывать **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Действия по развертыванию нескольких VPN

Это процесс с несколькими шагами. Для этого решения вы будете использовать портал Azure Stack Hub. Однако вы можете использовать PowerShell, Azure CLI или другие цепочки средств как кода для сбора выходных данных, а затем использовать их в качестве входных.

![замещающий текст](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Пошаговое руководство

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Развертывание веб-уровня в экземплярах Azure Stack Hub PPE1

1.  Откройте портал пользователя Azure Stack Hub и выберите **Создать ресурс**.

2.  Выберите **Развертывание шаблона**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  Скопируйте и вставьте содержимое файла azuredeploy.json из репозитория **azure-smart-edge-Patterns/rras-vnet-vpntunnel** в окно шаблона. Вы увидите ресурсы, содержащиеся в шаблоне, выберите **Сохранить**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  Введите имя **группы ресурсов** и проверьте параметры.

    > ![Примечание]  
    > Адресное пространство WebTier будет **10.10.0.0/16**, и вы сможете видеть расположение группы ресурсов **PPE1**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Развертывание уровня приложений во втором экземпляре Azure Stack Hub

Вы можете использовать тот же процесс, что и **WebTier**, но другие параметры, как показано ниже:

> [!Note]  
> Адресное пространство AppTier будет **10.20.0.0/16**, и вы сможете видеть расположение группы ресурсов **WestUS2**.

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Обзор развертываний веб-уровня и уровня приложений, а также выходных данных записи

1.  Убедитесь, что развертывание успешно завершено. Выберите **Выходные данные**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  Скопируйте первые четыре значения в приложение "Блокнот".

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  Повторите действия для развертывания **AppTier**.

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Создание туннеля от веб-уровня до уровня приложения

1.  Откройте портал пользователя Azure Stack Hub и выберите **Создать ресурс**.

2.  Выберите **Развертывание шаблона**.

3.  Вставьте содержимое из файла **azuredeploy.tunnel.ike.json**.

4.  Выберите **Изменить параметры**.

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Создание туннеля из уровня приложения в веб-уровень

1.  Откройте портал пользователя Azure Stack Hub и выберите **Создать ресурс**.

2.  Выберите **Развертывание шаблона**.

3.  Вставьте содержимое из файла **azuredeploy.tunnel.ike.json**.

4.  Выберите **Изменить параметры**.

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Просмотр развертывания туннеля

При просмотре выходных данных из расширения пользовательского сценария, вы увидите создаваемый туннель и его состояние. Вы увидите, что одна сторона показывает **соединение** в ожидании готовности другой стороны, а другая — **соединение** после развертывания.

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Устранение неполадок на виртуальной машине RRAS

1.  Измените правило RDP с **Отклонить** на **Разрешить**.

2.  Подключитесь к системе с помощью клиента удаленного рабочего стола (DRP) с учетными данными, заданными во время развертывания.

3.  Откройте PowerShell с помощью командной строки с повышенными привилегиями и выполните команду `get-VPNS2SInterface`.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  Для управления системой используйте командлеты **RemoteAccess**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>Установка RRAS на локальной виртуальной машине уровня служб базы данных

1.  Целевым объектом является образ Windows 2016.

2.  Если скопировать сценарий `Add-Site2SiteIKE.ps1` из репозитория и запустить его локально, сценарий установит **WindowsFeature** и **RemoteAccess**.

    > [!Note]
    > В зависимости от среды может потребоваться перезагрузить систему.

    Для справки см. сведения о конфигурации сети локального компьютера.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  Запустите сценарий, добавив параметры**вывода**, записанные в развертывании шаблона AppTier.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  Теперь туннель настроен и ожидает подключения AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Настройка уровня приложения для уровня базы данных

1.  Откройте портал пользователя Azure Stack Hub и выберите **Создать ресурс**.

2.  Выберите **Развертывание шаблона**.

3.  Вставьте содержимое из файла **azuredeploy.tunnel.ike.json**.

4.  Выберите **Изменить параметры**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  Убедитесь, что выбран параметр AppTier и удаленный внутренний сетевой набор установлен по адресу 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Подтверждение туннеля между уровнем приложения и уровнем базы данных

1.  Чтобы проверить туннель без входа в виртуальную машину, запустите расширение пользовательского скрипта.

2.  Перейдите на виртуальную машину RRAS (AppTier).

3.  Выберите **Расширения** и **Запустить расширение пользовательских скриптов**.

4.  Перейдите в каталог сценариев в репозитории **azure-smart-edge-Patterns/rras-vnet-vpntunnel**. Выберите **Get-VPNS2SInterfaceStatus.ps1**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  Если включить RDP и войти, открыть PowerShell и запустить `get-vpns2sinterface`, вы увидите, что туннель подключен.

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > Вы можете тестировать RDP как с одной машины на другую, так и со второй на первую.

    > [!Note]  
    > Для локальной реализации этого решения вам потребуется развернуть маршруты к удаленной сети Azure Stack Hub в коммутационной инфраструктуре или, как минимум, на определенных виртуальных машинах

### <a name="deploying-a-gre-tunnel"></a>Развертывание туннеля GRE

Для этого шаблона в этом пошаговом руководстве использовался [шаблон протокола IKE](network-howto-vpn-tunnel-ipsec.md). Однако вы также можете развернуть [туннель GRE](network-howto-vpn-tunnel-gre.md). Этот туннель обеспечивает большую пропускную способность.

Процесс почти идентичен. Однако, при развертывании шаблона туннеля в существую инфраструктуру, необходимо использовать выходные данные из другой системы для первых трех входных данных. Для группы ресурсов, к которой выполняется развертывание, необходимо знать **LOCALTUNNELGATEWAY**, а не группу ресурсов, к которой вы пытаетесь подключиться.

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Дальнейшие действия

[Сети Azure Stack Hub: различия и рекомендации](azure-stack-network-differences.md)  
[How to create a VPN tunnel using GRE](network-howto-vpn-tunnel-gre.md) (Создание VPN-туннеля с помощью GRE)  
[How to create a VPN Tunnel using IPSEC](network-howto-vpn-tunnel-ipsec.md) (Создание VPN-туннеля с помощью IPSEC)