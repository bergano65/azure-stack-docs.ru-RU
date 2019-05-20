---
title: Настройка среды разработки в Azure Stack | Документация Майкрософт
description: Начните разработку приложений для Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490022"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Настройка среды разработки в Azure Stack 

Вы можете разрабатывать приложения для Azure Stack, используя рабочую станцию ​​Windows 10, Linux или macOS. В этой статье мы рассмотрим следующие вопросы: 

- Различные контексты, в которых ваше приложение запускается в Azure Stack. 
- Шаги, которые помогут настроить рабочую станцию ​​Windows 10, Linux или macOS. 
- Действия по созданию ресурсов в Azure Stack и развертыванию приложений. 

## <a name="azure-stack-context-and-your-code"></a>Контекст Azure Stack и ваш код 

Вы можете написать сценарии и приложения, которые могут выполнить все в Azure Stack. Тем не менее будет полезно разделить то, что вы будете выполнять, на три разные режима. 

1. В первом режиме можно создавать приложения, которые будут предоставлять ресурсы в Azure Stack, с использованием шаблонов Azure Resource Manager. Например, можно создать сценарий для создания шаблона Azure Resource Manager, создающего виртуальную сеть и виртуальные машины, на которых будет размещаться ваше приложение. 

2. Во втором режиме вы работаете напрямую с конечными точками, используя REST API и клиент REST, созданный в вашем коде. В этом режиме вы создадите сценарий, который создает виртуальную сеть и виртуальные машины, отправляя запросы в API-интерфейсе. 

3. В третьем режиме можно использовать код, чтобы создать приложение, размещенное в Azure Stack. После создания инфраструктуры в Azure Stack для размещения приложения разверните приложение в инфраструктуре. Как правило, сначала подготавливается среда, а затем в нее развертывается приложение. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Инфраструктура как услуга и платформа как услуга 

Облако Azure Stack поддерживает оба варианта: 

- инфраструктура как услуга (IaaS); 
- платформа как услуга (PaaS). 

IaaS и PaaS указывают на то, как настраивается компьютер для разработки. 

IaaS — это виртуализация частей центра обработки данных от сетевого оборудования, сети и серверов. При развертывании приложения на виртуальной машине, на которой размещен веб-сервер, вы работаете в парадигме IaaS. В этой парадигме Azure Stack управляет виртуальным механизмом, а ваше приложение находится на виртуальном сервере. Поставщики ресурсов Azure Stack поддерживают сетевые компоненты и виртуальные серверы. 

PaaS абстрагирует уровень инфраструктуры, чтобы вы развернули свое приложение на конечной точке, которая затем запускает ваше приложение. В парадигме PaaS можно использовать контейнеры для размещения приложения, а затем развернуть контейнерное приложение в службе, которая запускает контейнер, или вы можете отправить приложение непосредственно в службу, в которой оно выполняется. Azure Stack можно использовать для запуска службы приложений Azure и Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Resource Manager для Azure Stack 

Эти три режима, а также PaaS или IaaS включены в версии Azure Stack для Azure Resource Manager. Платформа управления позволяет развертывать и отслеживать ресурсы Azure Stack, а также управлять ими. Диспетчер позволяет работать со следующими элементами в группе за одну операцию. Дополнительные сведения о работе c Azure Stack Resource Manger в см. в статье [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Пакеты средств разработки Azure Stack 

Azure Stack использует версию Azure Resource Manager для Azure Stack.  Чтобы помочь вам работать с Azure Resource Manager для Azure Stack, используя выбранный вами код, мы предоставили несколько пакетов средств разработки. в частности такие: 

- [.NET и C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [GO](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Перед началом работы 

Вам потребуется: 

- Войти на портал пользователя Azure Stack. 
- Имя клиента. 
- Знать используемый тип хранилища удостоверений — Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS). 

Если у вас есть вопросы об Azure Stack, обратитесь к оператору облака. 

## <a name="windows-10"></a>Windows 10 

Компьютер с Windows 10 позволит работать с PowerShell 5.0, Visual Studio, а если вы работаете с ASDK, то подключиться к среде по VPN. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Выполните настройки с помощью PowerShell. Дополнительные сведения см. в статье [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Установка Azure Stack Powershell). 

2. Скачайте средства Azure Stack. Дополнительные сведения см. в статье [Скачивание средств Azure Stack из GitHub](../operator/azure-stack-powershell-download.md). 

3. Если вы используете ASDK, установите и настройте [VPN-подключение к Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Установите и настройте Azure CLI. Дополнительные сведения см. в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Скачайте и установите обозреватель хранилищ Azure. Обозреватель хранилища Azure —это автономное приложение, позволяющее легко работать с данными из хранилища Azure Stack.  Дополнительные сведения см. в статье [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Подключение обозревателя хранилища к подписке Azure Stack или к учетной записи хранения). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет средств разработки (SDK) для кода. 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Компьютер с Linux позволит работать с интерфейсом командной строки Azure и Visual Studio Code или с предпочитаемой вами интегрированной средой разработки. 

> [!Note]   
> Если вы используете компьютер с Linux с ASDK, удаленный компьютер должен быть в той же сети, что и ASDK. Вы не сможете подключиться через виртуальную частную сеть. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Установите и настройте Azure CLI. Дополнительные сведения см. в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите обозреватель хранилищ Azure. Обозреватель хранилища Azure —это автономное приложение, позволяющее легко работать с данными из хранилища Azure Stack.  Дополнительные сведения см. в статье [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Подключение обозревателя хранилища к подписке Azure Stack или к учетной записи хранения). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет средств разработки (SDK) для кода. 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Компьютер с macOS позволит работать с интерфейсом командной строки Azure и Visual Studio Code или с предпочитаемой вами интегрированной средой разработки. 

> [!Note]   
> Если вы используете компьютер с macOS с ASDK, удаленный компьютер должен быть в той же сети, что и ASDK. Вы не сможете подключиться через виртуальную частную сеть. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Установите и настройте Azure CLI. Дополнительные сведения см. в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите обозреватель хранилищ Azure. Обозреватель хранилища Azure —это автономное приложение, позволяющее легко работать с данными из хранилища Azure Stack. Дополнительные сведения см. в статье [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Подключение обозревателя хранилища к подписке Azure Stack или к учетной записи хранения). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет средств разработки (SDK) для кода. 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Дополнительная информация 

Разверните приложение на ресурсах в Azure Stack. Дополнительные сведения см. в статье [Common deployments for Azure Stack](azure-stack-dev-start-deploy-app.md) (Распространенные сценарии развертывания для Azure Stack).