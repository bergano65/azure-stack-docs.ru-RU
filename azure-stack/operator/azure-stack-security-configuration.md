---
title: Настройка элементов управления безопасностью в Azure Stack Hub
description: Узнайте, как настроить элементы управления безопасностью в Azure Stack Hub
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/17/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: cd63908eceffb5072a41ae200ca2a544728b5971
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509523"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Настройка элементов управления безопасностью в Azure Stack Hub

В этой статье приведены сведения об элементах управления безопасностью, которые можно изменять в Azure Stack Hub, а также о возможных недостатках таких изменений.

Архитектура Azure Stack Hub основана на двух принципах безопасности: неизбежность наличия уязвимых мест и защищенный режим по умолчанию. Дополнительные сведения см. в статье [Элементы управления безопасностью для инфраструктуры Azure Stack Hub](azure-stack-security-foundations.md). Несмотря на то что в Azure Stack Hub система безопасности реализована по умолчанию, в некоторых сценариях развертывания необходимо реализовать дополнительные меры защиты.

## <a name="tls-version-policy"></a>Политика выбора версий протокола TLS

TLS — это широко распространенный протокол шифрования, который обеспечивает зашифрованный обмен данными по сети. Со временем этот протокол развивался, и сейчас доступно несколько его версий. Инфраструктура Azure Stack Hub исключительно использует протокол TLS 1.2. Все внешние интерфейсы, взаимодействующие с Azure Stack Hub, сейчас по умолчанию должны использовать протокол TLS 1.2. Тем не менее, для обеспечения обратной совместимости эта инфраструктура также поддерживает протоколы TLS 1.1. и 1.0. Когда клиент TLS запрашивает обмен данными по протоколу TLS 1.1 или TLS 1.0, Azure Stack Hub переходит на более раннюю версию TLS. Если клиент запрашивает обмен данными по протоколу TLS 1.2, Azure Stack Hub устанавливает подключение по протоколу TLS 1.2.

Так как протоколы TLS 1.0 и 1.1 постепенно признаны устаревшими или запрещены организациями и стандартами соответствия, начиная с обновления 1906 теперь вы можете настраивать в Azure Stack Hub политику TLS. Вы можете принудительно применить политику обмена данными только по протоколу TLS 1.2, если любая попытка установить сеанс TLS с версией ниже 1.2 не разрешена и отклонена.

> [!IMPORTANT]
> Корпорация Майкрософт рекомендует использовать политику обмена данными только по протоколу TLS 1.2 в рабочих средах Azure Stack Hub.

## <a name="get-tls-policy"></a>Получение политики TLS

Чтобы просмотреть политику TLS на всех конечных точках Azure Stack Hub, используйте [привилегированную конечную точку (PEP)](azure-stack-privileged-endpoint.md):

```powershell
Get-TLSPolicy
```

Выходные данные примера:

    TLS_1.2

## <a name="set-tls-policy"></a>Настройка политики TLS

Чтобы задать политику TLS на всех конечных точках Azure Stack Hub, используйте [привилегированную конечную точку (PEP)](azure-stack-privileged-endpoint.md):

```powershell
Set-TLSPolicy -Version <String>
```

Параметры командлета *Set-TLSPolicy*:

| Параметр | Описание | Тип | Обязательно |
|---------|---------|---------|---------|
| *Версия* | Допустимые версии протокола TLS в Azure Stack Hub | Строка | да|

Чтобы настроить разрешенные версии протокола TLS на всех конечных точках Azure Stack Hub, используйте следующие значения:

| Значение версии | Описание |
|---------|---------|
| *TLS_All* | Конечные точки TLS Azure Stack Hub поддерживают протокол TLS 1.2, но использование версий TLS 1.1 и TLS 1.0 разрешается. |
| *TLS_1.2* | Конечные точки TLS Azure Stack Hub поддерживают только протокол TLS 1.2. | 

Обновление политики TLS занимает несколько минут.

### <a name="enforce-tls-12-configuration-example"></a>Пример конфигурации принудительного применения протокола TLS 1.2

Этот пример позволяет настроить принудительное применение политикой TLS только протокола TLS 1.2.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Выходные данные примера:

    VERBOSE: Successfully setting enforce TLS 1.2 to True
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is enforced

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Пример конфигурации разрешения всех версий протокола TLS (1.2, 1.1 и 1.0)

Этот пример позволяет настроить разрешение политикой TLS всех версий протокола TLS (1.2, 1.1 и 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Выходные данные примера:

    VERBOSE: Successfully setting enforce TLS 1.2 to False
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is not enforced

## <a name="next-steps"></a>Дальнейшие действия

- [Элементы управления безопасностью для инфраструктуры Azure Stack Hub](azure-stack-security-foundations.md)
- [Подробнее о смене секретов в Azure Stack Hub](azure-stack-rotate-secrets.md)
- [Обновление антивирусной программы "Защитник Windows" в Azure Stack Hub](azure-stack-security-av.md)
