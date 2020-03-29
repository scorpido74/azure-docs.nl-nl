---
title: Identiteitsvereisten voor hybride cloudidentiteitsontwerp Azure | Microsoft Documenten
description: Identificeer de bedrijfsbehoeften van het bedrijf die u ertoe zullen brengen de vereisten voor het hybride identiteitsontwerp te definiëren.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65950891"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Identiteitsvereisten voor uw hybride identiteitsoplossing bepalen
De eerste stap in het ontwerpen van een hybride identiteitsoplossing is het bepalen van de vereisten voor de bedrijfsorganisatie die gebruik zal maken van deze oplossing.  Hybride identiteit begint als een ondersteunende rol (het ondersteunt alle andere cloudoplossingen door verificatie te bieden) en biedt nieuwe en interessante mogelijkheden die nieuwe workloads voor gebruikers ontgrendelen.  Deze workloads of services die u voor uw gebruikers wilt gebruiken, bepalen de vereisten voor het hybride identiteitsontwerp.  Deze services en workloads moeten zowel on-premises als in de cloud gebruik maken van hybride identiteit.  

U moet deze belangrijke aspecten van het bedrijf doornemen om te begrijpen wat het nu een vereiste is en wat het bedrijf voor de toekomst van plan is. Als u niet de zichtbaarheid van de lange termijn strategie voor hybride identiteit ontwerp, is de kans groot dat uw oplossing niet schaalbaar zal zijn als de zakelijke behoeften groeien en veranderen. Het onderstaande diagram toont een voorbeeld van een hybride identiteitsarchitectuur en de workloads die worden ontgrendeld voor gebruikers. Dit is slechts een voorbeeld van alle nieuwe mogelijkheden die kunnen worden ontgrendeld en geleverd met een solide hybride identiteitsstrategie. 

