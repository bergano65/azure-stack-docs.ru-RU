---
title: Настройка идентификатора гибридного облака в приложениях Azure и Azure Stack | Документация Майкрософт
description: Из этой статьи вы узнаете, как настроить идентификатор гибридного облака в приложениях Azure и Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 074d971c1f951797b5dc2d53a62eef56d0b7249f
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492322"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Руководство по Настройка идентификатора гибридного облака для приложений Azure и Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Из этой статьи вы узнаете, как настроить идентификатор гибридного облака для приложений Azure и Azure Stack.

В Azure и Azure Stack предусмотрено два способа доступа к приложениям.

 * Если в Azure Stack есть постоянное подключение к Интернету, можно использовать Azure Active Directory (Azure AD).
 * Если в Azure Stack нет подключения к Интернету, используйте службы федерации Active Directory (AD FS).

Субъекты-службы обеспечивают доступ к приложениям Azure Stack для их развертывания или настройки с помощью Azure Resource Manager в Azure Stack.

В этом руководстве показано, как создать среду, после чего вы выполните в ней следующие действия:

> [!div class="checklist"]
> - создадите гибридное удостоверение для глобальной платформы Azure и Azure Stack;
> - Получите маркер для доступа к API Azure Stack.

Для выполнения указанных в этом руководстве действий необходимо иметь разрешения оператора Azure Stack.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack — это расширение Azure.re. Azure Stack обеспечивает гибкость и высокую скорость внедрения инноваций облачных вычислений в локальной среде. Только это гибридное облако позволяет создавать и развертывать гибридные приложения в любой точке мира.  
> 
> В [рекомендациях по проектированию гибридных приложений](https://aka.ms/hybrid-cloud-applications-pillars) описаны характеристики качественного программного обеспечения (размещение, масштабируемость, доступность, устойчивость, управляемость и безопасность), которых следует придерживаться при разработке, развертывании и использовании гибридных приложений. Рекомендации помогут оптимизировать разработку гибридных приложений и свести к минимуму проблемы в рабочих средах.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Создание субъекта-службы с доступом для Azure AD с помощью портала

Если вы развернули Azure Stack с использованием Azure AD в качестве хранилища идентификаторов, субъекты-службы создаются точно так же, как для Azure. Подробные сведения об использовании портала см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal). Прежде чем начать, проверьте [необходимые разрешения Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Создание субъекта-службы для AD FS с помощью PowerShell

Если вы развернули Azure Stack с использованием AD FS, для создания субъекта-службы, назначения роли для доступа и входа с этим идентификатором можно использовать PowerShell. Подробные сведения об использовании PowerShell см. в статье [Предоставление приложениям доступа к Azure Stack](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="using-the-azure-stack-api"></a>Использование API Azure Stack

В руководстве [Azure Stack API](azure-stack-rest-api-use.md) приведены шаги по получению маркера для доступа к Azure Stack API.

## <a name="connect-to-azure-stack-using-powershell"></a>Подключение к Azure Stack с помощью PowerShell

Краткое руководство [Начало работы с PowerShell в Azure Stack](../operator/azure-stack-powershell-install.md) поможет выполнить действия, необходимые для установки Azure PowerShell, и подключиться к среде Azure Stack.

### <a name="prerequisites"></a>Предварительные требования

Установленная среда Azure Stack с подключением к Azure Active Directory и доступной подпиской. Если вы не установили Azure Stack, воспользуйтесь инструкциями по установке [Пакета средств разработки Azure Stack](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Подключение к Azure Stack из кода программы

Чтобы подключиться к Azure Stack из кода, через API конечных точек Azure Resource Manager получите конечную точку аутентификации и конечную точку Microsoft Graph для Azure Stack, а затем выполните аутентификацию с помощью запросов REST. Образец клиентского приложения можно найти на [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Если пакет Azure SDK для используемого языка не поддерживает профили API Azure, вы не можете использовать его для работы с Azure Stack. Дополнительные сведения о профилях API Azure см. в статье [Управление профилями версий API](azure-stack-version-profiles.md).

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о работе с удостоверениями в Azure Stack см. в статье [Архитектура удостоверений для Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Дополнительные сведения о шаблонах облака Azure см. в статье [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns) (Конструктивные шаблоны облачных решений).
