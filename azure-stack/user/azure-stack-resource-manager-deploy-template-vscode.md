---
title: Развертывание в Azure Stack с помощью Visual Studio Code | Документация Майкрософт
description: Я, как пользователь, хочу создать шаблон Azure Resource Manager в Visual Studio Code и применить схему развертывания для подготовки шаблона, совместимого с моей версией Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711105"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>Развертывание в Azure Stack с помощью Visual Studio Code

Вы можете использовать Visual Studio Code и расширение средств Azure Resource Manager для создания и изменения шаблонов Azure Resource Manager, которые будут работать в вашей версии Azure Stack. Шаблоны Resource Manager в Visual Studio Code можно создавать и без расширения. Но расширение предоставляет варианты автозаполнения, которые упрощают разработку шаблона. Кроме того, вы можете указать схему развертывания, которая поможет вам оценить доступные ресурсы Azure Stack.

С помощью инструкций в этой статье вы развернете виртуальную машину Windows.

## <a name="concepts-for-azure-stack-resource-manager"></a>Концепции Resource Manager в Azure Stack

### <a name="azure-stack-resource-manager"></a>Resource Manager для Azure Stack

Основные понятия, связанные с развертыванием решений Azure и управлением ими в Azure Stack, описаны в статье [об использовании шаблонов Azure Resource Manager в Azure Stack](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>Профили API
Основные понятия, связанные с координацией поставщиков ресурсов в Azure Stack, описаны в статье [об управлении профилями версий API в Azure Stack](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>Схема развертывания

Схема развертывания Azure Stack поддерживает гибридные профили на основе шаблонов Azure Resource Manager в Visual Studio Code. Вы можете изменить одну строку в шаблоне JSON, чтобы она ссылалась на схему, а затем использовать IntelliSense для оценки ресурса, совместимого с Azure. Изучите в этой схеме поставщиков ресурсов, типы и версии API, поддерживаемые вашей версией Azure Stack. Эта схема использует профиль API для получения конкретных версий конечных точек API из поставщиков ресурсов, поддерживаемых в вашей версии Azure Stack. Для заполнения значений type и apiVersion вы можете использовать функцию завершения слов. Кроме того, здесь будут доступны только типы ресурсов и версии API для соответствующего профиля API.

## <a name="prerequisites"></a>Предварительные требования

- [Visual Studio Code](https://code.visualstudio.com/)
- Доступ к Azure Stack.
- [Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json), установленный на компьютере, который обращается к конечным точкам управления.

## <a name="install-resource-manager-tools-extension"></a>Установка расширения для средств Resource Manager

Чтобы установить расширение средств Resource Manager, выполните следующие шаги:

1. Откройте Visual Studio Code.
2. Нажмите клавиши CTRL+SHIFT+X, чтобы открыть панель расширений.
3. Найдите `Azure Resource Manager Tools`, а затем нажмите кнопку **Установить**.
4. Чтобы завершить установку расширения, щелкните **Перезагрузить**.

## <a name="get-a-template"></a>Получение шаблона

Чтобы не создавать шаблон с нуля, откройте готовую версию из AzureStack-QuickStart-Templates (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-QuickStart-Templates — это репозиторий для шаблонов Resource Manager, которые развертывают ресурсы в Azure Stack. 

В этой статье используется шаблон с именем `101-vm-windows-create`. Этот шаблон определяет несложное развертывание виртуальной машины Windows для Azure Stack.  Этот шаблон также развертывает виртуальную сеть (с DNS), группу безопасности сети и сетевой интерфейс.

1. Откройте Visual Studio Code и перейдите в рабочую папку на локальном компьютере.
2. Откройте терминал Git bash в Visual Studio Code.
3. Выполните приведенную ниже команду, чтобы получить репозиторий быстрого начала работы в Azure Stack.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Откройте каталог, содержащий этот репозиторий.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Выберите **Открыть**, чтобы открыть файл `/101-vm-windows-create/azuredeploy.json` в репозитории.
6. Сохраните этот файл в своей рабочей области или, если вы создали ветвь репозитория, работайте прямо в ней.
7. Не закрывайте файл и измените значение поля `$Schema` на `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`.
8. Чтобы убедиться, что схема развертывания работает, очистите значение поля apiProfile.
    ```JSON  
    "apiProfile": ""
    ```
9. Поместите курсор в пустое пространство между кавычками и нажмите клавиши CTRL+ПРОБЕЛ. Вы можете выбрать любой из допустимых профилей API в схеме развертывания для Azure Stack. Эту операцию можно выполнить для каждого поставщика ресурсов в шаблоне.

    ![Схема развертывания Resource Manager в Azure Stack](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Когда все будет готово, вы сможете развернуть шаблон с помощью PowerShell. Следуйте указаниям из статьи [о развертывании с помощью PowerShell](azure-stack-deploy-template-powershell.md). Укажите в скрипте расположение шаблона.
11. Завершив развертывание виртуальной машины Windows, перейдите на портал Azure Stack и найдите группу ресурсов. Если вы хотите удалить из Azure Stack результаты выполнения этого упражнения, удалите группу ресурсов.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения [о шаблонах Azure Stack Resource Manager](azure-stack-arm-templates.md).  
- Дополнительные сведения [о профилях API в Azure Stack](azure-stack-version-profiles.md).