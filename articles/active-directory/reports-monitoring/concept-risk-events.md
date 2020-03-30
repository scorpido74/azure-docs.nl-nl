---
title: Azure Active Directory-risicodetecties | Microsoft Documenten
description: Deze artice geeft u een gedetailleerd overzicht van wat risicodetecties zijn.
services: active-directory
keywords: azure active directory identity protection, security, risk, risk level, vulnerability, security policy
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
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383953"
---
# <a name="azure-active-directory-risk-detections"></a>Risicodetecties van Azure Active Directory

De overgrote meerderheid van de inbreuken op de beveiliging vindt plaats wanneer aanvallers toegang krijgen tot een omgeving door het stelen van de identiteit van een gebruiker. Het ontdekken van gecompromitteerde identiteiten is geen gemakkelijke taak. Azure Active Directory maakt gebruik van adaptieve machine learning-algoritmen en heuristiek om verdachte acties te detecteren die gerelateerd zijn aan uw gebruikersaccounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record genaamd een **risicodetectie.**

Er zijn twee plaatsen waar u gerapporteerde risicodetecties bekijkt:

 - **Azure AD-rapportage** - Risicodetecties maken deel uit van de beveiligingsrapporten van Azure AD. Zie voor meer informatie het [beveiligingsrapport gebruikers met risico's](concept-user-at-risk.md) en het [risicovolle beveiligingsrapport voor aanmeldingen](concept-risky-sign-ins.md).

 - **Azure AD-identiteitsbeveiliging** - Risicodetecties maken ook deel uit van de rapportagemogelijkheden van [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Daarnaast u de [API voor risicodetecties van identiteitsbescherming](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) gebruiken om programmatische toegang te krijgen tot beveiligingsdetecties met Microsoft Graph. Zie [Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/graph-get-started.md)voor meer informatie. 

Momenteel detecteert Azure Active Directory zes typen risicodetecties:

- [Gebruikers van wie de referenties zijn gelekt](#leaked-credentials) 
- [Aanmeldingen vanaf anonieme IP-adressen](#sign-ins-from-anonymous-ip-addresses) 
- [Onmogelijke reis naar ongewone locaties](#impossible-travel-to-atypical-locations) 
- [Aanmeldingen vanaf geïnfecteerde apparaten](#sign-ins-from-infected-devices) 
- [Aanmeldingen van IP-adressen met verdachte activiteit](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Aanmeldingen vanaf onbekende locaties](#sign-in-from-unfamiliar-locations) 

![Risicodetectie](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Soms vindt u een risicodetectie zonder een overeenkomstige aanmeldingsvermelding in het [aanmeldingsrapport.](concept-sign-ins.md) Identiteitsbescherming evalueert namelijk het risico voor zowel **interactieve** als **niet-interactieve** aanmeldingen, terwijl in het aanmeldingsrapport alleen de interactieve aanmeldingen worden weergegeven.

Het inzicht dat u krijgt voor een gedetecteerde risicodetectie is gekoppeld aan uw Azure AD-abonnement. 

* Met de **Azure AD Premium P2-editie**krijgt u de meest gedetailleerde informatie over alle onderliggende detecties. 
* Met de **Azure AD Premium P1-editie**vallen geavanceerde detecties (zoals onbekende aanmeldingseigenschappen) niet onder uw licentie en worden ze weergegeven onder de naam **Sign-in met extra risico's gedetecteerd.** Bovendien worden de risico- en risicodetailvelden verborgen.

Hoewel de detectie van risicodetecties al een belangrijk aspect is van het beschermen van uw identiteit, hebt u ook de mogelijkheid om deze handmatig aan te pakken of geautomatiseerde antwoorden te implementeren door beleid voor voorwaardelijke toegang te configureren. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) voor meer informatie.

## <a name="risk-detection-types"></a>Risicodetectietypen

De eigenschap **van het type risicodetectie** is een id voor de verdachte actie waarvoor een risicodetectierecord is gemaakt.

De voortdurende investeringen van Microsoft in het detectieproces leiden tot:

- Verbeteringen in de detectienauwkeurigheid van bestaande risicodetecties 
- Nieuwe soorten risicodetectie die in de toekomst zullen worden toegevoegd

### <a name="leaked-credentials"></a>Gelekte referenties

Wanneer cybercriminelen geldige wachtwoorden van legitieme gebruikers compromitteren, delen ze die referenties vaak. Dit wordt meestal gedaan door ze openbaar te plaatsen op het dark web of sites te plakken of door de referenties op de zwarte markt te verhandelen of te verkopen. De Microsoft gelekte credentials service verwerft gebruikersnaam / wachtwoord paren door het toezicht op openbare en donkere websites en door te werken met:

- Onderzoekers
- Rechtshandhaving
- Security teams bij Microsoft
- Andere vertrouwde bronnen 

Wanneer de service gebruikersnaam/ wachtwoordparen verwerft, worden ze gecontroleerd op basis van de huidige geldige referenties van AAD-gebruikers. Wanneer een overeenkomst wordt gevonden, betekent dit dat het wachtwoord van een gebruiker is gecompromitteerd en dat er een **gelekte risicodetectie** voor referenties wordt gemaakt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Aanmeldingen vanaf anonieme IP-adressen

Dit type risicodetectie identificeert gebruikers die zich met succes hebben aangemeld vanaf een IP-adres dat is geïdentificeerd als een anoniem proxy-IP-adres. Deze proxy's worden gebruikt door mensen die het IP-adres van hun apparaat willen verbergen en kunnen worden gebruikt voor kwade bedoelingen.

### <a name="impossible-travel-to-atypical-locations"></a>Onmogelijke reis naar ongewone locaties

Dit type risicodetectie identificeert twee aanmeldingen afkomstig van geografisch ver afgelegen locaties, waar ten minste één van de locaties ook atypisch kan zijn voor de gebruiker, gezien gedrag uit het verleden. Dit machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die de gebruiker nodig zou hebben om van de eerste locatie naar de tweede te reizen, wat aangeeft dat een andere gebruiker dezelfde Referenties.

Het algoritme negeert duidelijke "false positives" die bijdragen aan de onmogelijke reisomstandigheden, zoals VPN's en locaties die regelmatig worden gebruikt door andere gebruikers in de organisatie. Het systeem heeft een initiële leerperiode van 14 dagen waarin het het aanmeldingsgedrag van een nieuwe gebruiker leert. 

### <a name="sign-in-from-unfamiliar-locations"></a>Aanmelden vanaf onbekende locaties

Dit type risicodetectie houdt rekening met eerdere aanmeldingslocaties (IP, Latitude / Lengte en ASN) om nieuwe / onbekende locaties te bepalen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en houdt rekening met deze "vertrouwde" locaties. De risicodetectie wordt geactiveerd wanneer de aanmelding plaatsvindt vanaf een locatie die nog niet in de lijst met bekende locaties staat. Het systeem heeft een initiële leerperiode van 30 dagen, waarin het geen nieuwe locaties markeert als onbekende locaties. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een vertrouwde locatie liggen. 

Identity Protection detecteert aanmeldingen vanaf onbekende locaties ook voor basisverificatie/ legacy-protocollen. Omdat deze protocollen geen moderne vertrouwde functies hebben, zoals client-id, is er niet genoeg telemetrie om fout-positieven te verminderen. Om het aantal gedetecteerde risicodetecties te verminderen, moet u overgaan op moderne verificatie.   

> [!NOTE]
> Als de inloggebruikersnaam en het wachtwoord niet overeenkomen, mislukt de aanmelding en treedt de risicodetectie niet op. Aanmelding vanaf onbekende locatierisicodetecties worden alleen geactiveerd bij succesvolle aanmeldingen.

### <a name="sign-ins-from-infected-devices"></a>Aanmeldingen vanaf geïnfecteerde apparaten

Dit type risicodetectie identificeert aanmeldingen van apparaten die zijn geïnfecteerd met malware, waarvan bekend is dat ze actief communiceren met een botserver. Dit wordt bepaald door IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact stonden met een botserver. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Aanmeldingen van IP-adressen met verdachte activiteit
Dit type risicodetectie identificeert IP-adressen waarvan gedurende een korte periode een groot aantal mislukte aanmeldingspogingen werd waargenomen op meerdere gebruikersaccounts. Dit komt overeen met verkeerspatronen van IP-adressen die door aanvallers worden gebruikt en is een sterke indicator dat accounts al zijn of op het punt staan te worden gecompromitteerd. Dit is een machine learning-algoritme dat duidelijke false-positives negeert, zoals IP-adressen die regelmatig worden gebruikt door andere gebruikers in de organisatie.  Het systeem heeft een initiële leerperiode van 14 dagen waar het het aanmeldingsgedrag van een nieuwe gebruiker en een nieuwe tenant leert.

## <a name="detection-type"></a>Detectietype

De eigenschap detectietype is een indicator **(Real-time** of **Offline)** voor het detectietijdsbestek van een risicodetectie. Momenteel worden de meeste risicodetecties offline gedetecteerd in een nabewerking nadat de risicodetectie is opgetreden.

In de volgende tabel wordt de tijd weergegeven die nodig is om een detectietype weer te geven in een gerelateerd rapport:

| Detectietype | Latentie rapporteren |
| --- | --- |
| Real-time | 5 tot 10 minuten |
| Offline | 2 tot 4 uur |


Voor de typen risicodetectie die Azure Active Directory detecteert, zijn de detectietypen:

| Type risicodetectie | Detectietype |
| :-- | --- | 
| [Gebruikers van wie de referenties zijn gelekt](#leaked-credentials) | Offline |
| [Aanmeldingen vanaf anonieme IP-adressen](#sign-ins-from-anonymous-ip-addresses) | Real-time |
| [Onmogelijke reis naar ongewone locaties](#impossible-travel-to-atypical-locations) | Offline |
| [Aanmeldingen vanaf onbekende locaties](#sign-in-from-unfamiliar-locations) | Real-time |
| [Aanmeldingen vanaf geïnfecteerde apparaten](#sign-ins-from-infected-devices) | Offline |
| [Aanmeldingen van IP-adressen met verdachte activiteit](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Risiconiveau

De eigenschap van een risicodetectie is een indicator (**Hoog**, **Gemiddeld**of **Laag**) voor de ernst en het vertrouwen van een risicodetectie. Deze eigenschap helpt u om prioriteit te geven aan de acties die u moet ondernemen. 

De ernst van de risicodetectie vertegenwoordigt de sterkte van het signaal als voorspeller van identiteitscompromis. Het vertrouwen is een indicator voor de mogelijkheid van false positives. 

Bijvoorbeeld: 

* **Hoog**: Hoog vertrouwen en detectie van risico's met hoge ernst. Deze gebeurtenissen zijn sterke indicatoren dat de identiteit van de gebruiker is aangetast en dat alle gevolgen van gebruikersaccounts onmiddellijk moeten worden gesaneerd.

* **Medium:** Hoge ernst, maar lagere vertrouwensrisicodetectie, of vice versa. Deze gebeurtenissen zijn potentieel riskant en alle gevolgen van gebruikersaccounts moeten worden gesaneerd.

* **Laag**: Weinig vertrouwen en detectie van risico's met lage ernst. Deze gebeurtenis vereist mogelijk geen onmiddellijke actie, maar in combinatie met andere risicodetecties kan een sterke aanwijzing zijn dat de identiteit wordt aangetast.

![Risiconiveau](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Gelekte referenties

Gelekte referenties risicodetecties worden geclassificeerd als een **hoog,** omdat ze een duidelijke indicatie dat de gebruikersnaam en het wachtwoord beschikbaar zijn voor een aanvaller.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Aanmeldingen vanaf anonieme IP-adressen

Het risiconiveau voor dit type risicodetectie is **Gemiddeld** omdat een anoniem IP-adres geen sterke indicatie is van een accountcompromis. We raden u aan onmiddellijk contact op te nemen met de gebruiker om te controleren of deze anonieme IP-adressen gebruikt.


### <a name="impossible-travel-to-atypical-locations"></a>Onmogelijke reis naar ongewone locaties

Onmogelijk reizen is meestal een goede indicator dat een hacker in staat was om succesvol in te loggen. Er kunnen echter false-positives optreden wanneer een gebruiker een nieuw apparaat gebruikt of een VPN gebruikt die doorgaans niet wordt gebruikt door andere gebruikers in de organisatie. Een andere bron van false-positives zijn toepassingen die ten onrechte server IP's doorgeven als client IP's, die de schijn kunnen geven van aanmeldingen die plaatsvinden vanuit het datacenter waar de back-end van die toepassing wordt gehost (vaak zijn dit Microsoft-datacenters, die kan de weergave van aanmeldingen die plaatsvinden van IP-adressen van Microsoft). Als gevolg van deze false-positives is het risiconiveau voor deze risicodetectie **Medium**.

> [!TIP]
> U de hoeveelheid gerapporteerde false-positives voor dit type risicodetectie verminderen door [benoemde locaties te](../active-directory-named-locations.md)configureren. 

### <a name="sign-in-from-unfamiliar-locations"></a>Aanmelden vanaf onbekende locaties

Onbekende locaties kunnen een sterke indicatie geven dat een aanvaller in staat is om een gestolen identiteit te gebruiken. False-positives kunnen optreden wanneer een gebruiker op reis is, een nieuw apparaat uitprobeert of een nieuwe VPN gebruikt. Als gevolg van deze false positives is het risiconiveau voor dit gebeurtenistype **Medium**.

### <a name="sign-ins-from-infected-devices"></a>Aanmeldingen vanaf geïnfecteerde apparaten

Deze risicodetectie identificeert IP-adressen, niet gebruikersapparaten. Als meerdere apparaten zich achter een enkel IP-adres, en slechts enkele worden gecontroleerd door een bot netwerk, aanmeldingen van andere apparaten mijn trigger deze gebeurtenis onnodig, dat is de reden waarom deze risicodetectie is geclassificeerd als **Laag**.  

We raden u aan contact op te nemen met de gebruiker en alle apparaten van de gebruiker te scannen. Het is ook mogelijk dat het persoonlijke apparaat van een gebruiker is geïnfecteerd of dat iemand anders een geïnfecteerd apparaat gebruikte vanaf hetzelfde IP-adres als de gebruiker. Geïnfecteerde apparaten zijn vaak geïnfecteerd door malware die nog niet zijn geïdentificeerd door anti-virus software, en kan ook wijzen op slechte gebruikersgewoonten die kunnen hebben veroorzaakt het apparaat besmet te raken.

Zie het [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)voor meer informatie over het aanpakken van malware-infecties.

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Aanmeldingen van IP-adressen met verdachte activiteit

We raden u aan contact op te nemen met de gebruiker om te controleren of deze daadwerkelijk is aangemeld vanaf een IP-adres dat als verdacht is gemarkeerd. Het risiconiveau voor dit gebeurtenistype is "**Medium**" omdat verschillende apparaten zich achter hetzelfde IP-adres kunnen begeven, terwijl slechts enkele verantwoordelijk zijn voor de verdachte activiteit. 


## <a name="next-steps"></a>Volgende stappen

* [Beveiligingsrapport gebruikers in gevaar](concept-user-at-risk.md)
* [Riskante aanmeldingen beveiligingsrapport](concept-risky-sign-ins.md)
* [Azure AD-identiteitsbeveiliging](../active-directory-identityprotection.md).
