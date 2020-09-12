---
title: Op rollen gebaseerd toegangs beheer voor Media Services accounts-Azure | Microsoft Docs
description: In dit artikel vindt u informatie over op rollen gebaseerd toegangs beheer (RBAC) voor Azure Media Services accounts.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d6bc37a8aaddfb48e6d06eb46d9c1648e815b5ad
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289236"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Op rollen gebaseerd toegangs beheer (RBAC) voor Media Services accounts

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Op dit moment worden door Azure Media Services geen aangepaste rollen gedefinieerd die specifiek zijn voor de service. Klanten kunnen de ingebouwde rollen van **eigenaar** of **Inzender**gebruiken om volledige toegang te krijgen tot het Media Services-account. Het belangrijkste verschil tussen deze rollen is: de **eigenaar** kan bepalen wie toegang heeft tot een resource en de **mede werker** niet kan. De ingebouwde rol **lezer** kan ook worden gebruikt, maar de gebruiker of toepassing heeft alleen lees toegang tot de Media Services-api's. 

## <a name="design-principles"></a>Ontwerpprincipes

Een van de belangrijkste principes van de API v3 is het beter beveiligen van de API. V3-Api's retour neren geen geheimen of referenties voor **Get** -of **List** -bewerkingen. De sleutels zijn altijd null, leeg of opgeschoond uit het antwoord. De gebruiker moet een afzonderlijke actie methode aanroepen om geheimen of referenties op te halen. De rol van **lezer** kan geen bewerkingen aanroepen zoals Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Door afzonderlijke acties te ondernemen kunt u, indien gewenst, meer gedetailleerde RBAC-beveiligings machtigingen instellen in een aangepaste rol.

Ga als volgt te werk om de bewerkingen weer te geven Media Services ondersteunt:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

In het artikel [ingebouwde roldefinities](../../role-based-access-control/built-in-roles.md) wordt duidelijk aangegeven wat de rol verleent. 

Raadpleeg de volgende artikelen voor meer informatie:

- [Klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Wat is Azure RBAC (toegangsbeheer op basis van rollen)?](../../role-based-access-control/overview.md)
- [RBAC gebruiken om toegang te beheren](../../role-based-access-control/role-assignments-rest.md)
- [Bewerkingen voor de resource provider Media Services](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen met Media Services v3-Api's](media-services-apis-overview.md)
- [Beleid voor inhouds sleutels ophalen met behulp van Media Services .NET](get-content-key-policy-dotnet-howto.md)
