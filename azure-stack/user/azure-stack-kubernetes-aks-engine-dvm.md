---
title: Перемещение кластера элементов Marketplace в AKS Engine в Azure Stack | Документация Майкрософт
description: Узнайте, как переместить кластер элементов Marketplace в AKS Engine в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 41ffa9d9d9f96506d30a6a3c69a557cdba034843
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145844"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack"></a>Переместите кластер элементов Marketplace в AKS Engine в Azure Stack

Элемент Kubernetes Azure Stack Marketplace использует шаблон Azure Resource Manager для развертывания виртуальной машины развертывания (ВМ) для загрузки и установки AKS Engine и создания модели API входных данных, используемой для описания кластера, после запуска AKS Engine на виртуальной машине и развертывания кластера. В этой статье показано, как получить доступ к AKS Engine и соответствующим файлам, чтобы их можно было использовать для выполнения операций обновления и масштабирования в кластере Kubernetes.

## <a name="access-aks-engine-in-the-dvm"></a>Доступ к AKS Engine в виртуальной машине развертывания

После успешного завершения развертывания, инициированного элементом Kubernetes Azure Stack Marketplace, вы можете найти AKS Engine, которая использовалась для развертывания кластера, установленного в виртуальной машине развертывания, созданной в группе ресурсов, которую было указано для кластера. Эта виртуальная машина не является частью кластера Kubernetes и создается в собственной виртуальной сети. Ниже приведены шаги для поиска виртуальной машины и обнаружения в ней AKS Engine:

1.  Откройте пользовательский портал Azure Stack и выберите группу ресурсов, указанную для кластера Kubernetes.
2.  В группе ресурсов найдите виртуальную машину развертывания. Имя начинается с префикса: **vmd-** .
3.  Выберите виртуальную машину развертывания. В разделе "Обзор" ** найдите общедоступный IP-адрес. Используйте этот адрес и консольное приложение, например Putty, чтобы установить сеанс SSH для виртуальной машины.
4.  В сеансе на виртуальной машине развертывания вы найдете AKS Engine по следующему пути: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Укажите файл `.json`, описывающий кластеры, используемые в качестве входных данных в AKS Engine. Файл по состоянию на `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Обратите внимание, что файл содержит учетные данные субъекта-службы, используемые для развертывания кластера. Если вы решили сохранить файл, постарайтесь переместить его в защищенное хранилище.
6.  Путь к выходному каталогу, созданному AKS Engine, можно узнать по адресу `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`. В этом каталоге найдите выходной `apimodel.json` по пути `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. В каталоге и файле `apimodel.json` содержатся все созданные сертификаты, ключи и учетные данные, необходимые для развертывания кластера Kubernetes. Храните эти ресурсы в безопасном месте.
7.  Выберите файл конфигурации Kubernetes, который часто называют файлом **kubeconfig**, по пути:  , где  соответствует идентификатору расположения Azure Stack. Этот файл полезен, если вы планируете настроить **kubectl** для доступа к кластеру Kubernetes.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Использование AKS Engine с созданным кластером

После того как вы найдете файл AKS Engine, входной файл "apimodel.json", выходной каталог и выходной файл "apimodel.json", храните их в безопасном месте. Вы можете использовать двоичный и выходной `apimodel.json` AKS Engine на любой виртуальной машине Linux.

1.  Чтобы продолжить использовать AKS Engine, выполните такие операции, как **обновление** и **масштабирование**, скопируйте двоичный файл **aks-engine** на целевой компьютер. Если вы используете ту же **vmd-** машину в каталоге.

2.  Создайте каталог с именем кластера или с другим мнемотехническим именем, которое ссылается на новый кластер, и сохраните в нем выходной файл apimodel.json. Убедитесь, что это защищенное место, так как файл содержит учетные данные. После этого AKS Engine можно запустить для выполнения таких операций, как [масштабирование](azure-stack-kubernetes-aks-engine-scale.md) или [обновление](azure-stack-kubernetes-aks-engine-upgrade.md)

## <a name="next-steps"></a>Дополнительная информация

- Сведения [об обработчике AKS в Azure Stack](azure-stack-kubernetes-aks-engine-overview.md).  
- [Устранение проблем с обработчиком AKS в Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

