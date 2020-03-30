---
title: Azure Active Directory-woordenlijst voor identiteitsbeveiliging
description: Azure Active Directory-woordenlijst voor identiteitsbeveiliging
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232347"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory-woordenlijst voor identiteitsbeveiliging

### <a name="at-risk-user"></a>Risico (Gebruiker)
Een gebruiker met een of meer actieve risicodetecties. 

### <a name="atypical-sign-in-location"></a>Atypische aanmeldingslocatie
Een aanmelding vanaf een geografische locatie die niet typisch is voor de specifieke gebruiker, vergelijkbare gebruikers of de tenant.

### <a name="azure-ad-identity-protection"></a>Azure AD-identiteitsbeveiliging
Een beveiligingsmodule van Azure Active Directory die een geconsolideerd overzicht biedt van risicodetecties en potentiële kwetsbaarheden die de identiteit van een organisatie beïnvloeden.

### <a name="conditional-access"></a>Voorwaardelijke toegang
Een beleid voor het beveiligen van de toegang tot bronnen. Regels voor voorwaardelijke toegang worden opgeslagen in de Azure Active Directory en worden geëvalueerd door Azure AD voordat ze toegang tot de bron verlenen.  Voorbeelden van regels zijn het beperken van de toegang op basis van de locatie van de gebruiker, de status van het apparaat of de verificatiemethode voor gebruikers.

### <a name="credentials"></a>Referenties
Informatie die identificatie en bewijs van identificatie bevat die wordt gebruikt om toegang te krijgen tot lokale en netwerkbronnen. Voorbeelden van referenties zijn gebruikersnamen en wachtwoorden, smartcards en certificaten.

### <a name="event"></a>Gebeurtenis
Een record van een activiteit in Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Vals-positief (risicodetectie)
Een status van risicodetectie die handmatig is ingesteld door een gebruiker van identiteitsbeveiliging, wat aangeeft dat de risicodetectie is onderzocht en ten onrechte is gemarkeerd als risicodetectie.

### <a name="identity"></a>Identiteit
Een persoon of entiteit die moet worden geverifieerd door middel van authenticatie, op basis van criteria zoals wachtwoord of een certificaat.

### <a name="identity-risk-detection"></a>Detectie van identiteitsrisico's
Azure AD-gebeurtenis die door Identiteitsbeveiliging als afwijkend is gemarkeerd en die erop kan wijzen dat een identiteit is aangetast.

### <a name="ignored-risk-detection"></a>Genegeerd (risicodetectie)
Een status van risicodetectie die handmatig is ingesteld door een gebruiker van identiteitsbeveiliging, wat aangeeft dat de risicodetectie is gesloten zonder een herstelactie te ondernemen.

### <a name="impossible-travel-from-atypical-locations"></a>Onmogelijk reizen vanaf atypische locaties
Een risicodetectie die wordt geactiveerd wanneer twee aanmeldingen voor dezelfde gebruiker worden gedetecteerd, waarbij ten minste één van hen afkomstig is van een atypische aanmeldingslocatie en waarbij de tijd tussen de aanmeldingen korter is dan de minimale tijd die nodig is om fysiek tussen deze Locaties.  

### <a name="investigation"></a>Onderzoek
Het proces van het beoordelen van de activiteiten, logboeken en andere relevante informatie met betrekking tot een risicodetectie om te beslissen of herstel- of mitigatiestappen nodig zijn, te begrijpen of en hoe de identiteit is aangetast en te begrijpen hoe de gecompromitteerde identiteit werd gebruikt.

### <a name="leaked-credentials"></a>Gelekte referenties
Een risicodetectie geactiveerd wanneer de huidige gebruikersreferenties (gebruikersnaam en wachtwoord) worden gevonden openbaar geplaatst in het Dark Web door onze onderzoekers.

### <a name="mitigation"></a>Oplossing
Een actie om de mogelijkheid van een aanvaller om een gecompromitteerde identiteit of apparaat te misbruiken te beperken of te elimineren zonder de identiteit of het apparaat in een veilige staat te herstellen. Een beperking lost eerdere risicodetecties in verband met de identiteit of het apparaat niet op.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Een verificatiemethode waarvoor twee of meer verificatiemethoden nodig zijn, waaronder mogelijk iets dat de gebruiker heeft, een dergelijk certificaat; iets wat de gebruiker kent, zoals gebruikersnamen, wachtwoorden of wachtwoordzinnen; fysieke kenmerken, zoals een duimafdruk; en persoonlijke kenmerken, zoals een persoonlijke handtekening.

