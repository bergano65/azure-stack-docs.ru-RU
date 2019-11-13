---
title: Настройка идентификатора гибридного облака для приложений Azure и Azure Stack Hub
description: Узнайте, как настроить идентификатор гибридного облака для приложений Azure и Azure Stack Hub.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a38a7541d4184e89cb0a821db04a2fcb8c267a31
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73639971"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-hub-applications"></a>Настройка идентификатора гибридного облака для приложений Azure и Azure Stack Hub

*Область применения: интегрированные системы Azure Stack Hub и Пакет средств разработки Azure Stack Hub*

Из этой статьи вы узнаете, как настроить идентификатор гибридного облака для приложений Azure и Azure Stack Hub.

В Azure и Azure Stack Hub предусмотрено два способа доступа к приложениям.

 * Если в Azure Stack Hub есть постоянное подключение к Интернету, можно использовать Azure Active Directory (Azure AD).
 * Если в Azure Stack Hub нет подключения к Интернету, используйте службы федерации Active Directory (AD FS).

Субъекты-службы обеспечивают доступ к приложениям Azure Stack Hub для их развертывания или настройки с помощью Azure Resource Manager в Azure Stack Hub.

В этом решении показано, как создать среду, после чего вы выполните в ней следующие действия:

> [!div class="checklist"]
> - создадите гибридный идентификатор для глобальной платформы Azure и Azure Stack Hub;
> - получите маркер для доступа к API Azure Stack Hub.

Для выполнения указанных в этом решении действий необходимо иметь разрешения оператора Azure Stack Hub.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub — это расширение Azure. Azure Stack Hub обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Это решение позволяет использовать единственное гибридное облако, с помощью которого можно создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В статье [Hybrid cloud design patterns for Azure Stack](overview-app-design-considerations.md) (Рекомендации по проектированию гибридных приложений) описаны основные аспекты качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которые следует учитывать при разработке, развертывании и использовании гибридных приложений. Эти рекомендации помогут оптимизировать разработку гибридных приложений и предотвратить появление проблем с рабочими средами.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Создание субъекта-службы с доступом для Azure AD с помощью портала

Если вы развернули Azure Stack Hub с использованием Azure AD в качестве хранилища идентификаторов, субъекты-службы создаются точно так же, как для Azure. Подробные сведения об использовании портала см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal). Прежде чем начать, проверьте [необходимые разрешения Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Создание субъекта-службы для AD FS с помощью PowerShell

Если вы развернули Azure Stack Hub с использованием AD FS, для создания субъекта-службы, назначения роли для доступа и входа с этим идентификатором можно использовать PowerShell. Подробные сведения об использовании PowerShell см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="using-the-azure-stack-hub-api"></a>Использование API Azure Stack Hub

В описании решения [API Azure Stack Hub](../user/azure-stack-rest-api-use.md) приведены шаги для получения маркера для доступа к API Azure Stack Hub.

## <a name="connect-to-azure-stack-hub-using-powershell"></a>Подключение к Azure Stack Hub с помощью PowerShell

Краткое руководство [Установка PowerShell для Azure Stack](../operator/azure-stack-powershell-install.md) поможет выполнить действия, необходимые для установки Azure PowerShell, и подключиться к установке Azure Stack Hub.

### <a name="prerequisites"></a>Предварительные требования

Установленная среда Azure Stack Hub с подключением к Azure Active Directory и доступной подпиской. Если вы не установили Azure Stack Hub, воспользуйтесь инструкциями по установке [Пакета средств разработки Azure Stack Hub](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-hub-using-code"></a>Подключение к Azure Stack Hub с помощью кода

Чтобы подключиться к Azure Stack Hub из кода, через API конечных точек Azure Resource Manager получите конечную точку аутентификации и конечную точку Microsoft Graph для Azure Stack Hub, а затем выполните аутентификацию с помощью запросов REST. Образец клиентского приложения можно найти на [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Если пакет Azure SDK для используемого языка не поддерживает профили API Azure, вы не можете использовать его для работы с Azure Stack Hub. Дополнительные сведения о профилях API Azure см. в статье [Управление профилями версий API](../user/azure-stack-version-profiles.md).

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о работе с идентификаторами в Azure Stack Hub см. в [этой статье](../operator/azure-stack-identity-architecture.md).
 - Дополнительные сведения о шаблонах для облака Azure см. в статье [Конструктивные шаблоны облачных решений](https://docs.microsoft.com/azure/architecture/patterns).
