---
title: RBAC-rollen instellen voor Azure-beheertoegang
titleSuffix: Azure Cognitive Search
description: RBAC (Role-based administrative control) in de Azure-portal voor het beheren en delegeren van administratieve taken voor Azure Cognitive Search-beheer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112559"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>RBAC-rollen instellen voor beheerderstoegang tot Azure Cognitive Search

Azure biedt een [wereldwijd op rollen gebaseerd autorisatiemodel](../role-based-access-control/role-assignments-portal.md) voor alle services die worden beheerd via de portal of API's voor Resource Manager. De rollen Eigenaar, Inzender en Lezer bepalen het *servicebeheerniveau* voor Active Directory-gebruikers, -groepen en beveiligingsprincipals die aan elke rol zijn toegewezen. 

> [!Note]
> Er zijn geen op rollen gebaseerde toegangscontroles voor het beveiligen van delen van een index of een subset van documenten. Voor toegang op basis van identiteit via zoekresultaten u beveiligingsfilters maken om resultaten bij te snijden op identiteit, waarbij documenten worden verwijderd waarvoor de aanvrager geen toegang zou moeten hebben. Zie [Beveiligingsfilters](search-security-trimming-for-azure-search.md) en [Beveiligen met Active Directory](search-security-trimming-for-azure-search-with-aad.md)voor meer informatie.

## <a name="management-tasks-by-role"></a>Beheertaken per rol

Voor Azure Cognitive Search worden rollen gekoppeld aan machtigingsniveaus die de volgende beheertaken ondersteunen:

| Rol | Taak |
| --- | --- |
| Eigenaar |Maak of verwijder de service of een object op de service, inclusief api-sleutels, indexen, indexers, indexergegevensbronnen en indexerschema's.<p>De servicestatus weergeven, inclusief tellingen en opslaggrootte.<p>Rollidmaatschap toevoegen of verwijderen (alleen een eigenaar kan het lidmaatschap van een rol beheren).<p>Abonnementsbeheerders en service-eigenaren hebben automatisch lid in de rol Eigenaren. |
| Inzender |Hetzelfde toegangsniveau als Eigenaar, minus RBAC-rolbeheer. Een inzender kan bijvoorbeeld objecten maken of verwijderen, of [api-sleutels](search-security-api-keys.md)weergeven en regenereren, maar kan geen rollidmaatschappen wijzigen. |
| [Ingebouwde rol zoekservicemedewerker](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Gelijk aan de rol van bijdrager. |
| Lezer |Bekijk serviceessentials en statistieken. Leden van deze rol kunnen geen index-, indexerer-, gegevensbron- of belangrijke informatie weergeven.  |

Rollen verlenen geen toegangsrechten tot het eindpunt van de service. Zoekservicebewerkingen, zoals indexbeheer, indexpopulatie en query's op zoekgegevens, worden beheerd via api-sleutels, niet via rollen. Zie [Api-sleutels beheren voor](search-security-api-keys.md)meer informatie.

## <a name="see-also"></a>Zie ook

+ [Beheren met PowerShell](search-manage-powershell.md) 
+ [Prestaties en optimalisatie in Azure Cognitive Search](search-performance-optimization.md)
+ [Ga aan de slag met toegangsbeheer op basis van rollen in de Azure-portal.](../role-based-access-control/overview.md)