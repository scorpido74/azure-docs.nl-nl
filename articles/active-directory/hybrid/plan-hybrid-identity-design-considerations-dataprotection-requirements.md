---
title: Hybride identiteitsontwerp - vereisten voor gegevensbescherming Azure | Microsoft Documenten
description: Bij het plannen van uw hybride identiteitsoplossing u de vereisten voor gegevensbescherming voor uw bedrijf identificeren en welke opties beschikbaar zijn om het beste aan deze vereisten te voldoen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918776"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plan voor het verbeteren van de gegevensbeveiliging door middel van een sterke identiteitsoplossing
De eerste stap in het beschermen van gegevens is om te bepalen wie toegang heeft tot die gegevens. U moet ook een identiteitsoplossing hebben die met uw systeem kan worden geïntegreerd om verificatie- en autorisatiemogelijkheden te bieden. Authenticatie en autorisatie worden vaak verward met elkaar en hun rollen verkeerd begrepen. In werkelijkheid zijn ze verschillend, zoals blijkt uit de figuur hieronder:

![levenscyclus van mobiele apparaten](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fasen in het levenscyclusbeheer van mobiele apparaten**

Bij het plannen van uw hybride identiteitsoplossing moet u inzicht krijgen in de vereisten voor gegevensbescherming voor uw bedrijf en welke opties beschikbaar zijn om het beste aan deze vereisten te voldoen.

> [!NOTE]
> Zodra u klaar bent met de planning voor gegevensbeveiliging, controleert [u De vereisten voor meervoudige verificatie](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) bepalen om ervoor te zorgen dat uw selecties met betrekking tot vereisten voor meervoudige verificatie niet worden beïnvloed door de beslissingen die u in deze sectie hebt genomen.
> 
> 

## <a name="determine-data-protection-requirements"></a>Vereisten voor gegevensbescherming bepalen
In het tijdperk van mobiliteit hebben de meeste bedrijven een gemeenschappelijk doel: hun gebruikers in staat stellen productief te zijn op hun mobiele apparaten, terwijl ze on-premises of op afstand van overal zijn om de productiviteit te verhogen. Bedrijven die dergelijke vereisten hebben, zullen zich ook zorgen maken over het aantal bedreigingen dat moet worden beperkt om de gegevens van het bedrijf veilig te houden en de privacy van de gebruiker te behouden. Elk bedrijf kan in dit verband andere eisen stellen; verschillende nalevingsregels die zullen variëren afhankelijk van de bedrijfstak van het bedrijf zal leiden tot verschillende ontwerpbeslissingen. 

Er zijn echter een aantal beveiligingsaspecten die moeten worden onderzocht en gevalideerd, ongeacht de industrie.

## <a name="data-protection-paths"></a>Gegevensbeschermingspaden
![gegevensbeschermingspaden](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Gegevensbeschermingspaden**

In het bovenstaande diagram is de identiteitscomponent de eerste die wordt geverifieerd voordat de gegevens worden geopend. Deze gegevens kunnen echter in verschillende toestanden zijn gedurende de tijd dat deze is geopend. Elk getal in dit diagram vertegenwoordigt een pad waarin gegevens zich op een bepaald moment kunnen bevinden. Deze cijfers worden hieronder uitgelegd:

1. Gegevensbescherming op apparaatniveau.
2. Gegevensbescherming tijdens het transport.
3. Gegevensbescherming in rust on-premises.
4. Gegevensbescherming in de cloud.

Het is noodzakelijk dat de hybride identiteitsoplossing zowel on-premises als cloudidentiteitsbeheerbronnen kan gebruiken om de gebruiker te identificeren voordat deze toegang tot de gegevens verleent. Zorg er bij het plannen van uw hybride identiteitsoplossing voor dat de volgende vragen worden beantwoord volgens de vereisten van uw organisatie:

## <a name="data-protection-at-rest"></a>Gegevensbescherming in rust
Ongeacht waar de gegevens zich in rust bevinden (apparaat, cloud of on-premises), is het belangrijk om een beoordeling uit te voeren om inzicht te krijgen in de behoeften van de organisatie in dit verband. Zorg er voor dit gebied voor dat de volgende vragen worden gesteld:

* Moet uw bedrijf gegevens in rust beschermen?
  * Zo ja, is de hybride identiteitsoplossing in staat om te integreren met uw huidige on-premises infrastructuur?
  * Zo ja, is de hybride identiteitsoplossing in staat om te integreren met uw workloads in de cloud?
* Is het cloudidentiteitsbeheer in staat om de referenties en andere gegevens van de gebruiker die in de cloud zijn opgeslagen, te beschermen?

## <a name="data-protection-in-transit"></a>Gegevensbescherming tijdens het transport
Gegevens die onderweg zijn tussen het apparaat en het datacenter of tussen het apparaat en de cloud moeten worden beschermd. Onderweg zijn betekent echter niet noodzakelijkerwijs een communicatieproces met een component buiten uw cloudservice; het beweegt ook intern, zoals tussen twee virtuele netwerken. Zorg er voor dit gebied voor dat de volgende vragen worden gesteld:

* Moet uw bedrijf gegevens tijdens het transport beschermen?
  * Zo ja, is de hybride identiteitsoplossing in staat om te integreren met beveiligde besturingselementen zoals SSL / TLS?
* Houdt het cloudidentiteitsbeheer het verkeer naar en binnen de directory store (binnen en tussen datacenters) ondertekend?

## <a name="compliance"></a>Naleving
Regelgeving, wetten en nalevingsvereisten voor regelgeving variëren afhankelijk van de branche waartoe uw bedrijf behoort. Bedrijven in hoog gereguleerde industrieën moeten problemen met identiteitsbeheer aanpakken in verband met nalevingskwesties. Regelgeving zoals Sarbanes-Oxley (SOX), de Health Insurance Portability and Accountability Act (HIPAA), de Gramm-Leach-Bliley Act (GLBA) en de Payment Card Industry Data Security Standard (PCI DSS) zijn streng met betrekking tot identiteit en toegang. De hybride identiteitsoplossing die uw bedrijf zal aannemen, moet de kerncapaciteiten hebben die voldoen aan de vereisten van een of meer van deze voorschriften. Zorg er voor dit gebied voor dat de volgende vragen worden gesteld:

* Voldoet de hybride identiteitsoplossing aan de wettelijke vereisten voor uw bedrijf?
* Heeft de hybride identiteitsoplossing 
* in mogelijkheden die uw bedrijf in staat stellen om te voldoen aan wettelijke vereisten? 

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Define Data Protection Strategy](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zal gaan over de beschikbare opties en voordelen / nadelen van elke optie.  Door deze vragen te hebben beantwoord, selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
 [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

