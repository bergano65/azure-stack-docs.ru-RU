---
title: Настройка среды разработки в Azure Stack | Документация Майкрософт
description: Начните разработку приложений для Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 437963355c8077c31ca5f0f55acdd344bb0060e2
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955720"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Настройка среды разработки в Azure Stack 

Вы можете разрабатывать приложения для Azure Stack, используя рабочую станцию ​​Windows 10, Linux или macOS. В этой статье мы рассмотрим следующие вопросы: 

- Различные контексты, в которых ваше приложение запускается в Azure Stack. 
- Действия, которые помогут настроить рабочую станцию ​​Windows 10, Linux или macOS. 
- Действия по созданию ресурсов в Azure Stack и их развертыванию в приложении. 

## <a name="azure-stack-context-and-your-code"></a>Контекст Azure Stack и ваш код 

Вы можете создавать сценарии и приложения для выполнения множества задач в Azure Stack. Тем не менее рекомендуется ограничить область применения следующими тремя режимами. 

1. В первом режиме можно создавать приложения, которые подготавливают ресурсы в Azure Stack с помощью шаблонов Azure Resource Manager. Например, можно создать сценарий для создания шаблона Azure Resource Manager, создающего виртуальную сеть и виртуальные машины, на которых будет размещено ваше приложение. 

2. Во втором режиме вы работаете напрямую с конечными точками, используя REST API и клиент REST, созданные в вашем коде. В этом режиме вы создадите сценарий, который создает виртуальную сеть и виртуальные машины, отправляя запросы к интерфейсам API. 

3. В третьем режиме можно использовать код, чтобы создать приложение, размещенное в Azure Stack. После создания инфраструктуры в Azure Stack для размещения приложения вы развертываете в ней свое приложение. Как правило, сначала подготавливается среда, а затем в ней развертывается приложение. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Инфраструктура как услуга и платформа как услуга 

Облачная платформа Azure Stack поддерживает обе технологии: 

- Инфраструктура как услуга (IaaS) 
- платформа как услуга (PaaS). 

IaaS и PaaS указывают, как настроить компьютер для разработки. 

IaaS — это виртуализация компонентов центра обработки данных, соответствующих сетевому оборудованию, сети и серверам. При развертывании приложения на виртуальной машине, на которой размещен веб-сервер, вы используете модель IaaS. В рамках этой модели Azure Stack управляет виртуальным оборудованием, а ваше приложение располагается на виртуальном сервере. Поставщики ресурсов Azure Stack поддерживают сетевые компоненты и виртуальные серверы. 

PaaS абстрагирует уровень инфраструктуры, чтобы вы развернули свое приложение на конечной точке, которая затем запускает приложение. В рамках модели PaaS можно использовать контейнеры для размещения приложения, а затем развернуть контейнерное приложение в службе, которая запускает контейнер. Или можно отправить приложение непосредственно в службу, которая запускает это приложение. Azure Stack можно использовать для запуска Службы приложений Azure и Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Resource Manager для Azure Stack 

Три режима, приведенные выше, а также PaaS или IaaS включены в версии Azure Resource Manager для Azure Stack. Платформа управления позволяет развертывать и отслеживать ресурсы Azure Stack, а также управлять ими. Она позволяет работать с ресурсами как с группой, используя одну операцию. Дополнительные сведения о работе c Azure Stack Resource Manger в см. в статье [Управление профилями версий API в Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>Пакеты SDK для Azure Stack 

Azure Stack использует версию Azure Resource Manager для Azure Stack. Чтобы упростить вам работу с Azure Resource Manager для Azure Stack, предоставив возможность использовать код по вашему выбору, мы предоставили ряд пакетов SDK: 

- [.NET и C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [GO](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Перед началом работы 

Прежде чем начать настройку среды, потребуется следующее. 

- Войти на портал пользователя Azure Stack. 
- Имя клиента. 
- Определить используемый тип диспетчера удостоверений — Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS). 

Если у вас есть вопросы об Azure Stack, обратитесь к оператору облака. 

## <a name="windows-10"></a>Windows 10 

Если вы используете компьютер с Windows 10, можно использовать PowerShell 5.0 и Visual Studio. И если вы работаете с Пакетом средств разработки Azure Stack (ASDK), то можете подключиться к среде с помощью VPN-подключения. 

### <a name="set-up-your-tools"></a>Настройка инструментов 

1. Выполните настройки с помощью PowerShell. Инструкции приведены в разделе [Установка Azure Stack для PowerShell](../operator/azure-stack-powershell-install.md). 

2. Скачайте средства Azure Stack. Инструкции приведены в разделе [Скачивание средств Azure Stack из GitHub](../operator/azure-stack-powershell-download.md). 

3. Если вы используете ASDK, установите и настройте [VPN-подключение к Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, позволяющее легко работать с данными из хранилища Azure Stack. Инструкции приведены в разделе [Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения](azure-stack-storage-connect-se.md). 

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

1. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, позволяющее легко работать с данными из хранилища Azure Stack. Инструкции приведены в разделе [Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения](azure-stack-storage-connect-se.md). 

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

1. Установите и настройте Azure CLI. Инструкции приведены в статье [Использование профилей версий API и Azure CLI в Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Скачайте и установите Обозреватель службы хранилища Azure. Обозреватель службы хранилища — это изолированное приложение, позволяющее легко работать с данными из хранилища Azure Stack. Инструкции приведены в разделе [Подключение обозревателя службы хранилища к подписке Azure Stack или к учетной записи хранения](azure-stack-storage-connect-se.md). 

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

## <a name="next-steps"></a>Дополнительная информация 

Развертывание приложения на ресурсах в Azure Stack описано в разделе [Распространенные сценарии развертывания для Azure Stack](azure-stack-dev-start-deploy-app.md).