### <a name="offline-detection"></a>Offlinedetectie
Het opsporen van afwijkingen en de evaluatie van het risico van een gebeurtenis, zoals aanmeldingspoging na het feit, voor een gebeurtenis die al is gebeurd.

### <a name="policy-condition"></a>Beleidsvoorwaarde
Een onderdeel van een beveiligingsbeleid, dat de entiteiten definieert (groepen, gebruikers, apps, apparaatplatforms, apparaatstatussen, IP-bereiken, clienttypen) die in het beleid zijn opgenomen of daarvan zijn uitgesloten.

### <a name="policy-rule"></a>Beleidsregel
Het deel van een beveiligingsbeleid dat de omstandigheden beschrijft die het beleid zouden activeren en de acties die worden ondernomen wanneer het beleid wordt geactiveerd.

### <a name="prevention"></a>Preventie
Een actie om schade aan de organisatie te voorkomen door misbruik van een identiteit of apparaat verdacht of weten te worden aangetast. Een preventieactie beveiligt het apparaat of de identiteit niet en lost eerdere risicodetecties niet op.

### <a name="privileged-user"></a>Geprivilegieerd (gebruiker)
Een gebruiker die op het moment van een risicodetectie permanente of tijdelijke beheerdersmachtigingen had voor een of meer bronnen in Azure Active Directory, zoals een globale beheerder, factureringsbeheerder, servicebeheerder, gebruikersbeheerder en wachtwoord Beheerder. 

### <a name="real-time"></a>Real-time
Zie Real-time detectie.

### <a name="real-time-detection"></a>Real-time detectie
Het opsporen van afwijkingen en de evaluatie van het risico van een gebeurtenis, zoals aanmeldingspoging voordat het evenement wordt toegestaan, mogen doorgaan.

### <a name="remediated-risk-detection"></a>Gesaneerd (risicodetectie)
Een status van risicodetectie die automatisch wordt ingesteld door Identiteitsbeveiliging, wat aangeeft dat de risicodetectie is gesaneerd met behulp van de standaardherstelactie voor dit type risicodetectie. Wanneer het gebruikerswachtwoord bijvoorbeeld opnieuw wordt ingesteld, worden veel risicodetecties die aangeven dat het vorige wachtwoord is gecompromitteerd, automatisch gesaneerd.

### <a name="remediation"></a>Herstel
Een actie om een identiteit of een apparaat te beveiligen waarvan eerder werd vermoed of bekend was dat deze is gecompromitteerd. Een herstelactie herstelt de identiteit of het apparaat in een veilige staat en lost eerdere risicodetecties op die zijn gekoppeld aan de identiteit of het apparaat.

### <a name="resolved-risk-detection"></a>Opgelost (risicodetectie)
Een status van risicodetectie die handmatig is ingesteld door een gebruiker met identiteitsbescherming, wat aangeeft dat de gebruiker een passende herstelactie buiten identiteitsbescherming heeft uitgevoerd en dat de risicodetectie als gesloten moet worden beschouwd.

### <a name="risk-detection-status"></a>Status van risicodetectie
Een eigenschap van een risicodetectie, die aangeeft of de gebeurtenis actief is en indien gesloten, de reden voor het sluiten ervan.

### <a name="risk-detection-type"></a>Type risicodetectie
Een categorie voor de risicodetectie, die het type anomalie aangeeft waardoor de gebeurtenis als riskant werd beschouwd.

### <a name="risk-level-risk-detection"></a>Risiconiveau (risicodetectie)
Een indicatie (Hoog, Gemiddeld of Laag) van de ernst van de risicodetectie om gebruikers van identiteitsbescherming te helpen prioriteit te geven aan de acties die ze ondernemen om het risico voor hun organisatie te verminderen. 

### <a name="risk-level-sign-in"></a>Risiconiveau (aanmelden)
Een indicatie (Hoog, Gemiddeld of Laag) van de waarschijnlijkheid dat iemand anders voor een specifieke aanmelding de identiteit van de gebruiker probeert te gebruiken.

