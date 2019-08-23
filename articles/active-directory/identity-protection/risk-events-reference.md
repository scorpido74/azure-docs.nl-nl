---
title: Naslag informatie over Azure Active Directory Identity Protection-risico gebeurtenissen | Microsoft Docs
description: Referentie voor Azure Active Directory Identity Protection risico gebeurtenissen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905198"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Naslag informatie over Azure Active Directory Identity Protection risico gebeurtenissen

Het overgrote deel van de beveiligings Risico's doen zich voor wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Het detecteren van gemanipuleerde identiteiten is geen eenvoudige taak. Azure Active Directory gebruikt adaptieve machine learning algoritmen en heuristiek om verdachte acties te detecteren die betrekking hebben op uw gebruikers accounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record met de naam risico gebeurtenis.

## <a name="anonymous-ip-address"></a>Anoniem IP-adres

**Detectie type:** Real-time  
**Oude naam:** Aanmeldingen vanaf een anoniem IP-adres

Dit type risico gebeurtenis geeft aanmeldingen vanaf een anoniem IP-adres (bijvoorbeeld Tor browser, Anonymizer Vpn's).
Deze IP-adressen worden meestal gebruikt door Actors die hun aanmeld telemetrie (IP-adres, locatie, apparaat enzovoort) willen verbergen voor mogelijk schadelijk doel.

## <a name="atypical-travel"></a>Ongewoon traject

**Detectie type:** Offline  
**Oude naam:** Onmogelijke reis naar ongewone locaties

Dit type risico gebeurtenis identificeert twee aanmeldingen die afkomstig zijn van geografische locaties, waarbij ten minste één van de locaties mogelijk ook ongewoon kan zijn voor de gebruiker, gezien het gedrag van het verleden. Deze machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die nodig zou zijn voor de gebruiker om van de eerste locatie naar de tweede te gaan, wat aangeeft dat een andere gebruiker hetzelfde gebruikt aanmeldings.

Het algoritme negeert duidelijke ' fout-positieven ' die bijdragen aan de niet-bewaarde reis omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. Het systeem heeft een initiële leer periode van het eerste aantal van 14 dagen of 10 aanmeldingen, waarbij het een nieuwe gebruiker aanmeldt.

## <a name="leaked-credentials"></a>Gelekte referenties

**Detectie type:** Offline  
**Oude naam:** Gebruikers van wie de referenties zijn gelekt

Dit type risico gebeurtenis geeft aan dat de geldige referenties van de gebruiker zijn gelekt.
Wanneer Cybercriminals geldige wacht woorden van legitieme gebruikers beschadigt, delen de criminelen deze referenties vaak. Dit wordt doorgaans gedaan door ze openbaar te maken op de donkere web-of plak sites of door de referenties op de zwarte markt te verhandelen of verkopen. De micro soft lekkende referentie Service verkrijgt gebruikers naam-en wachtwoord paren door open bare en donkere websites te bewaken en te werken met:

- Onderzoekers
- Politie
- Beveiligings teams bij micro soft
- Andere vertrouwde bronnen

Wanneer de service gebruikers referenties ophaalt van de donkere websites, plak sites of de bovenstaande bronnen, worden ze gecontroleerd op basis van de huidige geldige referenties van de Azure AD-gebruikers om geldige overeenkomsten te vinden.

## <a name="malware-linked-ip-address"></a>Aan malware gekoppeld IP-adres

**Detectie type:** Offline  
**Oude naam:** Aanmeldingen vanaf geïnfecteerde apparaten

Dit type risico gebeurtenis duidt op aanmeldingen van IP-adressen die zijn geïnfecteerd met malware waarvan bekend is dat deze actief communiceert met een bot-server. Dit wordt bepaald door de IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact komen met een bot-server terwijl de bot-server actief was.

## <a name="unfamiliar-sign-in-properties"></a>Onbekende aanmeldings eigenschappen

**Detectie type:** Real-time  
**Oude naam:** Aanmeldingen vanaf onbekende locaties

Dit type risico gebeurtenis beschouwt eerdere aanmeldings geschiedenis (IP, breedte graad/lengte graad en ASN) om te zoeken naar afwijkende aanmeldingen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en beschouwt deze bekende locaties. De risico gebeurtenis wordt geactiveerd wanneer de aanmelding wordt uitgevoerd vanaf een locatie die zich nog niet in de lijst met bekende locaties bevindt. Nieuwe gebruikers worden in de ' leer modus ' opgenomen gedurende een periode waarin niet-vertrouwde aanmeldings eigenschappen risico gebeurtenissen worden uitgeschakeld, terwijl de algoritmen het gedrag van de gebruiker ontdekken. De duur van de leer modus is dynamisch en is afhankelijk van hoe lang het duurt om voldoende informatie over de aanmeld patronen van de gebruiker te verzamelen. De minimale duur is vijf dagen. Een gebruiker kan na een lange periode van inactiviteit terugkeren naar de leer modus. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een bekende locatie vallen. 

We voeren deze detectie ook uit voor basis verificatie (of verouderde protocollen). Omdat deze protocollen geen moderne eigenschappen hebben, zoals client-ID, is er beperkte telemetrie om fout-positieven te verminderen. We raden onze klanten aan om over te stappen op moderne authenticatie.

## <a name="azure-ad-threat-intelligence"></a>Azure AD-bedreigingsinformatie

**Detectie type:** Offline <br>
**Oude naam:** Deze detectie wordt weer gegeven in de verouderde Azure AD Identity Protection rapporten (gebruikers die zijn gemarkeerd voor risico, risico gebeurtenissen) als ' gebruikers met gelekte referenties '

Dit type risico gebeurtenis duidt op gebruikers activiteit die ongebruikelijk is voor de opgegeven gebruiker of is consistent met bekende aanvals patronen gebaseerd op de interne en externe informatie bronnen van micro soft.

## <a name="admin-confirmed-user-compromised"></a>Beheerder heeft inbraak bij gebruiker bevestigd

**Detectie type:** Offline <br>
Deze detectie geeft aan dat een beheerder in de gebruikers interface van Risk ante gebruikers de gebruiker heeft geknoeid, of met behulp van de riskyUsers-API. Als u wilt zien welke beheerder deze gebruiker heeft bevestigd, controleert u de risico geschiedenis van de gebruiker (via de gebruikers interface of API).
