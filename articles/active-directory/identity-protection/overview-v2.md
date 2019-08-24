---
title: Wat is Azure Active Directory Identity Protection (vernieuwd)? | Microsoft Docs
description: Wat is Azure Active Directory Identity Protection (vernieuwd)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f7e26e2f32ba02db9afc0676a99003717ff585
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991080"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Wat is Azure Active Directory Identity Protection (vernieuwd)?

De ervaring voor identiteits beveiliging is vernieuwd om de identiteiten van uw organisatie beter te beveiligen. Deze vernieuwde ervaring biedt:

- Opnieuw ontworpen beheerders ervaring die het risico vormt dat het meest relevant is voor uw gebruikers risico en aanmeldings risico
- Krachtige onderzoek ervaring met ondersteuning voor filteren, sorteren en slimme down loads
- De berekening van het gebruikers risico is verbeterd om u te helpen bij het bepalen van uw inspanningen bij de gebruikers die het meest waarschijnlijk worden aangetast
- Nieuwe API-ondersteuning om programmatische toegang tot risico gegevens in te scha kelen
- Vereenvoudigd beheer feedback proces waarmee u uw gebruikers onmiddellijk kunt beveiligen
- Nieuw onder Super visie machine learning om de nauw keurigheid van risico beoordelingen te verbeteren

Beveiliging is de belangrijkste reden voor organisaties. Het meren deel van de schending van de beveiliging gebeurt wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Over de jaren zijn aanvallers steeds effectiever geworden bij inbreuken van derden en met behulp van geavanceerde phishing-aanvallen. Zodra aanvallers toegang krijgen tot zelfs gelaagde gebruikers accounts met beperkte rechten, is het relatief eenvoudig om toegang te krijgen tot belang rijke bedrijfs resources via een later verplaatsings beweging. 

Azure AD Identity Protection biedt u de volgende mogelijkheden om te reageren op deze bedreigingen: 

- Proactief voor komen dat identiteiten misbruikt worden 
- Automatisch risico beperken wanneer verdachte activiteit wordt gedetecteerd 
- Risk ante gebruikers en aanmeldingen onderzoeken om mogelijke beveiligings problemen op te lossen  
- Waarschuwen wanneer een gebruiker risico een opgegeven drempel waarde bereikt 

Azure AD Identity Protection is een functie van Azure Active Directory Premium P2 waarmee u beleid kunt configureren om automatisch te reageren wanneer de identiteit van een gebruiker is aangetast of als iemand anders dan de account eigenaar probeert zich aan te melden met hun persoon. Deze beleids regels, naast andere besturings elementen voor voorwaardelijke toegang die worden geleverd door Azure AD, kunnen de toegang automatisch blok keren of actie ondernemen, zoals het opnieuw instellen van het wacht woord of het afdwingen van multi-factor Authentication. Daarnaast biedt identiteits beveiliging bewakings-en rapportage mogelijkheden waarmee u meer inzicht krijgt in Risico's en mogelijke inbreuken in uw organisatie. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Risicogebeurtenissen

Azure AD Identity Protection detecteert de volgende risico gebeurtenissen: 

