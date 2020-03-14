---
title: Detectie van Azure Active Directorye Risico's | Microsoft Docs
description: Deze artice biedt een gedetailleerd overzicht van de risico detecties.
services: active-directory
keywords: identiteits beveiliging van Azure Active Directory, beveiliging, risico, risico niveau, beveiligings beleid
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1f3755d61b5fa082665cfdb9aa91d1e31e2d4e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266479"
---
# <a name="azure-active-directory-risk-detections"></a>Detectie van Azure Active Directory-Risico's

Het overgrote deel van de beveiligings Risico's doen zich voor wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Het detecteren van gemanipuleerde identiteiten is geen eenvoudige taak. Azure Active Directory gebruikt adaptieve machine learning algoritmen en heuristiek om verdachte acties te detecteren die betrekking hebben op uw gebruikers accounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record met de naam **risico detectie**.

Er zijn twee locaties waar u gerapporteerde risico detecties kunt bekijken:

 - **Azure AD Reporting** : risico detecties maken deel uit van de beveiligings rapporten van Azure AD. Voor meer informatie, zie het beveiligings rapport [gebruikers die risico](concept-user-at-risk.md) lopen, beveiligings rapport en [Risk ante aanmeldingen](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** -risico detecties maken ook deel uit van de rapportage mogelijkheden van [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Daarnaast kunt u de [API voor risico detectie van identiteits beveiliging](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) gebruiken om programmatisch toegang te krijgen tot beveiligings detecties met behulp van Microsoft Graph. Zie [aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/graph-get-started.md)voor meer informatie. 

Momenteel detecteert Azure Active Directory zes soorten risico detecties:

- [Gebruikers met gelekte referenties](#leaked-credentials) 
- [Aanmeldingen vanaf anonieme IP-adressen](#sign-ins-from-anonymous-ip-addresses) 
- [Onmogelijke reis naar ongewone locaties](#impossible-travel-to-atypical-locations) 
- [Aanmeldingen vanaf geïnfecteerde apparaten](#sign-ins-from-infected-devices) 
- [Aanmeldingen vanaf IP-adressen met verdachte activiteiten](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Aanmeldingen vanaf onbekende locaties](#sign-in-from-unfamiliar-locations) 

![Risico detectie](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Soms vindt u een risico detectie zonder een corresponderende aanmeldings vermelding in het [aanmeld rapport](concept-sign-ins.md). Dit komt doordat identiteits beveiliging het risico evalueert voor **interactieve** en **niet-interactieve** aanmeldingen, terwijl in het rapport voor aanmeldingen alleen de interactieve aanmeldingen worden weer gegeven.

Het inzicht dat u krijgt bij een gedetecteerde risico detectie is gekoppeld aan uw Azure AD-abonnement. 

* Met de **Azure AD Premium P2-editie**krijgt u de meest gedetailleerde informatie over alle onderliggende detecties. 
* Met de **Azure AD Premium P1 Edition**worden geavanceerde detecties (zoals niet-vertrouwde aanmeldings eigenschappen) niet gedekt door uw licentie en worden ze weer gegeven onder de naam **aanmelding met extra risico gedetecteerd**. Daarnaast zijn de velden risico niveau en risico detail verborgen.

Hoewel de detectie van risico detecties al een belang rijk aspect vormt van het beveiligen van uw identiteiten, hebt u ook de mogelijkheid om deze hand matig te beantwoorden of om automatische antwoorden te implementeren door beleid voor voorwaardelijke toegang te configureren. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.

## <a name="risk-detection-types"></a>Typen risico detectie

De eigenschap **type risico detectie** is een id voor de verdachte actie waarvoor een risico detectie record is gemaakt.

De continue investeringen van micro soft in het detectie proces leiden tot het volgende:

- Verbeteringen in de nauw keurigheid van de detectie van bestaande risico detecties 
- Nieuwe typen risico detectie die in de toekomst zullen worden toegevoegd

### <a name="leaked-credentials"></a>Gelekte referenties

Wanneer Cybercriminals geldige wacht woorden van legitieme gebruikers beveiligt, delen ze deze referenties vaak. Dit wordt meestal gedaan door ze openbaar te plaatsen op de sites op het donkere web of plakken, of door de referenties op de zwarte markt te handelen of te verkopen. De micro soft lekkende referentie Service verkrijgt gebruikers naam-en wachtwoord paren door open bare en donkere websites te bewaken en te werken met:

- Onderzoekers
- Politie
- Beveiligings teams bij micro soft
- Andere vertrouwde bronnen 

Wanneer de service gebruikers naam/wachtwoord paren ophaalt, worden ze gecontroleerd op basis van de huidige geldige referenties van AAD-gebruikers. Als er een overeenkomst wordt gevonden, betekent dit dat het wacht woord van een gebruiker is aangetast en dat er een **gelekte referentie risico detectie** wordt gemaakt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Aanmeldingen vanaf anonieme IP-adressen

Dit type risico detectie identificeert gebruikers die zijn aangemeld bij een IP-adres dat is geïdentificeerd als een IP-adres van een anonieme proxy. Deze proxy's worden gebruikt door mensen die u wilt verbergen, IP-adres van hun apparaat en kunnen voor kwade bedoelingen worden gebruikt.

### <a name="impossible-travel-to-atypical-locations"></a>Onmogelijke reis naar ongewone locaties

Dit type risico detectie identificeert twee aanmeldingen die afkomstig zijn van geografische locaties, waarbij ten minste één van de locaties mogelijk ook ongewoon kan zijn voor de gebruiker, gezien het gedrag van het verleden. Deze machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die nodig zou zijn voor de gebruiker om van de eerste locatie naar de tweede te gaan, wat aangeeft dat een andere gebruiker hetzelfde gebruikt aanmeldings.

Het algoritme negeert duidelijke ' fout-positieven ' die bijdragen aan de niet-bewaarde reis omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. Het systeem heeft een initiële leer periode van 14 dagen waarin het aanmeldings gedrag van een nieuwe gebruiker wordt beschreven. 

### <a name="sign-in-from-unfamiliar-locations"></a>Aanmelden vanaf onbekende locaties

Dit type risico detectie houdt rekening met eerdere aanmeldings locaties (IP, breedte graad/lengte graad en ASN) om te bepalen of er nieuwe/onbekende locaties zijn. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en beschouwt deze bekende locaties. De risico detectie wordt geactiveerd wanneer de aanmelding wordt uitgevoerd vanaf een locatie die zich nog niet in de lijst met bekende locaties bevindt. Het systeem heeft een initiële leer periode van 30 dagen, waardoor er geen nieuwe locaties worden gemarkeerd als onbekende locaties. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een bekende locatie vallen. 

Identiteits beveiliging detecteert aanmeldingen vanaf onbekende locaties ook voor basis verificatie/verouderde protocollen. Omdat deze protocollen niet over moderne bekende functies beschikken, zoals client-id, is er onvoldoende telemetrie om fout-positieven te verminderen. Als u het aantal gedetecteerde risico detecties wilt verminderen, moet u overstappen op moderne verificatie.   

### <a name="sign-ins-from-infected-devices"></a>Aanmeldingen vanaf geïnfecteerde apparaten

Dit type risico detectie identificeert aanmeldingen van apparaten die zijn geïnfecteerd met malware, waarvan bekend is dat deze actief communiceren met een bot-server. Dit wordt bepaald door de IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact komen met een bot-server. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Aanmeldingen van IP-adressen met verdachte activiteit
Met dit type risico detectie worden IP-adressen geïdentificeerd waarvan een groot aantal mislukte aanmeldings pogingen gedurende een korte periode in meerdere gebruikers accounts is gedetecteerd. Dit komt overeen met de verkeers patronen van IP-adressen die door aanvallers worden gebruikt. Dit is een sterke indicator die de accounts al of op het punt staan te worden aangetast. Dit is een machine learning-algoritme dat duidelijke fout-positieven negeert, zoals IP-adressen die regel matig worden gebruikt door andere gebruikers in de organisatie.  Het systeem heeft een initiële leer periode van 14 dagen waarin het aanmeldings gedrag van een nieuwe gebruiker en een nieuwe Tenant wordt ontdekt.

## <a name="detection-type"></a>Detectie type

De eigenschap detectie type is een indicator (**realtime** of **offline**) voor de detectie periode van een risico detectie. Op dit moment worden de meeste risico detecties tijdens een verwerkings bewerking offline gedetecteerd nadat de risico detectie is opgetreden.

De volgende tabel geeft een overzicht van de tijd die nodig is om een detectie type weer te geven in een gerelateerd rapport:

| Detectie type | Rapportage latentie |
| --- | --- |
| Real-time | 5 tot 10 minuten |
| Off line | 2 tot 4 uur |


Voor de typen risico detectie Azure Active Directory gedetecteerd, zijn de volgende detectie typen:

| Type risico detectie | Detectie type |
| :-- | --- | 
| [Gebruikers met gelekte referenties](#leaked-credentials) | Off line |
| [Aanmeldingen vanaf anonieme IP-adressen](#sign-ins-from-anonymous-ip-addresses) | Real-time |
| [Onmogelijke reis naar ongewone locaties](#impossible-travel-to-atypical-locations) | Off line |
| [Aanmeldingen vanaf onbekende locaties](#sign-in-from-unfamiliar-locations) | Real-time |
| [Aanmeldingen vanaf geïnfecteerde apparaten](#sign-ins-from-infected-devices) | Off line |
| [Aanmeldingen vanaf IP-adressen met verdachte activiteiten](#sign-ins-from-ip-addresses-with-suspicious-activity) | Off line|


## <a name="risk-level"></a>Risico niveau

De eigenschap risico niveau van een risico detectie is een indicator (**hoog**, **gemiddeld**of **laag**) voor de ernst en het vertrouwen van een risico detectie. Deze eigenschap helpt u bij het bepalen van de acties die u moet uitvoeren. 

De ernst van de risico detectie duidt op de sterkte van het signaal als voor speld op identiteits inbreuk. Het vertrouwen is een indicator voor de mogelijkheid van valse positieven. 

Bijvoorbeeld: 

* **Hoog**: hoge betrouw baarheid en hoge risico detectie van de ernst. Deze gebeurtenissen zijn sterke indica toren die zijn aangetast door de identiteit van de gebruiker, en eventuele gebruikers accounts die van invloed zijn op de gebruiker, moeten onmiddellijk worden hersteld.

* **Medium**: hoge Ernst, maar lagere betrouwbaarheids Risico's, of andersom. Deze gebeurtenissen zijn mogelijk riskant en eventuele gevolgen voor gebruikers accounts moeten worden hersteld.

* **Laag**: lage betrouw baarheid en risico detectie met lage urgentie. Deze gebeurtenis vereist mogelijk geen directe actie, maar in combi natie met andere risico detecties kan een sterke indicatie worden geboden dat de identiteit is aangetast.

![Risico niveau](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Gelekte referenties

De beveiligings detecties van lekken worden geclassificeerd als **hoog**, omdat ze een duidelijke indicatie geven dat de gebruikers naam en het wacht woord beschikbaar zijn voor een aanvaller.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Aanmeldingen vanaf anonieme IP-adressen

Het risico niveau voor dit type risico detectie is **gemiddeld** omdat een anoniem IP-adres geen sterke indicatie vormt van een inbreuk op een account. U wordt aangeraden direct contact op te nemen met de gebruiker om te controleren of ze anonieme IP-adressen gebruiken.


### <a name="impossible-travel-to-atypical-locations"></a>Onmogelijke reis naar ongewone locaties

Onmogelijke reis is doorgaans een goede indicatie dat een hacker zich kan aanmelden. Fout-positieven kunnen echter optreden wanneer een gebruiker met een nieuw apparaat reist of een VPN gebruikt dat doorgaans niet wordt gebruikt door andere gebruikers in de organisatie. Een andere bron van false-positieven is toepassingen die server Ip's onjuist door geven als client-Ip's, waardoor het uiterlijk van de aanmeldingen kan worden weer gegeven vanuit het Data Center waar de back-end van de toepassing wordt gehost (vaak dit zijn micro soft-data centers). kan de weer gave van aanmeldingen mogelijk maken op basis van IP-adressen van micro soft. Als gevolg van deze valse-positieven is het risico niveau voor deze risico detectie **gemiddeld**.

> [!TIP]
> U kunt de gerapporteerde fout-positieven voor dit type risico detectie verminderen door [benoemde locaties](../active-directory-named-locations.md)te configureren. 

### <a name="sign-in-from-unfamiliar-locations"></a>Aanmelden vanaf onbekende locaties

Onbekende locaties kunnen een sterke indicatie geven dat een aanvaller een gestolen identiteit kan gebruiken. ONWAAR-positieven kunnen optreden wanneer een gebruiker op reis is, een nieuw apparaat probeert of een nieuwe VPN gebruikt. Als gevolg van deze fout-positieven is het risico niveau voor dit gebeurtenis type **gemiddeld**.

### <a name="sign-ins-from-infected-devices"></a>Aanmeldingen vanaf geïnfecteerde apparaten

Met deze risico detectie worden IP-adressen, niet van gebruikers apparaten geïdentificeerd. Als verschillende apparaten zich achter één IP-adres bevinden, en slechts enkele ervan worden beheerd door een bot-netwerk, wordt deze gebeurtenis onnodig door de aanmeldingen van andere apparaten geactiveerd. Dit is de reden waarom deze risico detectie is geclassificeerd als **laag**.  

U kunt het beste contact opnemen met de gebruiker en alle apparaten van de gebruiker scannen. Het is ook mogelijk dat het persoonlijke apparaat van een gebruiker is geïnfecteerd of dat iemand anders een geïnfecteerd apparaat gebruikt van hetzelfde IP-adres als de gebruiker. Geïnfecteerde apparaten worden vaak geïnfecteerd door malware die nog niet zijn geïdentificeerd door antivirus software, en kunnen ook duiden op onjuiste gebruikers gewoonten, waardoor het apparaat geïnfecteerd kan raken.

Zie het [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)voor meer informatie over het oplossen van malware-infecties.

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Aanmeldingen van IP-adressen met verdachte activiteit

We raden u aan contact op te nemen met de gebruiker om te controleren of ze daad werkelijk zijn aangemeld vanaf een IP-adres dat is gemarkeerd als verdacht. Het risico niveau voor dit gebeurtenis type is '**medium**' omdat verschillende apparaten zich achter hetzelfde IP-adres bevinden, terwijl er slechts enkele voor de verdachte activiteit kan worden verantwoordelijk. 


## <a name="next-steps"></a>Volgende stappen

* [Beveiligings rapport gebruikers tegen risico](concept-user-at-risk.md)
* [Beveiligings rapport Risk ante aanmeldingen](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
