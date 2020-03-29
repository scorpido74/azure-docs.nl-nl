---
title: Overwegingen voor hybride identiteitsontwerp in Azure Active Directory - overzicht | Microsoft Documenten
description: Overzicht en inhoud kaart van Hybrid Identity design overwegingen gids
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381456"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Ontwerpoverwegingen voor Azure Active Directory Hybrid Identity
Consumentenapparaten verspreiden de bedrijfswereld en cloudgebaseerde software-as-a-service (SaaS)-toepassingen zijn eenvoudig te gebruiken. Als gevolg hiervan is het een uitdaging om de controle over de toegang tot applicaties van gebruikers in interne datacenters en cloudplatforms te behouden.  

De identiteitsoplossingen van Microsoft omvatten mogelijkheden voor zowel on-premises als in de cloud. Er wordt één gebruikersidentiteit gemaakt voor verificatie en autorisatie bij alle resources, ongeacht hun locatie. Dit concept staat bekend als Hybrid Identity. Er zijn verschillende ontwerp- en configuratieopties voor hybride identiteit met behulp van Microsoft-oplossingen en in sommige gevallen kan het moeilijk zijn om te bepalen welke combinatie het beste aan de behoeften van uw organisatie voldoet. 

Deze Hybrid Identity Design Considerations Guide helpt u te begrijpen hoe u een hybride identiteitsoplossing ontwerpen die het beste past bij de zakelijke en technologische behoeften van uw organisatie.  In deze handleiding beschrijft u een reeks stappen en taken die u volgen om u te helpen bij het ontwerpen van een hybride identiteitsoplossing die voldoet aan de unieke vereisten van uw organisatie. Tijdens de stappen en taken presenteert de gids de relevante technologieën en functieopties die beschikbaar zijn voor organisaties om te voldoen aan de functionele en servicekwaliteit (zoals beschikbaarheid, schaalbaarheid, prestaties, beheerbaarheid en beveiligingsniveau) Eisen. 

In het bijzonder, de hybride identiteit overwegingen overwegingen gids doelen zijn het beantwoorden van de volgende vragen: 

* Welke vragen moet ik stellen en beantwoorden om een hybride identiteitsspecifiek ontwerp te besturen voor een technologie of probleemdomein dat het beste aan mijn eisen voldoet?
* Welke reeks activiteiten moet ik voltooien om een hybride identiteitsoplossing voor de technologie of het probleemdomein te ontwerpen? 
* Welke hybride identiteitstechnologie en configuratieopties zijn beschikbaar om mij te helpen aan mijn vereisten te voldoen? Wat zijn de afwegingen tussen deze opties, zodat ik de beste optie voor mijn bedrijf kan selecteren?

## <a name="who-is-this-guide-intended-for"></a>Voor wie is deze handleiding bedoeld?
 CIO, CITO, Chief Identity Architects, Enterprise Architects en IT Architects verantwoordelijk voor het ontwerpen van een hybride identiteitsoplossing voor middelgrote of grote organisaties.

## <a name="how-can-this-guide-help-you"></a>Hoe kan deze handleiding u helpen?
U deze handleiding gebruiken om te begrijpen hoe u een hybride identiteitsoplossing ontwerpt die in staat is om een cloudgebaseerd identiteitsbeheersysteem te integreren met uw huidige on-premises identiteitsoplossing. 

In de volgende afbeelding wordt een voorbeeld weergegeven van een hybride identiteitsoplossing waarmee IT-beheerders hun huidige Windows Server Active Directory-oplossing on-premises kunnen integreren met Microsoft Azure Active Directory , zodat gebruikers Single Sign-On kunnen gebruiken ( SSO) voor applicaties in de cloud en on-premises.

![Voorbeeld](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

De bovenstaande illustratie is een voorbeeld van een hybride identiteitsoplossing die gebruik maakt van cloudservices om te integreren met on-premises mogelijkheden om één ervaring te bieden aan het verificatieproces van eindgebruikers en om IT-beheer te vergemakkelijken Middelen. Hoewel dit voorbeeld een veelvoorkomend scenario kan zijn, is het hybride identiteitsontwerp van elke organisatie waarschijnlijk anders dan het voorbeeld dat in figuur 1 wordt geïllustreerd vanwege verschillende vereisten. 

Deze handleiding bevat een reeks stappen en taken die u volgen om een hybride identiteitsoplossing te ontwerpen die voldoet aan de unieke vereisten van uw organisatie. Tijdens de volgende stappen en taken presenteert de gids de relevante technologieën en functieopties die voor u beschikbaar zijn om te voldoen aan functionele en servicekwaliteitsniveauvereisten voor uw organisatie.

**Aannames:** u hebt enige ervaring met Windows Server, Active Directory Domain Services en Azure Active Directory. In dit document wordt ervan uitgegaan dat u op zoek bent naar hoe deze oplossingen zelf aan uw bedrijfsbehoeften kunnen voldoen, of in een geïntegreerde oplossing.

## <a name="design-considerations-overview"></a>Overzicht ontwerpoverwegingen
Dit document bevat een reeks stappen en taken die u volgen om een hybride identiteitsoplossing te ontwerpen die het beste aan uw vereisten voldoet. De stappen worden op een geordende wijze weergegeven. Ontwerpoverwegingen die u in latere stappen leert, vereisen mogelijk dat u beslissingen wijzigt die u in eerdere stappen hebt gemaakt, vanwege conflicterende ontwerpkeuzes. Elke poging wordt gedaan om u te waarschuwen voor mogelijke ontwerpconflicten in het hele document. 

U komt bij het ontwerp dat het beste aan uw eisen voldoet pas na het doorlopen van de stappen zo vaak als nodig is om alle overwegingen in het document op te nemen. 

| Hybride identiteitsfase | Onderwerplijst |
| --- | --- |
| Identiteitsvereisten vaststellen |[Bepalen van de zakelijke behoeften](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Adreslijstsynchronisatievereisten bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Vereisten voor meervoudige verificatie bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Een strategie voor hybride identiteitsacceptatie definiëren](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plan voor het verbeteren van de gegevensbeveiliging door middel van een sterke identiteitsoplossing |[Vereisten voor gegevensbescherming bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Strategie voor gegevensbescherming definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| De levenscyclus van een hybride identiteit plannen |[Hybride identiteitsbeheertaken bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Synchronisatiebeheer](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Bepalen hybride identity management adoptie strategie](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Volgende stappen
[Identiteitsvereisten vaststellen](plan-hybrid-identity-design-considerations-business-needs.md)