| Type risicogebeurtenis | Description | Detectietype |
| --- | --- | --- |
| Ongewoon traject | Meld u vanaf een ongewoone locatie aan op basis van de recente aanmeldingen van de gebruiker. | Offline |
| Anoniem IP-adres | Meld u aan vanaf een anoniem IP-adres (bijvoorbeeld: Tor-browser, Anonymizer Vpn's). | Real-time |
| Onbekende aanmeldings eigenschappen | Meld u aan met de eigenschappen die niet recent voor de gegeven gebruiker zijn weer gegeven. | Real-time |
| Aan malware gekoppeld IP-adres | Meld u aan bij een IP-adres dat is gekoppeld aan malware | Offline |
| Gelekte referenties | Deze risico gebeurtenis geeft aan dat de geldige referenties van de gebruiker zijn gelekt | Offline |

## <a name="types-of-risk"></a>Risico typen 

Identiteits beveiliging is gebaseerd op twee risico typen:

- Aanmeldings risico
- Gebruikersrisico

### <a name="sign-in-risk"></a>Aanmeldings risico

Een aanmeldings risico duidt op de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit.

Er zijn twee evaluaties van aanmeldings risico: 

- **Aanmeldings risico (realtime)** : het aanmeldings risico (realtime) is gebaseerd op alle real-time detecties die activeren tijdens de verwerking van de aanmelding...  
- **Aanmeldings risico (aggregatie)** : het aanmeldings risico (aggregatie) is het totale risico van een aanmelding. Het wordt berekend op basis van een machine learning model dat in aanmerking komt voor:
   - Real-time detecties (hierboven beschreven)
   - Offline detecties (die worden geactiveerd nadat het aanmelden is uitgevoerd) 
   - Alle andere functies van de aanmelding

### <a name="user-risk"></a>Gebruikersrisico

Een gebruikers risico duidt op de kans dat een bepaalde identiteit is aangetast. 

Het gebruikers risico wordt berekend door alle Risico's te overwegen die aan de gebruiker zijn gekoppeld:

- Alle Risk ante aanmeldingen
- Alle risico gebeurtenissen die niet zijn gekoppeld aan een aanmelding
- Het huidige gebruikers risico
- Eventuele door de gebruiker geherstelde of afsluitings acties die worden uitgevoerd op de gebruikers datum

## <a name="how-identity-protection-detects-risk"></a>Hoe identiteits beveiliging het risico detecteert  

Azure AD gebruikt machine learning om afwijkingen en verdachte activiteiten te detecteren, waarbij beide signalen in realtime worden gedetecteerd tijdens aanmeldingen en niet-real-time signalen die zijn gerelateerd aan gebruikers en hun aanmeldings activiteiten. Met behulp van deze gegevens berekent identiteits beveiliging een realtime-aanmeldings risico telkens wanneer een gebruiker zich verifieert, en het bepalen van het algehele gebruikers risico niveau voor elke gebruiker. Met identiteits beveiliging kunt u automatisch actie ondernemen op deze risico detecties door gebruikers risico voor identiteits beveiliging en beleids regels voor aanmeldings Risico's te configureren.  

Om inzicht te krijgen in hoe identiteits beveiliging het risico detecteert, zijn er twee belang rijke concepten: gebruikers risico en aanmeldings risico. Aanmeldings risico weerspiegelt de waarschijnlijkheid dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit. Er zijn twee soorten aanmeldings Risico's: realtime en totaal. Realtime aanmeldings risico wordt gedetecteerd op het moment van de aanmeldings poging (zoals aanmeldingen vanuit anonieme IP-adressen). Het totale aanmeldings risico is het samen voegen van gedetecteerde realtime aanmeldings Risico's en eventuele daaropvolgende niet-real-time risico gebeurtenissen die zijn gekoppeld aan de aanmeldingen van de gebruiker (zoals niet mogelijk reis). Gebruikers risico weerspiegelt de algehele kans dat een ongeldige actor een bepaalde identiteit heeft aangetast. Gebruikers risico bevat alle risico activiteiten voor een bepaalde gebruiker, waaronder:

- Real-time aanmeldings risico
- Volgend aanmeldings risico
- Risk ante gebruikers detecties.   

 ![Stroom](./media/overview-v2/01.png)

De basislijn stroom voor de detectie van Risico's voor identiteits beveiliging en reacties voor een bepaalde aanmelding wordt in de bovenstaande afbeelding weer gegeven.  

## <a name="common-scenarios"></a>Algemene scenario's 

Laten we eens kijken naar het voor beeld van een werk nemer van contoso. 

1. Een werk nemer probeert zich vanaf de Tor-browser aan te melden bij Exchange Online. Op het moment van aanmelden detecteert Azure AD in real-time risico gebeurtenissen. 
2. Azure AD detecteert dat de werk nemer zich aanmeldt vanaf een anoniem IP-adres, waardoor het risico niveau gemiddeld wordt geactiveerd. 
3. De werk nemer wordt bevraagd met een MFA-prompt, omdat de IT-beheerder van Contoso het beleid voor voorwaardelijke toegang voor aanmeldings Risico's voor identiteits beveiliging heeft geconfigureerd. Voor het beleid is MFA vereist voor een aanmeldings risico van gemiddeld of hoger. 
4. De werk nemer geeft de MFA-prompt door en opent Exchange Online en het niveau van de gebruikers risico wordt niet gewijzigd. 

Wat is er achter de schermen gebeurd? De aanmeldings poging van de Tor-browser heeft een real-time aanmeldings risico geactiveerd in azure AD voor anoniem IP-adres. Als Azure AD de aanvraag heeft verwerkt, heeft hij het beleid voor aanmeldings Risico's dat is geconfigureerd in identiteits beveiliging toegepast, omdat het risico niveau van de werk nemer de drempel waarde (gemiddeld) heeft bereikt. Omdat de werk nemer eerder is geregistreerd voor MFA, konden ze reageren op de MFA-uitdaging. De mogelijkheid om de MFA-Challenge aan Azure AD door te geven, dat ze waarschijnlijk de rechtmatige identiteits eigenaar waren, en hun gebruikers risico niveau neemt niet toe. 

Maar wat gebeurt er als de werk nemer geen poging doet om zich aan te melden? 

1. Een kwaadwillende actor met de referenties van de werk nemer probeert zich aan te melden bij hun Exchange Online-account via de Tor-browser, omdat ze hun IP-adres proberen te verbergen. 
2. Azure AD detecteert dat de aanmeldings poging afkomstig is van een anoniem IP-adres, waardoor er een real-time aanmeldings risico wordt geactiveerd. 
3. De schadelijke actor wordt gevraagd met een MFA-prompt, omdat de IT-beheerder van Contoso het beleid voor voorwaardelijke toegang van het aanmeldings risico voor identiteits beveiliging heeft geconfigureerd om MFA te vereisen wanneer het aanmeldings risico gemiddeld of hoger is. 
4. De schadelijke actor mislukt de MFA-Challenge en heeft geen toegang tot het Exchange Online-account van de werk nemer. 
5. De mislukte MFA-prompt heeft een risico gebeurtenis geactiveerd die moet worden vastgelegd, waardoor de gebruikers Risico's voor toekomstige aanmeldingen worden verhoogd. 

Nu een kwaadwillende actor heeft geprobeerd toegang te krijgen tot het account van Sarah, gaan we kijken wat er gebeurt wanneer de werk nemer zich de volgende keer probeert aan te melden. 

1. De werk nemer probeert zich vanuit Outlook aan te melden bij Exchange Online. Op het moment van aanmelden detecteert Azure AD realtime-risico gebeurtenissen en eventuele voor gaande gebruikers Risico's. 
2. Azure AD detecteert geen real-time aanmeldings Risico's, maar detecteert hoge gebruikers Risico's als gevolg van de vroegere Risk ante activiteiten in de vorige scenario's.  
3. De werk nemer wordt bevraagd met een prompt voor het opnieuw instellen van een wacht woord, omdat de IT-beheerder van Contoso het beleid voor gebruikers Risico's voor identiteits beveiliging heeft geconfigureerd om wachtwoord wijziging te vereisen wanneer een gebruiker met een hoog risico zich aanmeldt 
4. Omdat de werk nemer is geregistreerd voor SSPR en MFA, zijn hun wacht woord opnieuw ingesteld. 
5. Door het wacht woord opnieuw in te stellen, worden de referenties van de werk nemer niet meer aangetast en wordt de identiteit weer in een veilige staat weer gegeven. 
6. De vorige risico gebeurtenissen van de werk nemer zijn opgelost en hun gebruikers risico niveau wordt automatisch opnieuw ingesteld als reactie op het beperken van de referenties. 

## <a name="how-do-i-configure-identity-protection"></a>Identiteits beveiliging Hoe kan ik configureren? 

Om aan de slag te gaan met identiteits beveiliging, moet u eerst een beleid voor gebruikers Risico's en aanmeldings Risico's configureren. Zodra deze beleids regels zijn geconfigureerd en toegepast op een test groep, kunt u risico gebeurtenissen simuleren om te begrijpen hoe identiteits beveiliging in uw omgeving reageert. De onderstaande Snelstartgids bevat een overzicht van het instellen van het bovengenoemde beleid en de test in uw omgeving. 

Identiteits beveiliging ondersteunt drie rollen in azure AD om de beheer activiteiten te verdelen over uw implementatie: 

| Role | Kan doen | Kan niet |
| --- | --- | --- |
| Globale beheerder | Volledige toegang tot identiteits beveiliging, onboarding-identiteits beveiliging | |
| Beveiligingsbeheerder | Volledige toegang tot identiteits beveiliging | Identiteits beveiliging voor onboarding, wacht woorden opnieuw instellen voor een gebruiker |
| Beveiligingslezer | Alleen-lezen toegang tot identiteits beveiliging | Identiteits beveiliging voor onboarding, gebruikers herstellen, beleid configureren, wacht woorden opnieuw instellen| 

Zie [beheerders rollen toewijzen in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) voor meer informatie.
 
## <a name="licensing"></a>Licenties

>[!NOTE]
> Tijdens de open bare preview van identiteits beveiliging (vernieuwd) hebben alleen Azure AD Premium P2-klanten toegang tot het rapport Risk ante gebruikers en Risk ante aanmeldingen.

| Mogelijkheid | Details | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/gratis |
| --- | --- | --- | --- | --- |
| Risico beleid | Beleid voor gebruikers risico's (via identiteits beveiliging) | Ja | Nee | Nee |
| Risico beleid | Beleid voor aanmeldings risico's (via identiteits beveiliging of voorwaardelijke toegang) | Ja | Nee | Nee |
| Beveiligingsrapporten | Overzicht | Ja | Nee | Nee |
| Beveiligingsrapporten | Risk ante gebruikers | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Riskante aanmeldingen | Volledige toegang | Beperkte informatie | Beperkte informatie |
| Beveiligingsrapporten | Risico detecties | Volledige toegang | Beperkte informatie | Nee |
| Meldingen | Waarschuwingen bij Gebruikers die risico lopen gedetecteerd | Ja | Nee | Nee |
| Meldingen | Wekelijkse samenvatting | Ja | Nee | Nee |
| | MFA-registratiebeleid | Ja | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen 

Zie [aanmeldings risico beleid configureren](quickstart-sign-in-risk-policy.md)om aan de slag te gaan met identiteits beveiliging. 
