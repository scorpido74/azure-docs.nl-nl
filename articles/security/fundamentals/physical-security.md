---
title: Fysieke beveiliging van Azure-datacenters - Microsoft Azure | Microsoft Documenten
description: In het artikel wordt beschreven wat Microsoft doet om de Azure-datacenters te beveiligen, inclusief fysieke infrastructuur, beveiliging en nalevingsaanbiedingen.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726730"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-faciliteiten, lokalen en fysieke beveiliging
In dit artikel wordt beschreven wat Microsoft doet om de Azure-infrastructuur te beveiligen.

## <a name="datacenter-infrastructure"></a>Datacenterinfrastructuur
Azure bestaat uit een [wereldwijd gedistribueerde datacenterinfrastructuur,](https://azure.microsoft.com/global-infrastructure/)die duizenden online services ondersteunt en wereldwijd meer dan 100 zeer beveiligde faciliteiten biedt.

De infrastructuur is ontworpen om toepassingen dichter bij gebruikers over de hele wereld te brengen, gegevensresidentie te behouden en uitgebreide nalevings- en tolerantieopties voor klanten aan te bieden. Azure heeft wereldwijd 52 regio's en is beschikbaar in 140 landen/regio's.

Een regio is een set datacenters die met elkaar verbonden is via een enorm en veerkrachtig netwerk. Het netwerk omvat standaard contentdistributie, taakverdeling, redundantie en versleuteling. Met meer globale regio's dan welke andere cloudprovider dan ook, biedt Azure u de flexibiliteit om toepassingen te implementeren waar u ze nodig hebt.

Azure-regio's zijn ingedeeld in regio's. Een Azure-geografie zorgt ervoor dat vereisten voor gegevensresidentie, soevereiniteit, naleving en tolerantie binnen geografische grenzen worden nageleefd.

Klanten met specifieke behoeften ten aanzien van gegevenslocatie en naleving, kunnen met geografische gebieden hun gegevens en toepassingen in de buurt houden. Regio's zijn fouttolerant om volledige regiostoringen te weerstaan, door hun verbinding met de speciale netwerkinfrastructuur met hoge capaciteit.

Beschikbaarheidszones zijn fysiek gescheiden locaties binnen een Azure-gebied. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Met beschikbaarheidszones u bedrijfskritieke toepassingen uitvoeren met een hoge beschikbaarheid en replicatie met lage latentie.

In de volgende afbeelding ziet u hoe de regio- en beschikbaarheidszones voor Azure-globale infrastructuur binnen dezelfde grens voor gegevensresidenties worden gekoppeld voor hoge beschikbaarheid, herstel na noodgevallen en back-up.

![Diagram met grens gegevensresidentie](./media/physical-security/data-residency-boundary.png)

Geografisch gedistribueerde datacenters stelt Microsoft in staat om dicht bij klanten te staan, de netwerklatentie te verminderen en georedundante back-up en failover mogelijk te maken.

## <a name="physical-security"></a>Fysieke beveiliging
Microsoft ontwerpt, bouwt en exploiteert datacenters op een manier die de fysieke toegang tot de gebieden waar uw gegevens worden opgeslagen, strikt controleert. Microsoft begrijpt het belang van het beschermen van uw gegevens en streeft ernaar de datacenters die uw gegevens bevatten te beveiligen. We hebben een hele divisie bij Microsoft die zich toelegt op het ontwerpen, bouwen en exploiteren van de fysieke faciliteiten die Azure ondersteunen. Dit team investeert in het handhaven van state-of-the-art fysieke veiligheid.

Microsoft hanteert een gelaagde benadering van fysieke beveiliging, om het risico te verkleinen dat onbevoegde gebruikers fysieke toegang krijgen tot gegevens en de datacenterbronnen. Datacenters die door Microsoft worden beheerd, hebben uitgebreide beschermingslagen: toegangsgoedkeuring bij de omtrek van de faciliteit, bij de omtrek van het gebouw, in het gebouw en op de vloer van het datacenter. Lagen van fysieke beveiliging zijn:

- **Toegangsaanvraag en goedkeuring.** U moet toegang aanvragen voordat u in het datacenter aankomt. U moet een geldige zakelijke rechtvaardiging voor uw bezoek geven, zoals nalevings- of controledoeleinden. Alle aanvragen worden goedgekeurd op basis van need-to-access door Microsoft-medewerkers. Een need-to-access basis helpt het aantal personen dat nodig is om een taak in de datacenters te voltooien tot het absolute minimum te beperken. Nadat Microsoft toestemming heeft verleend, heeft een individu alleen toegang tot het discrete gedeelte van het vereiste datacenter, op basis van de goedgekeurde zakelijke rechtvaardiging. Machtigingen zijn beperkt tot een bepaalde periode en verlopen vervolgens.

- **De omgeving van de faciliteit.** Wanneer u in een datacenter aankomt, moet u via een goed gedefinieerd toegangspunt gaan. Typisch, hoge hekken gemaakt van staal en beton omvatten elke centimeter van de omtrek. Er zijn camera's rond de datacenters, met een security team toezicht op hun video's te allen tijde.

- **De ingang van het gebouw.** De ingang van het datacenter is bemand met professionele beveiligingsmedewerkers die een strenge opleiding en antecedentenonderzoek hebben ondergaan. Deze security officers ook routinematig patrouilleren het datacenter, en toezicht op de video's van camera's in het datacenter te allen tijde.

- **In het gebouw.** Nadat u het gebouw hebt ingevoerd, moet u tweestapsverificatie met biometrie doorstaan om door het datacenter te kunnen blijven bewegen. Als uw identiteit is gevalideerd, u alleen het gedeelte van het datacenter invoeren waartoe u toegang hebt goedgekeurd. U er alleen verblijven voor de duur van de toegestane tijd.

- **Datacenter vloer.** Je mag alleen op de vloer komen die je hebt goedgekeurd. U bent verplicht om een full body metaal detectie screening passeren. Om het risico te verkleinen dat ongeautoriseerde gegevens zonder onze medeweten het datacenter binnenkomen of verlaten, kunnen alleen goedgekeurde apparaten hun weg vinden naar de datacentervloer. Daarnaast houden videocamera's de voor- en achterkant van elk serverrack in de gaten. Wanneer u de datacentervloer verlaat, moet u opnieuw door de full body metaaldetectiescreening gaan. Om het datacenter te verlaten, moet u een extra beveiligingsscan doorstaan.

Microsoft verplicht bezoekers om badges in te leveren bij vertrek uit een Microsoft-faciliteit.

## <a name="physical-security-reviews"></a>Beoordelingen van fysieke beveiliging
Periodiek voeren we fysieke beveiligingsbeoordelingen uit van de faciliteiten om ervoor te zorgen dat de datacenters de beveiligingsvereisten van Azure goed aanpakken. Het personeel van de datacenterhostingprovider biedt geen Azure-servicebeheer. Personeel kan zich niet aanmelden bij Azure-systemen en heeft geen fysieke toegang tot de Azure-collocatieruimte en -kooien.

## <a name="data-bearing-devices"></a>Gegevensdragende apparaten
Microsoft maakt gebruik van procedures voor aanbevolen procedures en een veegoplossing die [nist 800-88-compatibel](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01)is. Voor harde schijven die niet kunnen worden gewist, gebruiken we een vernietigingsproces dat het vernietigt en het herstel van informatie onmogelijk maakt. Dit vernietigingsproces kan zijn om te desintegreren, versnipperen, verpulveren of verbranden. Wij bepalen de wijze van vervreemding op basis van het activatype. We bewaren gegevens van de vernietiging.  

## <a name="equipment-disposal"></a>Verwijdering van apparatuur
Na het einde van de levensduur van een systeem volgen operationele medewerkers van Microsoft strenge procedures voor gegevensverwerking en hardwareverwijdering om ervoor te zorgen dat hardware die uw gegevens bevat, niet beschikbaar wordt gesteld aan niet-vertrouwde partijen. We gebruiken een veilige wissen aanpak voor harde schijven die het ondersteunen. Voor harde schijven die niet kunnen worden gewist, gebruiken we een vernietigingsproces dat de schijf vernietigt en maakt het herstel van informatie onmogelijk. Dit vernietigingsproces kan zijn om te desintegreren, versnipperen, verpulveren of verbranden. Wij bepalen de wijze van vervreemding op basis van het activatype. We bewaren gegevens van de vernietiging. Alle Azure-services maken gebruik van goedgekeurde mediaopslag- en verwijderingsbeheerservices.

## <a name="compliance"></a>Naleving
We ontwerpen en beheren de Azure-infrastructuur om te voldoen aan een breed scala aan internationale en branchespecifieke nalevingsnormen, zoals ISO 27001, HIPAA, FedRAMP, SOC 1 en SOC 2. We voldoen ook aan land- of regiospecifieke normen, waaronder Australië IRAP, UK G-Cloud en Singapore MTCS. Strenge audits van derden, zoals die van het British Standards Institute, controleren of de strikte beveiligingscontroles in het mandaat van deze normen worden nageleefd.

Zie de [compliance-aanbiedingen](https://www.microsoft.com/trustcenter/compliance/complianceofferings)voor een volledige lijst met nalevingsstandaarden waaraan Azure voldoet.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)


