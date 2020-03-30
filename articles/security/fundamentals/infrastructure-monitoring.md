---
title: Azure-infrastructuurbewaking
description: In dit artikel wordt de bewaking van het Azure-productienetwerk besproken.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727154"
---
# <a name="azure-infrastructure-monitoring"></a>Azure-infrastructuurbewaking   

## <a name="configuration-and-change-management"></a>Configuratie- en wijzigingsbeheer
Azure controleert en werkt jaarlijks configuratie-instellingen en basislijnconfiguraties van hardware, software en netwerkapparaten. Wijzigingen worden ontwikkeld, getest en goedgekeurd voordat ze vanuit een ontwikkel- en/of testomgeving in de productieomgeving terechtkomen.

De basislijnconfiguraties die vereist zijn voor Azure-gebaseerde services worden beoordeeld door het Azure-beveiligings- en nalevingsteam en door serviceteams. Een serviceteambeoordeling maakt deel uit van de tests die plaatsvinden vóór de implementatie van hun productieservice.

## <a name="vulnerability-management"></a>Kwetsbaarheidsbeheer
Beveiligingsupdatebeheer helpt systemen te beschermen tegen bekende kwetsbaarheden. Azure maakt gebruik van geïntegreerde implementatiesystemen om de distributie en installatie van beveiligingsupdates voor Microsoft-software te beheren. Azure kan ook gebruik maken van de bronnen van het Microsoft Security Response Center (MSRC). De MSRC identificeert, controleert, reageert op en lost 24 uur per dag beveiligingsincidenten en cloudkwetsbaarheden op.

## <a name="vulnerability-scanning"></a>Kwetsbaarheid scannen
Het scannen van kwetsbaarheden wordt uitgevoerd op besturingssystemen, databases en netwerkapparaten van servers. De kwetsbaarheid scans worden uitgevoerd op een kwartaal basis op zijn minst. Azure-contracten met onafhankelijke beoordelaars om penetratietests van de Azure-grens uit te voeren. Red-team oefeningen worden ook routinematig uitgevoerd en de resultaten worden gebruikt om verbeteringen in de beveiliging te maken.

## <a name="protective-monitoring"></a>Beschermende controle
Azure-beveiliging heeft vereisten gedefinieerd voor actieve bewaking. Serviceteams configureren actieve monitoringtools in overeenstemming met deze vereisten. Actieve monitoringtools omvatten de Microsoft Monitoring Agent (MMA) en System Center Operations Manager. Deze hulpprogramma's zijn geconfigureerd om tijdwaarschuwingen te geven aan Azure-beveiligingspersoneel in situaties die onmiddellijke actie vereisen.

## <a name="incident-management"></a>Incidentbeheer
Microsoft implementeert een beveiligingsincidentbeheerproces om een gecoördineerde reactie op incidenten te vergemakkelijken, mocht er een optreden.

Als Microsoft zich bewust wordt van ongeoorloofde toegang tot klantgegevens die zijn opgeslagen op haar apparatuur of in haar faciliteiten, of als microsoft zich bewust wordt van ongeoorloofde toegang tot dergelijke apparatuur of faciliteiten die leiden tot verlies, openbaarmaking of wijziging van klantgegevens, Microsoft voert de volgende acties uit:

- Stelt de klant onmiddellijk op de hoogte van het beveiligingsincident.
- Onderzoekt snel het beveiligingsincident en geeft klanten gedetailleerde informatie over het beveiligingsincident.
- Neemt redelijke en snelle stappen om de gevolgen te beperken en eventuele schade als gevolg van het beveiligingsincident te minimaliseren.

Er is een incidentmanagementframework opgesteld dat rollen definieert en verantwoordelijkheden toewijst. Het Azure Security Incident Management Team is verantwoordelijk voor het beheren van beveiligingsincidenten, waaronder escalatie, en het waarborgen van de betrokkenheid van gespecialiseerde teams wanneer dat nodig is. Azure operations managers zijn verantwoordelijk voor het toezicht op het onderzoek en de oplossing van beveiligings- en privacyincidenten.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
