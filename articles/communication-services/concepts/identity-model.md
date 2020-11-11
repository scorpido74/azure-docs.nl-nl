---
title: Identiteits model
titleSuffix: An Azure Communication Services concept
description: Meer informatie over de identiteits-en toegangs tokens
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 996f138a14923319381738e7a55cd7ba4e8c4320
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517764"
---
# <a name="identity-model"></a>Identiteits model

Azure Communication Services is identiteit neutraal service. Dit ontwerp heeft meerdere voor delen:
- Bestaande identiteiten opnieuw gebruiken in uw identiteits beheersysteem
- Flexibiliteit voor integratie scenario's
- Houdt uw identiteiten geheim aan de Azure-communicatie Services

In plaats van de bestaande informatie in uw systeem te dupliceren, houdt u de toewijzings relatie bij die specifiek is voor uw zakelijke case. Bijvoorbeeld toewijzing van identiteiten 1:1, 1: N, N:1, N:M. Externe id's (zoals telefoon nummers, gebruikers, apparaten, toepassingen, GUID) kunnen niet worden gebruikt als Azure-communicatie-identiteit. Toegangs tokens die zijn gegenereerd voor de Azure communication service-identiteit worden gebruikt voor toegang tot primitieven, zoals chatten of aanroepen. 

## <a name="identity"></a>Identiteit

Identiteiten worden gemaakt met de Azure Communication Service-beheer bibliotheek. Identiteit fungeert als id in de conversaties en wordt gebruikt voor het maken van toegangs tokens. Dezelfde identiteit kan deel uitmaken van meerdere gelijktijdige sessies op meerdere apparaten. De identiteit kan tegelijkertijd meerdere actieve toegangs tokens hebben. Het verwijderen van de identiteit, resource of het abonnement heeft tot gevolg dat alle toegangs tokens ongeldig zijn en alle gegevens die voor deze identiteit zijn opgeslagen, worden verwijderd. Verwijderde identiteit kan geen nieuwe toegangs tokens uitgeven, geen toegang tot eerder opgeslagen gegevens (bijvoorbeeld chat berichten). 

Er worden geen kosten in rekening gebracht op basis van het aantal identiteiten dat u hebt, maar door primitieven te gebruiken. Het aantal identiteiten hoeft niet te worden beperkt, het toewijzen van de identiteit van uw toepassing aan de Azure Communication Services-identiteiten. Met de vrijheid van de toewijzing is verantwoordelijk voor de persoonlijke voor waarden. Wanneer de gebruiker van uw toepassing wil verwijderen van uw systeem, moet u alle identiteiten verwijderen die aan die gebruiker zijn gekoppeld.

Azure Communication Services biedt geen speciale identiteiten voor anonieme gebruikers. Het houdt geen rekening met de toewijzing tussen de gebruikers en identiteiten, maar het is niet duidelijk of de identiteit anoniem is. U kunt het concept ontwerpen om aan uw vereisten te voldoen. Onze aanbeveling is een nieuwe identiteit te maken voor de anonieme gebruiker van elke toepassing. Met het bezit van het geldige toegangs token kan iedereen toegang krijgen tot de niet-verwijderde inhoud van de identiteit. Bijvoorbeeld chat berichten die door de gebruiker zijn verzonden. De toegang is beperkt tot scopes die deel uitmaken van het toegangs token. Meer informatie over bereiken vindt u in het *toegangs token* van de sectie.

### <a name="mapping-of-identities"></a>Toewijzing van identiteiten

De functionaliteit van IMS wordt niet door Azure Communication Services gerepliceerd. Het biedt klanten geen manier om klantspecifieke identiteiten te gebruiken. Bijvoorbeeld telefoon nummer of e-mail adres. In plaats daarvan worden unieke id's geboden, die u kunt toewijzen aan de identiteiten van uw toepassing. In azure Communication Services wordt geen informatie opgeslagen, waardoor de echte identiteit van uw gebruikers kan worden onthuld.

U wordt aangeraden om te ontwerpen, hoe gebruikers van uw identiteits domein worden toegewezen aan de Azure communication service-identiteiten. U kunt elk soort patroon 1:1, 1: N, N:1 of M:N. volgen U kunt bepalen of één gebruiker is toegewezen aan één identiteit of aan meerdere identiteiten. Wanneer er een nieuwe identiteit wordt gemaakt, wordt u aangeraden de toewijzing van deze identiteit op te slaan in de gebruikers of gebruikers van uw toepassing. Aangezien de-identiteiten de toegangs tokens vereisen voor het gebruik van de primitieven, moet de identiteit bekend zijn voor de gebruiker of de gebruikers van uw toepassing.

