---
title: Снять защиту с полезных данных, ключи которых были отозваны в ASP.NET Core
author: rick-anderson
description: Узнайте, как снять защиту данных, защищенных с помощью ключей, которые были отозваны в ASP.NET Core приложении.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654598"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Снять защиту с полезных данных, ключи которых были отозваны в ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core API-интерфейсы защиты данных в основном не предназначены для неопределенного сохранения конфиденциальных полезных данных. Другие технологии, такие как [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) и [Azure Rights Management](/rights-management/) , более подходят для сценария неопределенного хранилища и имеют соответствующие возможности управления ключами. С другой стороны, разработчик не запрещает использовать ASP.NET Core API-интерфейсы защиты данных для долгосрочной защиты конфиденциальных данных. Ключи никогда не удаляются из кольца ключа, поэтому `IDataProtector.Unprotect` всегда может восстанавливать существующие полезные данные при условии, что ключи доступны и действительны.

Однако проблема возникает, когда разработчик пытается снять защиту с данных, защищенных с помощью отозванного ключа, так как в этом случае `IDataProtector.Unprotect` выдаст исключение. Это может быть удобно для кратковременных или временных полезных данных (например, маркеров проверки подлинности), так как эти типы полезных данных могут легко воссоздаться системой, и в худшем случае посетителям сайта может потребоваться снова войти в систему. Но для материализованных полезных данных наличие `Unprotect` может привести к неприемлемой потери данных.

## <a name="ipersisteddataprotector"></a>иперсистеддатапротектор

Для поддержки сценария, позволяющего снять защиту полезных данных даже на лицевой стороне отмененных ключей, система защиты данных содержит тип `IPersistedDataProtector`. Чтобы получить экземпляр `IPersistedDataProtector`, просто получите экземпляр `IDataProtector` обычным образом и попробуйте приведение `IDataProtector` к `IPersistedDataProtector`.

> [!NOTE]
> Не все `IDataProtector` экземпляры можно привести к `IPersistedDataProtector`. Разработчики должны использовать оператор C# AS или аналогичным образом, чтобы избежать исключений среды выполнения, вызванных недопустимыми приведениями, и должны быть готовы к обработке случайного сбоя.

`IPersistedDataProtector` предоставляет следующую поверхность API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Этот API принимает защищенные полезные данные (в виде массива байтов) и возвращает незащищенные полезные данные. Перегрузка на основе строк отсутствует. Ниже приведены два выходных параметра.

* `requiresMigration`: будет иметь значение true, если ключ, используемый для защиты этих полезных данных, больше не является активным ключом по умолчанию, например, ключ, используемый для защиты этих полезных данных, устарел, а операция с предыдущим действием уже выполнена. Вызывающий объект может попытаться повторно защитить полезные данные в зависимости от бизнес-потребностей.

* `wasRevoked`: будет иметь значение true, если ключ, используемый для защиты полезной нагрузки, был отозван.

>[!WARNING]
> При передаче `ignoreRevocationErrors: true` методу `DangerousUnprotect` следует соблюдать особую осторожность. Если после вызова этого метода значение `wasRevoked` равно true, то ключ, используемый для защиты этих полезных данных, был отозван, а подлинность полезной нагрузки должна рассматриваться как подозрительная. В этом случае продолжайте работать только с незащищенными полезными данными, только если у вас есть отдельная гарантия, которую она подлинна, например, она поступает из защищенной базы данных, а не отправляется недоверенным веб-клиентом.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
