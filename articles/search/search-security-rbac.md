---
title: RBAC-rollen instellen voor toegang tot Azure-beheerders
titleSuffix: Azure Cognitive Search
description: Op rollen gebaseerd beheer (RBAC) in de Azure Portal voor het beheren en delegeren van beheer taken voor Azure Cognitive Search management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 402fae5622219b14cfdab921ebe1a78ad5dd111e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462835"
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

## <a name="permissions-table"></a>Machtigings tabel

De volgende tabel bevat een overzicht van de bewerkingen die zijn toegestaan in azure Cognitive Search en met welke sleutel de toegang tot een bepaalde bewerking wordt ontgrendeld.

| Bewerking | Machtigingen |
|-----------|-------------------------|
| Een service maken | Eigenaar van het Azure-abonnement |
| Een service schalen | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource  |
| Een service verwijderen | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource |
| Objecten op de service maken, wijzigen, verwijderen: <br>Indexen en onderdeel onderdelen (met inbegrip van analyse definities, Score profielen, CORS-opties), Indexeer functies, gegevens bronnen, synoniemen, suggesties | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource |
| Een query uitvoeren op een index | Beheerder of query sleutel (RBAC niet van toepassing) |
| Systeem gegevens opvragen, zoals het retour neren van statistieken, aantallen en lijsten met objecten | Beheerder sleutel, RBAC op de bron (eigenaar, bijdrager, lezer) |
| Beheer sleutels beheren | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource |
| Query sleutels beheren |  Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource  |

## <a name="see-also"></a>Zie ook

+ [Beheren met PowerShell](search-manage-powershell.md) 
+ [Prestaties en optimalisatie in azure Cognitive Search](search-performance-optimization.md)
+ [Ga aan de slag met Access Control op basis van rollen in de Azure Portal](../role-based-access-control/overview.md).