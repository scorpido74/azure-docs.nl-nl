---
title: Bedreigingen - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: Pagina met bedreigingscategorie voor de Microsoft Threat Modeling Tool, met categorieën voor alle blootgestelde gegenereerde bedreigingen.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727835"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Bedreigingen voor Microsoft Threat Modeling Tool

De Threat Modeling Tool is een kernelement van de Microsoft Security Development Lifecycle (SDL). Het stelt softwarearchitecten in staat om potentiële beveiligingsproblemen vroegtijdig te identificeren en te beperken, wanneer ze relatief eenvoudig en kosteneffectief zijn om op te lossen. Als gevolg daarvan vermindert het de totale ontwikkelingskosten sterk. Ook hebben we de tool ontworpen met niet-beveiligingsexperts in het achterhoofd, waardoor bedreigingsmodellering voor alle ontwikkelaars gemakkelijker wordt door duidelijke richtlijnen te bieden voor het maken en analyseren van bedreigingsmodellen.

> Bezoek de **[Threat Modeling Tool](threat-modeling-tool.md)** om vandaag nog aan de slag te gaan!

De Threat Modeling Tool helpt u bij het beantwoorden van bepaalde vragen, zoals die hieronder:

* Hoe kan een aanvaller de verificatiegegevens wijzigen?
* Wat is de impact als een aanvaller de gebruikersprofielgegevens kan lezen?
* Wat gebeurt er als de toegang tot de gebruikersprofieldatabase wordt geweigerd?

## <a name="stride-model"></a>STRIDE-model

Om u beter te helpen bij het formuleren van dit soort gerichte vragen, gebruikt Microsoft het STRIDE-model, dat verschillende soorten bedreigingen categoriseert en de algehele beveiligingsgesprekken vereenvoudigt.

| Categorie | Beschrijving |
| -------- | ----------- |
| **Adresvervalsing (spoofing)** | Het gaat om het illegaal openen en vervolgens gebruiken van authenticatiegegevens van een andere gebruiker, zoals gebruikersnaam en wachtwoord |
| **Knoeien** | Gaat om de kwaadaardige wijziging van gegevens. Voorbeelden hiervan zijn ongeautoriseerde wijzigingen in permanente gegevens, zoals die in een database, en de wijziging van gegevens terwijl deze tussen twee computers via een open netwerk, zoals het internet, worden uitgevoerd |
| **Verwerping** | Gekoppeld aan gebruikers die weigeren een actie uit te voeren zonder dat andere partijen een manier hebben om het tegendeel te bewijzen, bijvoorbeeld, een gebruiker voert een illegale bewerking uit in een systeem dat niet de mogelijkheid heeft om de verboden bewerkingen te traceren. Niet-verwerping verwijst naar het vermogen van een systeem om afwijzingsbedreigingen tegen te gaan. Een gebruiker die een artikel koopt, moet bijvoorbeeld bij ontvangst voor het object tekenen. De leverancier kan vervolgens het ondertekende ontvangstbewijs gebruiken als bewijs dat de gebruiker het pakket heeft ontvangen |
| **Openbaarmaking van informatie** | Het gaat om de blootstelling van informatie aan personen die er geen toegang toe mogen hebben, bijvoorbeeld de mogelijkheid van gebruikers om een bestand te lezen waartoe ze geen toegang hebben gekregen, of de mogelijkheid van een indringer om gegevens te lezen tijdens het transport tussen twee computers |
| **Denial of Service** | DoS-aanvallen (Denial of service) weigeren de service aan geldige gebruikers, bijvoorbeeld door een webserver tijdelijk niet beschikbaar of onbruikbaar te maken. U moet beschermen tegen bepaalde soorten DoS-bedreigingen alleen maar om de beschikbaarheid en betrouwbaarheid van het systeem te verbeteren |
| **Verhoging van privilege** | Een onbevoegde gebruiker krijgt bevoorrechte toegang en heeft daardoor voldoende toegang tot compromissen of vernietiging van het hele systeem. Verhoging van privilege bedreigingen omvatten die situaties waarin een aanvaller effectief is doorgedrongen alle systeem verdediging en deel uitmaken van het vertrouwde systeem zelf, een gevaarlijke situatie inderdaad |

## <a name="next-steps"></a>Volgende stappen

Ga naar **[Threat Modeling Tool Mitigations](threat-modeling-tool-mitigations.md)** om te leren op welke manieren u deze bedreigingen beperken met Azure.