Sommige componenten die deel uitmaken ![van de hybride identiteitsarchitectuur](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Bepalen van de zakelijke behoeften
Elk bedrijf zal verschillende eisen hebben, zelfs als deze bedrijven deel uitmaken van dezelfde industrie, de werkelijke zakelijke eisen kunnen variëren. U nog steeds gebruikmaken van best practices uit de industrie, maar uiteindelijk is het bedrijf zakelijke behoeften die u zal leiden tot het definiëren van de eisen voor de hybride identiteit ontwerp. 

Zorg ervoor dat u de volgende vragen beantwoordt om uw zakelijke behoeften te identificeren:

* Wil uw bedrijf de operationele kosten van IT verlagen?
* Is uw bedrijf op zoek naar cloudassets (SaaS-apps, infrastructuur)?
* Is uw bedrijf op zoek naar modernisering van uw IT?
  * Zijn uw gebruikers mobieler en eisen ze it om uitzonderingen te maken op uw DMZ, zodat verschillende soorten verkeer toegang krijgen tot verschillende bronnen?
  * Heeft uw bedrijf verouderde apps die moesten worden gepubliceerd aan deze moderne gebruikers, maar zijn niet gemakkelijk te herschrijven?
  * Moet uw bedrijf al deze taken uitvoeren en tegelijkertijd onder controle brengen?
* Is uw bedrijf op zoek naar het beveiligen van de identiteit van gebruikers en het verminderen van risico's door nieuwe tools te brengen die gebruikmaken van de expertise van microsoft's Azure-beveiligingsexpertise on-premises?
* Probeert uw bedrijf de gevreesde "externe" accounts on-premises te verwijderen en ze naar de cloud te verplaatsen, waar ze niet langer een sluimerende bedreiging zijn in uw on-premises omgeving?

## <a name="analyze-on-premises-identity-infrastructure"></a>On-premises identiteitsinfrastructuur analyseren
Nu u een idee hebt over uw bedrijfsvereisten, moet u uw on-premises identiteitsinfrastructuur evalueren. Deze evaluatie is belangrijk voor het definiëren van de technische vereisten om uw huidige identiteitsoplossing te integreren in het cloud identity management systeem. Zorg ervoor dat u de volgende vragen beantwoordt:

* Welke authenticatie- en autorisatieoplossing gebruikt uw bedrijf on-premises? 
* Heeft uw bedrijf momenteel on-premises synchronisatieservices?
* Maakt uw bedrijf gebruik van externe identiteitsproviders (IdP)?

U moet zich ook bewust zijn van de cloudservices die uw bedrijf zou kunnen hebben. Het uitvoeren van een beoordeling om inzicht te krijgen in de huidige integratie met SaaS-, IaaS- of PaaS-modellen in uw omgeving is erg belangrijk. Zorg ervoor dat u de volgende vragen beantwoordt tijdens deze beoordeling:

* Heeft uw bedrijf enige integratie met een cloudserviceprovider?
* Zo ja, welke diensten worden gebruikt?
* Is deze integratie momenteel in productie of is het een pilot?

> [!NOTE]
> Cloud Discovery analyseert uw verkeerslogboeken ten opzichte van de cloud-appcatalogus van Microsoft Cloud App Security van meer dan 16.000 cloud-apps die zijn gerangschikt en beoordeeld op basis van meer dan 70 risicofactoren, om u voortdurend inzicht te geven in cloudgebruik, Shadow IT en het risico dat Shadow IT in uw organisatie vormt. Zie [Clouddiscovery instellen](/cloud-app-security/set-up-cloud-discovery)om aan de slag te gaan.
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vereisten voor identiteitsintegratie evalueren
Vervolgens moet u de vereisten voor identiteitsintegratie evalueren. Deze evaluatie is belangrijk om de technische vereisten te definiëren voor hoe gebruikers zich zullen verifiëren, hoe de aanwezigheid van de organisatie eruit zal zien in de cloud, hoe de organisatie autorisatie toestaat en wat de gebruikerservaring zal zijn. Zorg ervoor dat u de volgende vragen beantwoordt:

* Gebruikt uw organisatie federatie, standaardverificatie of beide?
* Is federatie een vereiste?  Vanwege het volgende:
  * SSO op basis van Kerberos
  * Uw bedrijf heeft een on-premises applicaties (in-house of 3rd party) die SAML of soortgelijke federatie mogelijkheden gebruikt.
  * MFA via Smart Cards. RSA SecurID, enz.
  * Clienttoegangsregels die de onderstaande vragen beantwoorden:
    1. Kan ik alle externe toegang tot Office 365 blokkeren op basis van het IP-adres van de client?
    2. Kan ik alle externe toegang tot Office 365 blokkeren, behalve Exchange ActiveSync?
    3. Kan ik alle externe toegang tot Office 365 blokkeren, behalve voor browsergebaseerde apps (OWA, SPO)
    4. Kan ik alle externe toegang tot Office 365 blokkeren voor leden van aangewezen AD-groepen
* Bezorgdheid over beveiliging en controle
* Reeds bestaande investering in federated authenticatie
* Welke naam zal onze organisatie gebruiken voor ons domein in de cloud?
* Heeft de organisatie een aangepast domein?
  1. Is dat domein openbaar en gemakkelijk verifieerbaar via DNS?
  2. Is dat niet zo, dan heeft u een publiek domein dat kan worden gebruikt om een alternatieve UPN in AD te registreren?
* Zijn de gebruikers-id's consistent voor cloudrepresentatie? 
* Heeft de organisatie apps die integratie met cloudservices vereisen?
* Heeft de organisatie meerdere domeinen en gebruiken ze allemaal standaard of federatieve authenticatie?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Toepassingen evalueren die in uw omgeving worden uitgevoerd
Nu u een idee hebt over uw on-premises en cloudinfrastructuur, moet u de toepassingen evalueren die in deze omgevingen worden uitgevoerd. Deze evaluatie is belangrijk om de technische vereisten te definiëren om deze toepassingen te integreren in het cloud identity management systeem. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar zullen onze applicaties wonen?
* Krijgen gebruikers toegang tot on-premises applicaties?  In de cloud? Of allebei?
* Zijn er plannen om de bestaande toepassingsworkloads naar de cloud te verplaatsen?
* Zijn er plannen om nieuwe toepassingen te ontwikkelen die on-premises of in de cloud zullen verblijven en die cloudverificatie gebruiken?

## <a name="evaluate-user-requirements"></a>Gebruikersvereisten evalueren
U moet ook de gebruikersvereisten evalueren. Deze evaluatie is belangrijk om de stappen te definiëren die nodig zijn voor het instappen en het assisteren van gebruikers bij de overgang naar de cloud. Zorg ervoor dat u de volgende vragen beantwoordt:

* Krijgen gebruikers on-premises toegang tot applicaties?
* Krijgen gebruikers toegang tot applicaties in de cloud?
* Hoe loggen gebruikers meestal in op hun on-premises omgeving?
* Hoe loggen gebruikers in bij de cloud?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Bepaal incident response eisen](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zal gaan over de beschikbare opties en voor-/tegens van elke optie.  Door deze vragen te hebben beantwoord, selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Adreslijstsynchronisatievereisten bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

