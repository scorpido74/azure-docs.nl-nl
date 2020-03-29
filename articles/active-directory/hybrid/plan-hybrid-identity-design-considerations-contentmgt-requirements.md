---
title: Hybride identiteitsontwerp - vereisten voor inhoudsbeheer Azure | Microsoft Documenten
description: Geeft inzicht in hoe u de vereisten voor contentmanagement van uw bedrijf bepalen. Meestal wanneer een gebruiker zijn eigen apparaat heeft, kunnen ze ook meerdere referenties hebben die worden afgewisseld volgens de toepassing die ze gebruiken. Het is belangrijk om te differentiëren welke inhoud is gemaakt met behulp van persoonlijke referenties ten opzichte van de inhoud die is gemaakt met behulp van bedrijfsreferenties. Uw identiteitsoplossing moet kunnen communiceren met cloudservices om de eindgebruiker een naadloze ervaring te bieden en tegelijkertijd hun privacy te waarborgen en de bescherming tegen gegevenslekken te vergroten.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918445"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Bepaal vereisten voor contentbeheer voor uw hybride identiteitsoplossing
Inzicht in de vereisten voor contentbeheer voor uw bedrijf kan rechtstreeks van invloed zijn op uw beslissing over welke hybride identiteitsoplossing u moet gebruiken. Met de proliferatie van meerdere apparaten en de mogelijkheid van gebruikers om hun eigen apparaten mee te nemen[(BYOD),](https://aka.ms/byodcg)moet het bedrijf zijn eigen gegevens beschermen, maar het moet ook de privacy van de gebruiker intact houden. Meestal wanneer een gebruiker zijn eigen apparaat heeft, kunnen ze ook meerdere referenties hebben die worden afgewisseld volgens de toepassing die ze gebruiken. Het is belangrijk om te differentiëren welke inhoud is gemaakt met behulp van persoonlijke referenties ten opzichte van de inhoud die is gemaakt met behulp van bedrijfsreferenties. Uw identiteitsoplossing moet kunnen communiceren met cloudservices om de eindgebruiker een naadloze ervaring te bieden en tegelijkertijd hun privacy te waarborgen en de bescherming tegen gegevenslekken te vergroten. 

Uw identiteitsoplossing wordt gebruikt door verschillende technische besturingselementen om contentmanagement te bieden, zoals in de onderstaande afbeelding wordt weergegeven:

![beveiligingscontroles](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Beveiligingscontroles die uw identiteitsbeheersysteem gebruiken**

In het algemeen maken vereisten voor contentbeheer gebruik van uw identiteitsbeheersysteem op de volgende gebieden:

* Privacy: het identificeren van de gebruiker die eigenaar is van een resource en het toepassen van de juiste besturingselementen om de integriteit te behouden.
* Gegevensclassificatie: identificeer de gebruiker of groep en het niveau van toegang tot een object op basis van de classificatie ervan. 
* Bescherming tegen gegevenslekkage: beveiligingscontroles die verantwoordelijk zijn voor de bescherming van gegevens om lekkage te voorkomen, moeten communiceren met het identiteitssysteem om de identiteit van de gebruiker te valideren. Dit is ook belangrijk voor het controleren van trail doel.

> [!NOTE]
> Lees [gegevensclassificatie voor cloudgereedheid](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) voor meer informatie over aanbevolen procedures en richtlijnen voor gegevensclassificatie.
> 
> 

Bij het plannen van uw hybride identiteitsoplossing zorgt u ervoor dat de volgende vragen worden beantwoord volgens de vereisten van uw organisatie:

* Heeft uw bedrijf beveiligingscontroles om de privacy van gegevens af te dwingen?
  * Zo ja, zullen de beveiligingscontroles in staat zijn om te integreren met de hybride identiteit oplossing die u gaat aannemen?
* Gebruikt uw bedrijf gegevensclassificatie?
  * Zo ja, is de huidige oplossing in staat om te integreren met de hybride identiteitsoplossing die u gaat aannemen?
* Heeft uw bedrijf momenteel een oplossing voor datalekken? 
  * Zo ja, is de huidige oplossing in staat om te integreren met de hybride identiteitsoplossing die u gaat aannemen?
* Moet uw bedrijf de toegang tot resources controleren?
  * Zo ja, wat voor soort middelen?
  * Zo ja, welk niveau van informatie is nodig?
  * Zo ja, waar het controlelogboek moet zich bevinden? On-premises of in de cloud?
* Moet uw bedrijf e-mails versleutelen die gevoelige gegevens bevatten (SSN's, creditcardnummers, enz.)?
* Moet uw bedrijf alle documenten/inhoud versleutelen die worden gedeeld met externe zakenpartners?
* Moet uw bedrijf het bedrijfsbeleid afdwingen op bepaalde soorten e-mails (niet alles beantwoorden, niet doorsturen)?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Define Data Protection Strategy](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zal gaan over de beschikbare opties en voordelen / nadelen van elke optie.  Door deze vragen te hebben beantwoord, selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

