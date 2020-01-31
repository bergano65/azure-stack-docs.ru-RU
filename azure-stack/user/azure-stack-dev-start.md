---
title: Настройка среды разработки в Azure Stack Hub
description: Начните разработку приложений для Azure Stack Hub.
author: mattbriggs
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 31df74f7c5f3db5c7651747601010109620e82cd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883736"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>Настройка среды разработки в Azure Stack Hub 

Вы можете разрабатывать приложения для Azure Stack Hub, используя рабочую станцию ​​Windows 10, Linux или macOS. В этой статье мы рассмотрим следующие вопросы: 

- Различные контексты, в которых ваше приложение запускается в Azure Stack Hub. 
- Действия, которые помогут настроить рабочую станцию ​​Windows 10, Linux или macOS. 
- Действия по созданию ресурсов в Azure Stack Hub и их развертыванию в приложении. 

## <a name="azure-stack-hub-context-and-your-code"></a>Контекст Azure Stack Hub и ваш код 

Вы можете создавать скрипты и приложения для выполнения разных задач в Azure Stack Hub. Тем не менее рекомендуется ограничить область применения следующими тремя режимами. 

1. Первый из них — создание приложений, которые подготавливают ресурсы в Azure Stack Hub с помощью шаблонов Azure Resource Manager. Например, можно создать сценарий для создания шаблона Azure Resource Manager, создающего виртуальную сеть и виртуальные машины, на которых будет размещено ваше приложение. 

2. Во втором режиме вы работаете напрямую с конечными точками, используя REST API и клиент REST, созданные в вашем коде. В этом режиме вы создадите сценарий, который создает виртуальную сеть и виртуальные машины, отправляя запросы к интерфейсам API. 

3. В третьем режиме можно использовать код, чтобы создать размещенное в Azure Stack Hub приложение. Создав в Azure Stack Hub инфраструктуру для размещения приложения, вы развертываете в ней свое приложение. Как правило, сначала подготавливается среда, а затем в ней развертывается приложение. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Инфраструктура как услуга и платформа как услуга 

Облачная платформа Azure Stack Hub поддерживает обе технологии: 

- Инфраструктура как услуга (IaaS) 
- платформа как услуга (PaaS). 

IaaS и PaaS указывают, как настроить компьютер для разработки. 

IaaS — это виртуализация компонентов центра обработки данных, соответствующих сетевому оборудованию, сети и серверам. При развертывании приложения на виртуальной машине, на которой размещен веб-сервер, вы используете модель IaaS. В рамках этой модели Azure Stack Hub управляет виртуальным оборудованием, а ваше приложение располагается на виртуальном сервере. Поставщики ресурсов Azure Stack Hub поддерживают сетевые компоненты и виртуальные серверы. 

PaaS абстрагирует уровень инфраструктуры, чтобы вы развернули свое приложение на конечной точке, которая затем запускает приложение. В рамках модели PaaS можно использовать контейнеры для размещения приложения, а затем развернуть контейнерное приложение в службе, которая запускает контейнер. Или можно отправить приложение непосредственно в службу, которая запускает это приложение. Azure Stack Hub можно использовать для запуска Службы приложений Azure и Kubernetes. 

### <a name="azure-stack-hub-resource-manager"></a>Resource Manager для Azure Stack Hub 

Три описанных выше режима, а также PaaS и IaaS поддерживаются версией Azure Resource Manager для Azure Stack Hub. Платформа управления позволяет развертывать и отслеживать ресурсы Azure Stack Hub, а также управлять ими. Она позволяет работать с ресурсами как с группой, используя одну операцию. Дополнительные сведения о работе c Resource Manager для Azure Stack Hub см. в статье [Управление профилями версий API в Azure Stack Hub](azure-stack-version-profiles.md). 

### <a name="azure-stack-hub-sdks"></a>Пакеты средств разработки для Azure Stack Hub 

Azure Stack Hub использует специальную версию Azure Resource Manager. Чтобы упростить работу с любым кодом в Azure Resource Manager для Azure Stack Hub, мы предоставляем пакеты средств разработки для нескольких языков, в том числе: 

- [.NET и C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [GO](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Перед началом работы 

Прежде чем начать настройку среды, потребуется следующее. 

- Доступ к порталу пользователя Azure Stack Hub. 
- Имя клиента. 
- Определить используемый тип диспетчера удостоверений — Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS). 

Если у вас есть вопросы об Azure Stack Hub, обратитесь к оператору облака. 

## <a name="windows-10"></a>Windows 10 

Если вы используете компьютер с Windows 10, можно использовать PowerShell 5.0 и Visual Studio. И если вы работаете с Пакетом средств разработки Azure Stack (ASDK), то можете подключиться к среде с помощью VPN-подключения. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Выполните настройки с помощью PowerShell. Инструкции приведены в руководстве по [установке PowerShell для Azure Stack Hub](../operator/azure-stack-powershell-install.md). 

2. Скачайте средства Azure Stack Hub. Инструкции приведены в разделе [Скачивание средств Azure Stack Hub из GitHub](../operator/azure-stack-powershell-download.md). 

3. Если вы используете ASDK, установите и настройте [VPN-подключение к Azure Stack Hub](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn). 

4. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

5. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, которое позволяет работать с данными из хранилища Azure Stack Hub. Инструкции приведены в статье [Подключение обозревателя службы хранилища к подписке Azure Stack Hub или к учетной записи хранения](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет SDK для своего кода: 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Если вы используете компьютер Linux, вы можете использовать интерфейс командной строки Azure и Visual Studio Code или предпочитаемую вами интегрированную среду разработки. 

> [!Note]   
> Если вы используете компьютер с Linux с ASDK, удаленный компьютер должен размещаться в той же сети, что и ASDK. Вы не сможете подключиться через виртуальную частную сеть. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, которое позволяет работать с данными из хранилища Azure Stack Hub. Инструкции приведены в статье [Подключение обозревателя службы хранилища к подписке Azure Stack Hub или к учетной записи хранения](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет SDK для своего кода: 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Компьютер macOS позволит использовать интерфейс командной строки Azure и Visual Studio Code или предпочитаемую вами интегрированную среду разработки. 

> [!Note]   
> Если вы используете компьютер macOS с ASDK, удаленный компьютер должен размещаться в той же сети, что и ASDK. Вы не сможете подключиться через виртуальную частную сеть. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack Hub](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, которое позволяет работать с данными из хранилища Azure Stack Hub. Инструкции приведены в статье [Подключение обозревателя службы хранилища к подписке Azure Stack Hub или к учетной записи хранения](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Установка интегрированной среды разработки 

1. Установите интегрированную среду разработки (IDE) в зависимости от базы кода и предпочтений. 

     - Visual Studio Code (Python, Go и NodeJS). Скачайте Visual Studio Code для компьютера с сайта [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET и C#). Скачайте выпуск Visual Studio Community с сайта [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse и Java Скачайте Eclipse с сайта [eclipse.org](https://www.eclipse.org/downloads/). 

2. Установите пакет SDK для своего кода: 

     - [.NET и C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [GO](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Дальнейшие действия 

Развертывание приложения в ресурсах в Azure Stack Hub описано в статье [Распространенные сценарии развертывания для Azure Stack Hub](azure-stack-dev-start-deploy-app.md).
