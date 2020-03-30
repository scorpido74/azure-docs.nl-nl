---
title: Beheer van Azure-productienetwerk - Microsoft Azure
description: In dit artikel wordt beschreven hoe Microsoft het Azure-productienetwerk beheert en beheert om de Azure-datacenters te beveiligen.
services: security
documentationcenter: n
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
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727117"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Beheer en werking van het Azure-productienetwerk    
In dit artikel wordt beschreven hoe Microsoft het Azure-productienetwerk beheert en beheert om de Azure-datacenters te beveiligen.

## <a name="monitor-log-and-report"></a>Controleren, inloggen en rapporteren

Het beheer en de werking van het Azure-productienetwerk is een gecoördineerde inspanning tussen de bedrijfsteams van Azure en Azure SQL Database. De teams maken gebruik van verschillende systeem- en applicatie-prestatiebewakingstools in de omgeving. En ze gebruiken de juiste tools om netwerkapparaten, servers, services en toepassingsprocessen te controleren.

Om ervoor te zorgen dat services in de Azure-omgeving veilig worden uitgevoerd, implementeren de operations-teams meerdere niveaus van monitoring, logboekregistratie en rapportage, waaronder de volgende acties:

- In de eerste plaats verzamelt de Microsoft Monitoring Agent (MMA) monitoring- en diagnostische logboekinformatie van vele plaatsen, waaronder de fabriccontroller (FC) en het root-besturingssysteem (OS), en schrijft deze naar logboekbestanden. De agent duwt uiteindelijk een verteerd subset van de informatie naar een vooraf geconfigureerd Azure-opslagaccount. Daarnaast leest de vrijstaande monitoring- en diagnostische dienst verschillende monitoring- en diagnostische loggegevens en vat de informatie samen. De monitoring- en diagnostische dienst schrijft de informatie naar een geïntegreerd logboek. Azure maakt gebruik van de op maat gemaakte Azure-beveiligingsbewaking, een uitbreiding van het Azure-bewakingssysteem. Het heeft componenten die observeren, analyseren en rapporteren over beveiligingsrelevante gebeurtenissen van verschillende punten in het platform.

- Het Azure SQL Database Windows Fabric-platform biedt beheer-, implementatie-, ontwikkel- en operationele toezichtsservices voor Azure SQL Database. Het platform biedt gedistribueerde implementatieservices in meerdere stappen, statusbewaking, automatische reparaties en naleving van de serviceversie. Het biedt de volgende diensten:

   - Mogelijkheden voor servicemodellering met een high-fidelity ontwikkelomgeving (datacenterclusters zijn duur en schaars).
   - Implementatie- en upgradeworkflows met één klik voor servicebootstrap en -onderhoud.
   - Gezondheidsrapportage met geautomatiseerde reparatieworkflows om zelfgenezing mogelijk te maken.
   - Realtime bewaking, waarschuwingen en foutopsporingsfaciliteiten op de knooppunten van een gedistribueerd systeem.
   - Gecentraliseerde verzameling van operationele gegevens en statistieken voor gedistribueerde oorzaakanalyse en service-inzicht.
   - Operationele tooling voor implementatie, wijzigingsbeheer en monitoring.
   - Het Azure SQL Database Windows Fabric-platform en de watchdog-scripts worden continu uitgevoerd en worden in realtime gecontroleerd.

Als er afwijkingen optreden, wordt het incidentresponsproces dat wordt gevolgd door het Azure-incidenttriageteam geactiveerd. Het juiste Azure-ondersteuningspersoneel wordt op de hoogte gesteld om op het incident te reageren. Het bijhouden en oplossen van problemen worden gedocumenteerd en beheerd in een gecentraliseerd ticketsysteem. Systeemuptime metrics zijn beschikbaar onder de non-disclosure agreement (NDA) en op verzoek.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Bedrijfsnetwerk en multi-factor toegang tot productie
De gebruikersgroep van het bedrijfsnetwerk omvat Azure-ondersteuningspersoneel. Het bedrijfsnetwerk ondersteunt interne bedrijfsfuncties en omvat toegang tot interne toepassingen die worden gebruikt voor Azure-klantenondersteuning. Het bedrijfsnetwerk is zowel logisch als fysiek gescheiden van het Azure-productienetwerk. Azure-personeel heeft toegang tot het bedrijfsnetwerk met Azure-werkstations en laptops. Alle gebruikers moeten een Azure Active Directory-account (Azure AD) hebben, inclusief gebruikersnaam en wachtwoord, om toegang te krijgen tot bedrijfsnetwerkbronnen. Bedrijfsnetwerktoegang maakt gebruik van Azure AD-accounts, die worden uitgegeven aan alle Microsoft-personeel, contractanten en leveranciers en worden beheerd door Microsoft Information Technology. Unieke gebruikers-id's onderscheiden personeel op basis van hun arbeidsstatus bij Microsoft.

Toegang tot interne Azure-toepassingen wordt beheerd via verificatie met Active Directory Federation Services (AD FS). AD FS is een service die wordt gehost door Microsoft Information Technology en die verificatie van zakelijke netwerkgebruikers biedt door het toepassen van een beveiligde token en gebruikersclaims. AD FS maakt met interne Azure-toepassingen gebruikers te verifiëren tegen het Active Directory-domein van Microsoft. Om toegang te krijgen tot het productienetwerk vanuit de bedrijfsnetwerkomgeving, moeten gebruikers zich verifiëren met behulp van meervoudige verificatie.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