Als u relationele data base gebruikt voor de opslag van gebruikers, kan de implementatie verschillen op basis van uw toewijzings scenario. Voor scenario's met toewijzing 1:1 of N:1 kunt u een kolom *CommunicationServicesId* toevoegen aan de tabel om uw Azure Communication Services-identiteit op te slaan. In scenario's met relatie 1: N of N:M kunt u overwegen om een afzonderlijke tabel in de relationele data base te maken.

## <a name="access-token"></a>Toegangs token

Toegangs token is een JWT-token dat kan worden gebruikt om toegang te krijgen tot de Azure Communication Service-primitieven. Het verleende toegangs token heeft integriteits beveiliging, de claims kunnen niet worden gewijzigd na het uitgeven. Dat wil zeggen dat de hand matige wijziging van eigenschappen, zoals identiteit, verval datum of bereiken, het toegangs token ongeldig maakt. Het gebruik van primitieven met ongeldig tokens leidt tot weigering van toegang tot de primitieve. 

De eigenschappen van het toegangs token zijn: *identiteit, verval datum* en *bereik*. Het toegangs token is altijd 24 uur geldig. Nadat dit toegangs token voor de tijd ongeldig is gemaakt en niet kan worden gebruikt voor toegang tot een primitieve. Identiteit moet een manier hebben, hoe er een nieuw toegangs token van de server-side service wordt aangevraagd. Met het parameter *bereik* wordt een niet-lege set primitieven gedefinieerd die kan worden gebruikt. Azure Communication Services ondersteunt de volgende bereiken voor toegangs tokens:

|Naam|Beschrijving|
|---|---|
|Chat|  Verleent de mogelijkheid om deel te nemen aan een chatsessie|
|VoIP|  Hiermee verleent u de mogelijkheid om identiteiten en telefoon nummers aan te roepen|


Als u het toegangs token wilt intrekken voordat het verloopt, kunt u de Azure Communication Service-beheer bibliotheek hiervoor gebruiken. Het intrekken van het token is niet onmiddellijk en duurt Maxi maal 15 minuten. Verwijdering van de identiteit, resource of het abonnement leidt ertoe dat alle toegangs tokens worden ingetrokken. Als u de mogelijkheid van een gebruiker om toegang te krijgen tot specifieke functionaliteit wilt verwijderen, trekt u alle toegangs tokens in. Geef vervolgens een nieuw toegangs token met een beperkt aantal bereiken.
Het draaien van toegangs sleutels van de Azure communication-service leidt ertoe dat alle actieve toegangs tokens die zijn gemaakt met de voormalige toegangs sleutel, worden ingetrokken. Alle identiteiten hebben geen toegang meer tot de Azure communication-service en zijn vereist voor het uitgeven van nieuwe toegangs tokens. 

We raden u aan toegangs tokens te verlenen aan de server zijde en niet in de toepassing van de client. De reden hiervoor is dat de verlenende toegangs sleutel of een beheerde identiteit vereist is. Het wordt niet aanbevolen om veiligheids redenen om de toegangs sleutels te delen met de toepassing van de client. De client toepassing moet gebruikmaken van een vertrouwd service-eind punt dat uw clients kan verifiëren en namens hen toegangs tokens kan verlenen. Meer informatie over de architectuur vindt u [hier](./client-and-server-architecture.md).

Als u toegangs tokens in de cache opslaat voor een back-upopslag, kunt u het beste versleuteling gebruiken. Het toegangs token is gevoelige gegevens en kan worden gebruikt voor schadelijke activiteiten als deze niet zijn beveiligd. Met het bezit van het toegangs token kunt u de SDK initialiseren en toegang krijgen tot de API. De toegankelijke API wordt alleen beperkt op basis van scopes die het toegangs token heeft. We raden u aan toegangs tokens alleen te verlenen met scopes die vereist zijn.

## <a name="next-steps"></a>Volgende stappen

* Zie [toegangs tokens maken en beheren](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens) voor een inleiding tot het beheren van toegangs tokens
* Zie [verifiëren bij Azure Communication Services](https://docs.microsoft.com/azure/communication-services/concepts/authentication) voor een inleiding tot verificatie
* Zie [Beschik baarheid van regio's en gegevens locatie](https://docs.microsoft.com/azure/communication-services/concepts/privacy) voor een inleiding tot het locatie en de privacy van gegevens.