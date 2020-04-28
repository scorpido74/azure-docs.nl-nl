---
title: RBAC-rollen instellen voor toegang tot Azure-beheerders
titleSuffix: Azure Cognitive Search
description: Op rollen gebaseerd beheer (RBAC) in de Azure Portal voor het beheren en delegeren van beheer taken voor Azure Cognitive Search management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112559"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>RBAC-rollen instellen voor beheerders toegang tot Azure Cognitive Search

Azure biedt een [globaal autorisatie model op basis van rollen](../role-based-access-control/role-assignments-portal.md) voor alle services die worden beheerd via de portal-of Resource Manager-api's. De rollen eigenaar, bijdrager en lezer bepalen het *Service beheer* niveau voor Active Directory gebruikers, groepen en beveiligings-principals die aan elke rol zijn toegewezen. 

> [!Note]
> Er zijn geen toegangs beheer op basis van rollen voor het beveiligen van delen van een index of een subset van documenten. Voor toegang op basis van identiteiten via zoek resultaten kunt u beveiligings filters maken om de resultaten te beperken op basis van identiteit, en documenten te verwijderen waarvoor de aanvrager geen toegang moet hebben. Zie [beveiligings filters](search-security-trimming-for-azure-search.md) en [veilig met Active Directory](search-security-trimming-for-azure-search-with-aad.md)voor meer informatie.

## <a name="management-tasks-by-role"></a>Beheer taken per rol

Voor Azure Cognitive Search zijn rollen gekoppeld aan machtigings niveaus die ondersteuning bieden voor de volgende beheer taken:

| Rol | Taak |
| --- | --- |
| Eigenaar |Maak of verwijder de service of een object op de service, inclusief de API-sleutels, indexen, Indexeer functies, Indexeer gegevens bronnen en de planningen voor de Indexeer functie.<p>Bekijk de status van de service, inclusief aantallen en opslag grootte.<p>Een rollidmaatschap toevoegen of verwijderen (alleen een eigenaar kan het lidmaatschap van een rol beheren).<p>Abonnements beheerders en service-eigen aren hebben automatisch lidmaatschap van de rol eigen aren. |
| Inzender |Hetzelfde toegangs niveau als eigenaar, min RBAC-functie beheer. Een inzender kan bijvoorbeeld objecten maken of verwijderen, of de [API-sleutels](search-security-api-keys.md)weer geven en opnieuw genereren, maar kan geen rollidmaatschap wijzigen. |
| [Ingebouwde rol Search Service Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Gelijk aan de rol Inzender. |
| Lezer |Bekijk de belangrijkste en metrische gegevens van de service. Leden van deze rol kunnen de index, indexer, gegevens bron of sleutel informatie niet weer geven.  |

Rollen verlenen geen toegangs rechten voor het service-eind punt. Zoek service bewerkingen, zoals index beheer, index populatie en query's op zoek gegevens, worden beheerd via API-Keys, niet op rollen. Zie [Manage API-Keys (](search-security-api-keys.md)Engelstalig) voor meer informatie.

## <a name="see-also"></a>Zie ook

+ [Beheren met PowerShell](search-manage-powershell.md) 
+ [Prestaties en optimalisatie in azure Cognitive Search](search-performance-optimization.md)
+ [Ga aan de slag met Access Control op basis van rollen in de Azure Portal](../role-based-access-control/overview.md).