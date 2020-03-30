---
title: Integriteit van Azure-infrastructuur
description: In dit artikel wordt ingegaan op de integriteit van de Azure-infrastructuur.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727193"
---
# <a name="azure-infrastructure-integrity"></a>Integriteit van Azure-infrastructuur

## <a name="software-installation"></a>Software-installatie
Alle componenten in de softwarestack die in de Azure-omgeving zijn geïnstalleerd, zijn op maat gemaakt volgens het SDL-proces (Microsoft Security Development Lifecycle). Alle softwarecomponenten, inclusief besturingssysteemafbeeldingen en SQL Database, worden geïmplementeerd als onderdeel van het change management- en releasemanagementproces. Het besturingssysteem dat op alle knooppunten wordt uitgevoerd, is een aangepaste versie van Windows Server 2008 of Windows Server 2012. De exacte versie wordt gekozen door de stof controller (FC) op basis van de rol die het van plan is voor het OS te spelen. Bovendien staat het host-besturingssysteem de installatie van ongeautoriseerde softwarecomponenten niet toe.

Sommige Azure-componenten worden geïmplementeerd als Azure-clients op een gast-VM die op een gastbesturingssysteem wordt uitgevoerd.

## <a name="virus-scans-on-builds"></a>Virus scans op builds
Azure-softwarecomponent (inclusief OS) builds moeten een virusscan ondergaan die gebruik maakt van het anti-virushulpprogramma Endpoint Protection. Elke virusscan maakt een logboek in de bijbehorende build directory, waarin wordt beschreven wat er is gescand en de resultaten van de scan. De virusscan maakt deel uit van de buildbroncode voor elk onderdeel binnen Azure. Code wordt niet verplaatst naar productie zonder een schone en succesvolle virusscan. Als er problemen worden opgemerkt, wordt de build bevroren en gaat vervolgens naar de beveiligingsteams binnen Microsoft Security om te bepalen waar de 'rogue'-code de build is binnengegaan.

## <a name="closed-and-locked-environment"></a>Gesloten en vergrendelde omgeving
Azure-infrastructuurknooppunten en gast-VM's hebben standaard geen gebruikersaccounts die erop zijn gemaakt. Daarnaast zijn standaard Windows-beheerdersaccounts ook uitgeschakeld. Beheerders van Azure live-ondersteuning kunnen zich met de juiste verificatie aanmelden bij deze machines en het Azure-productienetwerk beheren voor noodreparaties.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database-verificatie
Zoals bij elke implementatie van SQL Server moet het beheer van gebruikersaccounts streng worden gecontroleerd. Azure SQL Database ondersteunt alleen SQL Server-verificatie. Als aanvulling op het gegevensbeveiligingsmodel van een klant moeten ook gebruikersaccounts met sterke wachtwoorden en geconfigureerd met specifieke rechten worden gebruikt.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACL's en firewalls tussen het Microsoft-bedrijfsnetwerk en een Azure-cluster
Toegangscontrolelijsten (ACL's) en firewalls tussen het serviceplatform en het Microsoft-bedrijfsnetwerk beschermen SQL Database-exemplaren tegen ongeautoriseerde toegang met voorkennis. Verder hebben alleen gebruikers van IP-adresuithet Microsoft-bedrijfsnetwerk toegang tot het eindpunt voor het beheervan het Windows Fabric-platformbeheer.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACL's en firewalls tussen knooppunten in een SQL-databasecluster
Als extra bescherming zijn acl. als onderdeel van de defensie-in-dieptestrategie acl. en een firewall geïmplementeerd tussen knooppunten in een SQL Database-cluster. Alle communicatie binnen het Windows Fabric-platformcluster en alle hardloopcode wordt vertrouwd.

## <a name="custom-monitoring-agents"></a>Aangepaste bewakingsagenten
SQL Database maakt gebruik van aangepaste monitoring agents (MA's), ook wel waakhonden genoemd, om de status van het SQL Database-cluster te controleren.

## <a name="web-protocols"></a>Webprotocollen

### <a name="role-instance-monitoring-and-restart"></a>Rolinstantiebewaking en opnieuw opstarten
Azure zorgt ervoor dat alle geïmplementeerde, lopende rollen (webgericht internet of back-end processing worker rollen) onderworpen zijn aan langdurige statusbewaking om ervoor te zorgen dat ze effectief en efficiënt de services leveren waarvoor ze zijn ingericht. Als een rol niet in orde wordt, door een kritieke fout in de toepassing die wordt gehost of een onderliggend configuratieprobleem binnen de rolinstantie zelf, detecteert de FC het probleem binnen de rolinstantie en initieert een correctiestatus.

### <a name="compute-connectivity"></a>Compute-connectiviteit
Azure zorgt ervoor dat de geïmplementeerde toepassing of service bereikbaar is via standaard webgebaseerde protocollen. Virtuele exemplaren van internetgerichte webrollen hebben een externe internetverbinding en zijn rechtstreeks bereikbaar voor webgebruikers. Om de gevoeligheid en integriteit te beschermen van de bewerkingen die werknemersrollen uitvoeren ten behoeve van de openbaar toegankelijke virtuele exemplaren van de webrol, hebben virtuele exemplaren van back-end processing worker-rollen externe internetverbinding, maar kunnen ze niet worden rechtstreeks toegankelijk voor externe webgebruikers.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
