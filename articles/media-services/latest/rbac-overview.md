---
title: Op rollen gebaseerd toegangscontrole voor Media Services-accounts - Azure | Microsoft Documenten
description: In dit artikel wordt gesproken over rbac (role-based access control) voor Azure Media Services-accounts.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236919"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>RBAC (Role-based access control) voor Media Services-accounts

Op dit moment definieert Azure Media Services geen aangepaste rollen die specifiek zijn voor de service. Om volledige toegang te krijgen tot het Media Services-account, kunnen klanten de ingebouwde rollen van **eigenaar** of **bijdrager**gebruiken. Het belangrijkste verschil tussen deze rollen is: de **eigenaar** kan bepalen wie toegang heeft tot een resource en de **inzender** niet. De ingebouwde **Reader-rol** kan ook worden gebruikt, maar de gebruiker of toepassing heeft alleen leestoegang tot de API's van Media Services. 

## <a name="design-principles"></a>Ontwerpprincipes

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. v3 API's geven geen geheimen of referenties terug op **bewerkingen op get** of **list.** De sleutels zijn altijd null, leeg of opgeschoond uit het antwoord. De gebruiker moet een aparte actiemethode aanroepen om geheimen of referenties te krijgen. De **readerrol** kan bewerkingen zoals Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets niet aanroepen. Met afzonderlijke acties u desgewenst meer gedetailleerde RBAC-beveiligingsmachtigingen instellen in een aangepaste rol.

Ga als eerste naar de ondersteuning van de bewerkingen die Media Services ondersteunt:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Het [ingebouwde roldefinitiesartikel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) vertelt u precies wat de rol toekent. 

Zie de volgende artikelen voor meer informatie:

- [Klassieke abonnementsbeheerdersrollen, Azure RBAC-rollen en Azure AD-beheerdersrollen](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Wat is RBAC voor Azure-bronnen?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [RBAC gebruiken om toegang te beheren](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services resource provider operations](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen met Media Services v3 API's](media-services-apis-overview.md)
- [Beleid voor inhoudssleutel gebruiken met Media Services .NET](get-content-key-policy-dotnet-howto.md)
