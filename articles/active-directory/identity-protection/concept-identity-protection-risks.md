---
title: Wat is risico? Azure AD-identiteitsbeveiliging
description: Risico's in Azure AD Identity Protection uitleggen
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd0b562ae0570917c9127e301964d089d49b087
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887024"
---
# <a name="what-is-risk"></a>Wat is risico?

Risico detecties in Azure AD Identity Protection omvatten alle geïdentificeerde verdachte acties die betrekking hebben op gebruikers accounts in de Directory.

Identiteits beveiliging biedt organisaties toegang tot krachtige bronnen om snel deze verdachte acties te bekijken en te beantwoorden. 

![Beveiligings overzicht met Risk ante gebruikers en aanmeldingen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risico typen en detectie

Er zijn twee soorten risico **gebruiker** en **aanmelding** , en twee typen detectie of berekeningen in **realtime** en **offline**.

### <a name="user-risk"></a>Gebruikers risico

Een gebruikers risico vertegenwoordigt de waarschijnlijkheid dat een bepaalde identiteit of een bepaald account is aangetast. 

Deze Risico's worden offline berekend met de interne en externe Threat Intelligence-bronnen van micro soft, waaronder beveiligings onderzoekers, politie professionals, beveiligings teams bij micro soft en andere vertrouwde bronnen.

| Risico detectie | Beschrijving |
| --- | --- |
| Gelekte referenties | Dit type risico detectie geeft aan dat de geldige referenties van de gebruiker zijn gelekt. Wanneer Cybercriminals geldige wacht woorden van legitieme gebruikers beveiligt, delen ze deze referenties vaak. Dit delen geschiedt doorgaans door op het donkere web te plaatsen, sites te plakken of door de referenties op de zwarte markt te verhandelen en te verkopen. Wanneer de micro soft lekkende referentie Service gebruikers referenties ophaalt van de donkere websites, plak sites of andere bronnen, worden ze gecontroleerd op basis van de huidige geldige referenties van de Azure AD-gebruikers om geldige overeenkomsten te vinden. |
| Azure AD Threat Intelligence | Dit type risico detectie duidt op de activiteit van de gebruiker die ongebruikelijk is voor de gegeven gebruiker of is consistent met bekende aanvals patronen op basis van de interne en externe informatie bronnen van micro soft. |

### <a name="sign-in-risk"></a>Aanmeldings risico

Een aanmeldings risico duidt op de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit. 

Deze Risico's kunnen in realtime worden berekend of offline worden berekend met behulp van de interne en externe Threat Intelligence-bronnen van micro soft, waaronder beveiligings onderzoekers, politie professionals, beveiligings teams bij micro soft en andere vertrouwde bronnen.

| Risico detectie | Detectie type | Beschrijving |
| --- | --- | --- |
| Anoniem IP-adres | Real-time | Dit type risico detectie duidt op aanmeldingen vanaf een anoniem IP-adres (bijvoorbeeld Tor browser of anoniem VPN). Deze IP-adressen worden meestal gebruikt door Actors die hun aanmeld telemetrie (IP-adres, locatie, apparaat enzovoort) willen verbergen voor mogelijk schadelijk doel. |
| Ongewoone reis | Breken | Dit type risico detectie identificeert twee aanmeldingen die afkomstig zijn van geografische locaties, waarbij ten minste één van de locaties mogelijk ook ongewoon kan zijn voor de gebruiker, gezien het gedrag van het verleden. Deze machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die nodig zou zijn voor de gebruiker om van de eerste locatie naar de tweede te gaan, wat aangeeft dat een andere gebruiker hetzelfde gebruikt aanmeldings. <br><br> Het algoritme negeert duidelijke ' fout-positieven ' die bijdragen aan de niet-bewaarde reis omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. Het systeem heeft een initiële leer periode van het eerste aantal van 14 dagen of 10 aanmeldingen, waarbij het een nieuwe gebruiker aanmeldt. |
| Gekoppeld IP-adres van malware | Breken | Dit type risico detectie duidt op aanmeldingen van IP-adressen die zijn geïnfecteerd met malware waarvan bekend is dat deze actief communiceert met een bot-server. Deze detectie wordt bepaald door de IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact komen met een bot-server terwijl de bot-server actief was. |
| Onbekende aanmeldings eigenschappen | Real-time | Dit type risico detectie beschouwt eerdere aanmeldings geschiedenis (IP, breedte graad/lengte graad en ASN) om te zoeken naar afwijkende aanmeldingen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en beschouwt deze bekende locaties. De risico detectie wordt geactiveerd wanneer de aanmelding wordt uitgevoerd vanaf een locatie die zich nog niet in de lijst met bekende locaties bevindt. Nieuwe gebruikers worden in de ' leer modus ' opgenomen voor een periode waarin niet-bekende aanmeldings eigenschappen risico detecties worden uitgeschakeld terwijl de algoritmen het gedrag van de gebruiker leren kennen. De duur van de leer modus is dynamisch en is afhankelijk van hoe lang het duurt om voldoende informatie over de aanmeld patronen van de gebruiker te verzamelen. De minimale duur is vijf dagen. Een gebruiker kan na een lange periode van inactiviteit terugkeren naar de leer modus. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een bekende locatie vallen. <br><br> We voeren deze detectie ook uit voor basis verificatie (of verouderde protocollen). Omdat deze protocollen geen moderne eigenschappen hebben, zoals client-ID, is er beperkte telemetrie om fout-positieven te verminderen. We raden onze klanten aan om over te stappen op moderne authenticatie. |
| Door beheerder bevestigde gebruiker geknoeid | Breken | Deze detectie geeft aan dat een beheerder in de gebruikers interface van Risk ante gebruikers de gebruiker heeft geknoeid, of met behulp van de riskyUsers-API. Als u wilt zien welke beheerder deze gebruiker heeft bevestigd, controleert u de risico geschiedenis van de gebruiker (via de gebruikers interface of API). |
| Schadelijk IP-adres | Breken | Met deze detectie wordt de aanmelding aangegeven vanaf een schadelijk IP-adres. Een IP-adres wordt beschouwd als schadelijk op basis van hoge fout frequenties vanwege ongeldige referenties die zijn ontvangen van het IP-adres of andere IP-reputatie bronnen. |

### <a name="other-risk-detections"></a>Andere risico detecties

| Risico detectie | Detectie type | Beschrijving |
| --- | --- | --- |
| Er is extra risico gedetecteerd | Realtime of offline | Deze detectie geeft aan dat een van de bovenstaande Premium-detecties is gedetecteerd. Aangezien de Premium-detecties alleen zichtbaar zijn voor Azure AD Premium P2-klanten, hebben ze de titel ' extra risico gedetecteerd ' voor klanten zonder Azure AD Premium P2-licenties. |

## <a name="next-steps"></a>Volgende stappen

- [Beleids regels beschikbaar om Risico's te beperken](concept-identity-protection-policies.md)

- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)