### <a name="risk-level-user-compromise"></a>Risiconiveau (gebruikerscompromis)
Een indicatie (Hoog, Gemiddeld of Laag) van de waarschijnlijkheid dat een identiteit is aangetast.

### <a name="risk-level-vulnerability"></a>Risiconiveau (kwetsbaarheid)
Een indicatie (Hoog, Gemiddeld of Laag) van de ernst van het beveiligingslek om gebruikers van identiteitsbescherming te helpen prioriteit te geven aan de acties die ze ondernemen om het risico voor hun organisatie te verminderen.

### <a name="secure-identity"></a>Veilig (identiteit)
Neem herstelactie zoals een wachtwoordwijziging of machinereimaging om een mogelijk gecompromitteerde identiteit te herstellen naar een compromisloze status.

### <a name="security-policy"></a>Beveiligingsbeleid
Een verzameling van beleidsregels en voorwaarden. Een beleid kan worden toegepast op entiteiten zoals gebruikers, groepen, apps, apparaten, apparaatplatforms, apparaatstatussen, IP-bereiken en Auth2.0-clienttypen. Wanneer een beleid is ingeschakeld, wordt het geëvalueerd wanneer een entiteit die in het beleid is opgenomen, een token voor een resource wordt uitgegeven.

### <a name="sign-in-v"></a>Log hier in
Verifiëren voor een identiteit in Azure Active Directory.

### <a name="sign-in-n"></a>Log hier in
Het proces of de actie van het verifiëren van een identiteit in Azure Active Directory en de gebeurtenis die deze bewerking vastlegt.

### <a name="sign-in-from-anonymous-ip-address"></a>Inloggen vanaf anoniem IP-adres
Een risicodetectie die wordt geactiveerd na een succesvolle aanmelding vanaf het IP-adres dat is geïdentificeerd als een anoniem proxy-IP-adres.

### <a name="sign-in-from-infected-device"></a>Inloggen vanaf geïnfecteerd apparaat
Een risicodetectie die wordt geactiveerd wanneer een aanmelding afkomstig is van een IP-adres, waarvan bekend is dat het wordt gebruikt door een of meer gecompromitteerde apparaten, die actief proberen te communiceren met een botserver.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Inloggen vanaf IP-adres met verdachte activiteit
Een risicodetectie die is geactiveerd na een succesvolle aanmelding vanaf een IP-adres met een groot aantal mislukte inlogpogingen voor meerdere gebruikersaccounts gedurende een korte periode.

### <a name="sign-in-from-unfamiliar-location"></a>Inloggen vanaf onbekende locatie
Een risicodetectie wordt geactiveerd wanneer een gebruiker zich met succes aanmeldt vanaf een nieuwe locatie (IP, Latitude/Longitude en ASN).

### <a name="sign-in-risk"></a>Aanmeldingsrisico
Zie Risiconiveau (aanmelden)

### <a name="sign-in-risk-policy"></a>Aanmeldingsrisicobeleid
Een beleid voor voorwaardelijke toegang dat het risico voor een specifieke aanmelding evalueert en beperkingen toepast op basis van vooraf gedefinieerde voorwaarden en regels.

### <a name="user-compromise-risk"></a>Risico voor het compromis van de gebruiker
Zie Risiconiveau (gebruikerscompromis)

### <a name="user-risk"></a>Gebruikersrisico
Zie Risiconiveau (gebruikerscompromis).

### <a name="user-risk-policy"></a>Gebruikersrisicobeleid
Een beleid voor voorwaardelijke toegang dat rekening houdt met de aanmelding en beperkingen toepast op basis van vooraf gedefinieerde voorwaarden en regels.

### <a name="users-flagged-for-risk"></a>Gebruikers voor wie wordt aangegeven dat ze risico lopen
Gebruikers met risicodetecties, die actief of gesaneerd zijn

### <a name="vulnerability"></a>Beveiligingsprobleem
Een configuratie of voorwaarde in Azure Active Directory, waardoor de map gevoelig is voor exploits of bedreigingen.

## <a name="see-also"></a>Zie ook

- [Azure Active Directory-identiteitsbeveiliging](../active-directory-identityprotection.md)
