---
title: Wat is risico? Azure AD-identiteitsbeveiliging
description: Risico's uitleggen in Azure AD-identiteitsbeveiliging
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
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253687"
---
# <a name="what-is-risk"></a>Wat is risico?

Risicodetecties in Azure AD Identity Protection omvatten geïdentificeerde verdachte acties met betrekking tot gebruikersaccounts in de map.

Identiteitsbescherming biedt organisaties toegang tot krachtige bronnen om deze verdachte acties snel te zien en erop te reageren. 

![Beveiligingsoverzicht met riskante gebruikers en aanmeldingen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risicotypen en detectie

Er zijn twee soorten risico **gebruiker** en **aanmelding** en twee soorten detectie of berekening **Real-time** en **Offline**.

### <a name="user-risk"></a>Gebruikersrisico

Een gebruikersrisico vertegenwoordigt de waarschijnlijkheid dat een bepaalde identiteit of account wordt aangetast. 

Deze risico's worden offline berekend met behulp van de interne en externe bronnen van Microsoft voor bedreigingsinformatie, waaronder beveiligingsonderzoekers, rechtshandhavingsprofessionals, beveiligingsteams bij Microsoft en andere vertrouwde bronnen.

| Risicodetectie | Beschrijving |
| --- | --- |
| Gelekte referenties | Dit type risicodetectie geeft aan dat de geldige referenties van de gebruiker zijn gelekt. Wanneer cybercriminelen geldige wachtwoorden van legitieme gebruikers compromitteren, delen ze die referenties vaak. Dit delen wordt meestal gedaan door het plaatsen van openbaar op het dark web, plak sites, of door de handel en verkoop van de referenties op de zwarte markt. Wanneer de Microsoft-service voor gelekte referenties gebruikersreferenties verwerft van het dark web, sites plakken of andere bronnen, worden deze gecontroleerd op basis van de huidige geldige referenties van Azure AD-gebruikers om geldige overeenkomsten te vinden. |
| Azure AD-bedreigingsinformatie | Dit type risicodetectie geeft gebruikersactiviteit aan die ongebruikelijk is voor de opgegeven gebruiker of consistent is met bekende aanvalspatronen op basis van de interne en externe threat intelligence-bronnen van Microsoft. |

### <a name="sign-in-risk"></a>Aanmeldingsrisico

Een aanmeldingsrisico vertegenwoordigt de waarschijnlijkheid dat een bepaald verificatieverzoek niet is geautoriseerd door de eigenaar van de identiteit. 

Deze risico's kunnen in realtime worden berekend of offline worden berekend met behulp van de interne en externe threat intelligence-bronnen van Microsoft, waaronder beveiligingsonderzoekers, rechtshandhavingsprofessionals, beveiligingsteams bij Microsoft en andere vertrouwde bronnen.

| Risicodetectie | Detectietype | Beschrijving |
| --- | --- | --- |
| Anoniem IP-adres | Real-time | Dit type risicodetectie geeft aanmeldingen aan vanaf een anoniem IP-adres (bijvoorbeeld Tor-browser of anonieme VPN). Deze IP-adressen worden meestal gebruikt door acteurs die hun inlogtelemetrie (IP-adres, locatie, apparaat, enz.) willen verbergen voor mogelijk kwade opzet. |
| Atypische reizen | Offline | Dit type risicodetectie identificeert twee aanmeldingen afkomstig van geografisch ver afgelegen locaties, waar ten minste één van de locaties ook atypisch kan zijn voor de gebruiker, gezien gedrag uit het verleden. Dit machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die de gebruiker nodig zou hebben om van de eerste locatie naar de tweede te reizen, wat aangeeft dat een andere gebruiker dezelfde Referenties. <br><br> Het algoritme negeert duidelijke "false positives" die bijdragen aan de onmogelijke reisomstandigheden, zoals VPN's en locaties die regelmatig worden gebruikt door andere gebruikers in de organisatie. Het systeem heeft een eerste leerperiode van de vroegste van 14 dagen of 10 aanmeldingen, waarin het het aanmeldingsgedrag van een nieuwe gebruiker leert. |
| Malware gekoppeld IP-adres | Offline | Dit type risicodetectie geeft aanmeldingen van IP-adressen die zijn geïnfecteerd met malware waarvan bekend is dat ze actief communiceren met een botserver. Deze detectie wordt bepaald door IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact stonden met een botserver terwijl de botserver actief was. |
| Onbekende aanmeldingseigenschappen | Real-time | Dit type risicodetectie beschouwt eerdere aanmeldingsgeschiedenis (IP, Latitude / Lengte en ASN) als zoeken naar afwijkende aanmeldingen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en houdt rekening met deze "vertrouwde" locaties. De risicodetectie wordt geactiveerd wanneer de aanmelding plaatsvindt vanaf een locatie die nog niet in de lijst met bekende locaties staat. Nieuw gemaakte gebruikers zullen in "leermodus" voor een periode waarin onbekende aanmeldingseigenschappen risicodetecties worden uitgeschakeld, terwijl onze algoritmen het gedrag van de gebruiker te leren. De duur van de leermodus is dynamisch en is afhankelijk van hoeveel tijd het algoritme nodig heeft om voldoende informatie te verzamelen over de aanmeldingspatronen van de gebruiker. De minimale duur is vijf dagen. Een gebruiker kan na een lange periode van inactiviteit weer in de leermodus gaan. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een vertrouwde locatie liggen. <br><br> We voeren deze detectie ook uit voor basisverificatie (of oudere protocollen). Omdat deze protocollen geen moderne eigenschappen hebben, zoals client-ID, is er beperkte telemetrie om fout-positieven te verminderen. We raden onze klanten aan om over te stappen op moderne authenticatie. |
| Beheerder bevestigd gebruiker gecompromitteerd | Offline | Deze detectie geeft aan dat een beheerder 'Gebruiker gecompromitteerd bevestigen' heeft geselecteerd in de gebruikersinterface voor riskante gebruikers of de API voor riskante gebruikers heeft gebruikt. Als u wilt zien welke beheerder deze gebruiker heeft gecompromitteerd, controleert u de risicogeschiedenis van de gebruiker (via ui of API). |
| Kwaadaardig IP-adres | Offline | Deze detectie geeft aan dat u zich aanmeldt vanaf een kwaadaardig IP-adres. Een IP-adres wordt als kwaadaardig beschouwd op basis van hoge foutpercentages vanwege ongeldige referenties die zijn ontvangen van het IP-adres of andere IP-reputatiebronnen. |
| Regels voor verdachte manipulatie van inboxen | Offline | Deze detectie wordt ontdekt door [Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) Deze detectie profileert uw omgeving en activeert waarschuwingen wanneer verdachte regels die berichten of mappen verwijderen of verplaatsen, zijn ingesteld in het postvak IN van een gebruiker. Dit kan erop wijzen dat het account van de gebruiker is gecompromitteerd, dat berichten opzettelijk worden verborgen en dat het postvak wordt gebruikt om spam of malware in uw organisatie te verspreiden. |
| Onmogelijk traject | Offline | Deze detectie wordt ontdekt door [Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#impossible-travel) Deze detectie identificeert twee gebruikersactiviteiten (is een enkele of meerdere sessies) afkomstig van geografisch verre locaties binnen een periode korter dan de tijd die de gebruiker zou hebben gekost om van de eerste locatie naar de tweede te reizen, met vermelding van dat een andere gebruiker dezelfde referenties gebruikt. |

### <a name="other-risk-detections"></a>Andere risicodetecties

| Risicodetectie | Detectietype | Beschrijving |
| --- | --- | --- |
| Extra risico gedetecteerd | Real-time of offline | Deze detectie geeft aan dat een van de bovenstaande premium detecties is gedetecteerd. Aangezien de premiumdetecties alleen zichtbaar zijn voor Azure AD Premium P2-klanten, hebben ze de titel 'extra risico gedetecteerd' voor klanten zonder Azure AD Premium P2-licenties. |

## <a name="next-steps"></a>Volgende stappen

- [Beleid beschikbaar om risico's te beperken](concept-identity-protection-policies.md)

- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)
