---
title: Hybride identiteitsontwerp - vereisten voor incidentrespons Azure | Microsoft Documenten
description: Bepaal de monitoring- en rapportagemogelijkheden voor de hybride identiteitsoplossing die door IT kan worden gebruikt om acties te ondernemen om potentiële bedreigingen te identificeren en te beperken
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109282"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Stel vereisten voor incidentrespons voor uw hybride identiteitsoplossing
Grote of middelgrote organisaties zullen hoogstwaarschijnlijk een [beveiligingsincidentrespons](https://technet.microsoft.com/library/cc700825.aspx) hebben om IT te helpen acties te ondernemen die neigen naar het niveau van het incident. Het identiteitsbeheersysteem is een belangrijk onderdeel van het incidentresponsproces, omdat het kan worden gebruikt om te bepalen wie een specifieke actie tegen het doel heeft uitgevoerd. De hybride identiteitsoplossing moet monitoring- en rapportagemogelijkheden kunnen bieden die door IT kunnen worden gebruikt om acties te ondernemen om een potentiële bedreiging te identificeren en te beperken. In een typisch incident response plan heb je de volgende fasen als onderdeel van het plan:

1. Eerste beoordeling.
2. Incident communicatie.
3. Schadebeperking en risicoreductie.
4. Identificatie van wat het was compromis en ernst.
5. Bewijsbehoud.
6. Kennisgeving aan de juiste partijen.
7. Systeemherstel.
8. Documentatie.
9. Schade en kostenanalyse.
10. Revisie verwerken en plannen.

Tijdens de identificatie van wat het was compromis en ernst-fase, zal het noodzakelijk zijn om de systemen die zijn gecompromitteerd, bestanden die zijn benaderd te identificeren en de gevoeligheid van die bestanden te bepalen. Uw hybride identiteitssysteem moet aan deze vereisten kunnen voldoen om u te helpen bij het identificeren van de gebruiker die deze wijzigingen heeft aangebracht. 

## <a name="monitoring-and-reporting"></a>Controle en rapportage
Vaak kan het identiteitssysteem ook helpen in de eerste beoordelingsfase, voornamelijk als het systeem heeft ingebouwd in auditing- en rapportagemogelijkheden. Tijdens de eerste beoordeling moet de IT-beheerder een verdachte activiteit kunnen identificeren of moet het systeem deze automatisch kunnen activeren op basis van een vooraf geconfigureerde taak. Veel activiteiten kunnen wijzen op een mogelijke aanval, maar in andere gevallen kan een slecht geconfigureerd systeem leiden tot een aantal valse positieven in een inbraakdetectiesysteem. 

Het identiteitsbeheersysteem moet IT-beheerders helpen bij het identificeren en rapporteren van deze verdachte activiteiten. Meestal kunnen aan deze technische vereisten worden voldaan door alle systemen te bewaken en een rapportagecapaciteit te hebben die potentiële bedreigingen kan markeren. Gebruik de onderstaande vragen om u te helpen bij het ontwerpen van uw hybride identiteitsoplossing, rekening houdend met de vereisten voor incidentrespons:

* Heeft uw bedrijf een beveiligingsincident reactie op zijn plaats?
  * Zo ja, wordt het huidige identiteitsbeheersysteem gebruikt als onderdeel van het proces?
* Moet uw bedrijf verdachte aanmeldingspogingen van gebruikers op verschillende apparaten identificeren?
* Moet uw bedrijf de referenties van potentiële gecompromitteerde gebruikers detecteren?
* Moet uw bedrijf de toegang en actie van de gebruiker controleren?
* Moet uw bedrijf weten wanneer een gebruiker zijn wachtwoord opnieuw instelt?

## <a name="policy-enforcement"></a>Beleidsafdwinging
Tijdens de schadebeperking en de risicoreductiefase is het belangrijk om de werkelijke en potentiële effecten van een aanval snel te verminderen. Die actie die u op dit punt zal nemen kan het verschil maken tussen een minderjarige en een grote. De exacte reactie is afhankelijk van uw organisatie en de aard van de aanval waarmee u wordt geconfronteerd. Als in de eerste beoordeling is geconcludeerd dat een account is gecompromitteerd, moet u beleid afdwingen om dit account te blokkeren. Dat is slechts een voorbeeld waar het identiteitsbeheersysteem zal worden benut. Gebruik de onderstaande vragen om u te helpen bij het ontwerpen van uw hybride identiteitsoplossing, waarbij u rekening houdt met de manier waarop beleid wordt afgedwongen om te reageren op een lopend incident:

* Heeft uw bedrijf beleid om gebruikers te blokkeren van toegang tot het netwerk indien nodig?
  * Zo ja, integreert de huidige oplossing met het hybride identiteitsbeheersysteem dat u gaat gebruiken?
* Moet uw bedrijf voorwaardelijke toegang afdwingen voor gebruikers die in quarantaine zitten? 

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Definieer gegevensbescherming strategie](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zal gaan over de beschikbare opties en voordelen / nadelen van elke optie.  Door deze vragen te hebben beantwoord, selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Strategie voor gegevensbescherming definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

