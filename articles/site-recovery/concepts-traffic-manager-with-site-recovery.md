---
title: Azure Traffic Manager met Azure Site Recovery | Microsoft Documenten
description: Beschrijft hoe u Azure Traffic Manager gebruiken met Azure Site Recovery voor herstel en migratie na noodgevallen
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947745"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager met Azure Site Recovery

Azure Traffic Manager stelt u in staat om de verdeling van het verkeer over de eindpunten van uw toepassing te beheren. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost.

Traffic Manager gebruikt het Domain Name System (DNS) om clientaanvragen naar het meest geschikte eindpunt te leiden, op basis van een verkeersrouteringsmethode en de status van de eindpunten. Traffic Manager biedt een scala aan [routeringsmethoden voor verkeer](../traffic-manager/traffic-manager-routing-methods.md) en [opties voor eindpuntcontrole](../traffic-manager/traffic-manager-monitoring.md) om verschillende toepassingsbehoeften en modellen voor automatische failover mogelijk te kunnen maken. Clients maken rechtstreeks verbinding met het geselecteerde eindpunt. Traffic Manager is geen proxy of gateway en het ziet het verkeer niet passeren tussen de client en de service.

In dit artikel wordt beschreven hoe u de intelligente routering van Azure Traffic Monitor combineren met de krachtige mogelijkheden voor herstel en migratie van Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>On-premises naar Azure failover

Voor het eerste scenario, overweeg **bedrijf A** dat al zijn toepassingsinfrastructuur draait in de on-premises omgeving. Om redenen van bedrijfscontinuïteit en naleving besluit **Bedrijf A** Azure Site Recovery te gebruiken om zijn toepassingen te beschermen.

**Bedrijf A** voert toepassingen uit met openbare eindpunten en wil de mogelijkheid hebben om verkeer naadloos om te leiden naar Azure in een rampgebeurtenis. Met [de methode voor het](../traffic-manager/traffic-manager-configure-priority-routing-method.md) routeren van prioriteit in Azure Traffic Manager kan bedrijf A dit failoverpatroon eenvoudig implementeren.

De setup is als volgt:
- **Bedrijf A** maakt een [Traffic Manager-profiel](../traffic-manager/traffic-manager-create-profile.md).
- Bedrijf **A** maakt gebruik van de **methode Prioriteitsroutering** twee eindpunten : **Primair** voor on-premises en **Failover** voor Azure. **Primair** is toegewezen prioriteit 1 en **failover** is toegewezen prioriteit 2.
- Aangezien het **primaire** eindpunt buiten Azure wordt gehost, wordt het eindpunt gemaakt als een [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) eindpunt.
- Met Azure Site Recovery heeft de Azure-site geen virtuele machines of toepassingen die worden uitgevoerd voordat deze mislukt. Het **Failover-eindpunt** wordt dus ook gemaakt als **extern** eindpunt.
- Standaard wordt gebruikersverkeer naar de on-premises toepassing geleid omdat dat eindpunt de hoogste prioriteit heeft. Er wordt geen verkeer naar Azure geleid als het **primaire** eindpunt in orde is.

![On-premises-to-Azure voor failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

In een rampgebeurtenis kan Bedrijf A een [failover](site-recovery-failover.md) naar Azure activeren en de toepassingen ervan herstellen op Azure. Wanneer Azure Traffic Manager detecteert dat het **primaire** eindpunt niet meer in orde is, wordt het **failover-eindpunt** automatisch gebruikt in de DNS-respons en maken gebruikers verbinding met de toepassing die is hersteld op Azure.

![On-premises-to-Azure na failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Afhankelijk van de zakelijke vereisten kan **bedrijf A** een hogere of lagere [indringende frequentie](../traffic-manager/traffic-manager-monitoring.md) kiezen om te schakelen tussen on-premises naar Azure in een rampgebeurtenis en minimale downtime voor gebruikers te garanderen.

Wanneer de ramp is opgenomen, kan **bedrijf A** failback van Azure naar de on-premises omgeving[(VMware](vmware-azure-failback.md) of [Hyper-V)](hyper-v-azure-failback.md)met azure site recovery. Wanneer Traffic Manager detecteert dat het **primaire** eindpunt weer in orde is, wordt het **primaire** eindpunt automatisch gebruikt in de DNS-antwoorden.

## <a name="on-premises-to-azure-migration"></a>On-premises naar Azure-migratie

Naast disaster recovery maakt Azure Site Recovery ook [migraties naar Azure](migrate-overview.md)mogelijk. Met behulp van de krachtige failovermogelijkheden van Azure Site Recovery kunnen klanten de prestaties van toepassingen op Azure beoordelen zonder dat dit gevolgen heeft voor hun on-premises omgeving. En wanneer klanten klaar zijn om te migreren, kunnen ze ervoor kiezen om hele workloads samen te migreren of ervoor te kiezen om geleidelijk te migreren en te schalen.

[De gewogen](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routeringsmethode van Azure Traffic Manager kan worden gebruikt om een deel van binnenkomend verkeer naar Azure te leiden terwijl de meerderheid wordt doorgedirigeerd naar de on-premises omgeving. Met deze aanpak u de schaalprestaties beoordelen, aangezien u het gewicht dat aan Azure is toegewezen, blijven verhogen naarmate u meer en meer van uw workloads migreert naar Azure.

**Bedrijf B** kiest er bijvoorbeeld voor om gefaseerd te migreren en een deel van de toepassingsomgeving te verplaatsen met behoud van de rest on-premises. In de beginfase waarin het grootste deel van de omgeving on-premises is, wordt een groter gewicht toegewezen aan de on-premises omgeving. Verkeersmanager retourneert een eindpunt op basis van gewichten die zijn toegewezen aan beschikbare eindpunten.

![On-premises-naar-Azure-migratie](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Tijdens de migratie zijn beide eindpunten actief en wordt het grootste deel van het verkeer naar de on-premises omgeving geleid. Naarmate de migratie vordert, kan een groter gewicht worden toegewezen aan het eindpunt op Azure en ten slotte kan het on-premises eindpunt na migratie worden gedeactiveerd.

## <a name="azure-to-azure-failover"></a>Failover van Azure naar Azure

Overweeg in dit voorbeeld **bedrijf C** met al zijn toepassingsinfrastructuur met Azure. Om redenen van bedrijfscontinuïteit en naleving besluit **bedrijf C** Azure Site Recovery te gebruiken om zijn toepassingen te beschermen.

**Bedrijf C** voert toepassingen uit met openbare eindpunten en wil de mogelijkheid hebben om verkeer naadloos om te leiden naar een andere Azure-regio in een rampgebeurtenis. Met [de methode Priority](../traffic-manager/traffic-manager-configure-priority-routing-method.md) traffic-routing kan bedrijf **C** dit failoverpatroon eenvoudig implementeren.

De setup is als volgt:
- **Bedrijf C** maakt een [Traffic Manager-profiel](../traffic-manager/traffic-manager-create-profile.md).
- Met behulp van de **methode Prioriteitsroutering** maakt bedrijf **C** twee eindpunten : **Primair** voor de bronregio (Azure East Asia) en **Failover** voor het herstelgebied (Azure Zuidoost-Azië). **Primair** is toegewezen prioriteit 1 en **failover** is toegewezen prioriteit 2.
- Aangezien het **primaire** eindpunt wordt gehost in Azure, kan het eindpunt als [Azure-eindpunt](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) zijn.
- Met Azure Site Recovery heeft de Azure-site voor herstel geen virtuele machines of toepassingen die worden uitgevoerd voordat deze mislukt. Het **Failover-eindpunt** kan dus worden gemaakt als [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) eindpunt.
- Standaard wordt gebruikersverkeer naar de bronregio (Oost-Azië) geleid, omdat aan dat eindpunt de hoogste prioriteit is gekoppeld. Er wordt geen verkeer naar het herstelgebied geleid als het **primaire** eindpunt in orde is.

![Azure-naar-Azure voor failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

In een rampgebeurtenis kan **bedrijf C** een [failover](azure-to-azure-tutorial-failover-failback.md) activeren en zijn toepassingen herstellen in het Azure-herstelgebied. Wanneer Azure Traffic Manager detecteert dat het primaire eindpunt niet meer in orde is, wordt het **failover-eindpunt** automatisch gebruikt in de DNS-respons en maken gebruikers verbinding met de toepassing die is hersteld in de Azure-regio voor herstel (Zuidoost-Azië).

![Azure-naar-Azure na failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Afhankelijk van de zakelijke vereisten kan **bedrijf C** een hogere of lagere [indringende frequentie](../traffic-manager/traffic-manager-monitoring.md) kiezen om te schakelen tussen bron- en herstelregio's en minimale downtime voor gebruikers te garanderen.

Wanneer de ramp is opgenomen, kan **bedrijf C** mislukken van het herstelazure-gebied naar de bronAzure-regio met Azure Site Recovery. Wanneer Traffic Manager detecteert dat het **primaire** eindpunt weer in orde is, wordt het **primaire** eindpunt automatisch gebruikt in de DNS-antwoorden.

## <a name="protecting-multi-region-enterprise-applications"></a>Bedrijfstoepassingen met meerdere regio's beschermen

Wereldwijde ondernemingen verbeteren vaak de klantervaring door hun toepassingen aan te passen aan regionale behoeften. Lokalisatie en latentiereductie kunnen leiden tot een splitsing van de toepassingsinfrastructuur tussen regio's. Ondernemingen zijn ook gebonden aan regionale gegevenswetgeving in bepaalde gebieden en kiezen ervoor om een deel van hun toepassingsinfrastructuur binnen regionale grenzen te isoleren.  

Laten we eens kijken naar een voorbeeld waar **Bedrijf D** zijn toepassingseindpunten heeft gesplitst om Duitsland en de rest van de wereld afzonderlijk te bedienen. **Bedrijf D** maakt hiervoor gebruik van de [geografische](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routeringsmethode van Azure Traffic Manager. Verkeer van oorsprong uit Duitsland wordt naar **Eindpunt 1** geleid en het verkeer van buiten Duitsland wordt naar Eindpunt **2 geleid.**

Het probleem met deze setup is dat als **Endpoint 1** om welke reden dan ook niet meer werkt, er geen omleiding is van het verkeer naar **Eindpunt 2.** Verkeer uit Duitsland wordt nog steeds naar **Endpoint 1** geleid, ongeacht de gezondheid van het eindpunt, waardoor Duitse gebruikers geen toegang hebben tot de toepassing van **bedrijf D.** Als **Endpoint 2** offline gaat, is er ook geen omleiding van het verkeer naar **Eindpunt 1.**

![Toepassing met meerdere regio's vóór](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Om te voorkomen dat dit probleem wordt opgelost en de tolerantie van toepassingen wordt gewaarborgd, gebruikt **bedrijf D** [geneste Traffic Manager-profielen](../traffic-manager/traffic-manager-nested-profiles.md) met Azure Site Recovery. In een geneste profielinstelling wordt het verkeer niet naar afzonderlijke eindpunten geleid, maar naar andere Traffic Manager-profielen. Zo werkt deze setup:
- In plaats van gebruik te maken van geografische routering met afzonderlijke eindpunten, gebruikt **Bedrijf D** geografische routering met Traffic Manager-profielen.
- Elk child Traffic Manager-profiel maakt gebruik van **Priority** routing met een primair en een hersteleindpunt, vandaar het nesten van **Priority** routing binnen **Geografische** routering.
- Om toepassingstolerantie mogelijk te maken, maakt elke werkbelastingsdistributie gebruik van Azure Site Recovery om te mislukken naar een herstelgebied dat is gebaseerd in het geval van een rampgebeurtenis.
- Wanneer de bovenliggende Traffic Manager een DNS-query ontvangt, wordt deze doorgestuurd naar de relevante onderliggende verkeersbeheermanager die reageert op de query met een beschikbaar eindpunt.

![Toepassing met meerdere regio's na](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Als het eindpunt in Duitsland Centraal bijvoorbeeld mislukt, kan de aanvraag snel worden hersteld naar Duitsland Noordoost. Het nieuwe eindpunt verwerkt verkeer afkomstig uit Duitsland met minimale downtime voor gebruikers. Op dezelfde manier kan een eindpuntstoring in West-Europa worden verwerkt door de toepassingswerkbelasting naar Noord-Europa te herstellen, waarbij Azure Traffic Manager DNS-omleidingen verwerkt naar het beschikbare eindpunt.

De bovenstaande instelling kan worden uitgebreid met zoveel regio- en eindpuntcombinaties die nodig zijn. Traffic Manager staat maximaal 10 niveaus van geneste profielen toe en staat geen lussen toe binnen de geneste configuratie.

## <a name="recovery-time-objective-rto-considerations"></a>Overwegingen voor hersteltijddoelstelling (RTO)

In de meeste organisaties wordt het toevoegen of wijzigen van DNS-records verwerkt door een apart team of door iemand buiten de organisatie. Dit maakt de taak van het wijzigen van DNS-records zeer uitdagend. De tijd die nodig is om DNS-records bij te werken door andere teams of organisaties die dns-infrastructuur beheren, varieert van organisatie tot organisatie en heeft gevolgen voor de RTO van de toepassing.

Door gebruik te maken van Traffic Manager u het werk dat nodig is voor DNS-updates vooraf laden. Er is geen handmatige of gescripte actie vereist op het moment van daadwerkelijke failover. Deze aanpak helpt bij het snel schakelen (en dus het verlagen van RTO) en het vermijden van kostbare tijdrovende DNS-wijzigingsfouten in een rampgebeurtenis. Met Traffic Manager wordt zelfs de failback-stap geautomatiseerd, die anders afzonderlijk zou moeten worden beheerd.

Het instellen van het juiste [indringende interval](../traffic-manager/traffic-manager-monitoring.md) door middel van eenvoudige of snelle interval statuscontroles kan de RTO tijdens failover aanzienlijk omlaag brengen en downtime voor gebruikers verminderen.

U bovendien de WAARDE DNS Time to Live (TTL) optimaliseren voor het Traffic Manager-profiel. TTL is de waarde waarvoor een DNS-item in de cache wordt opgeslagen door een client. Voor een record zou DNS niet twee keer worden opgevraagd binnen de spanwijdte van TTL. Aan elke DNS-record is een TTL gekoppeld. Het verlagen van deze waarde resulteert in meer DNS-query's voor Traffic Manager, maar kan RTO verminderen door sneller uitval te ontdekken.

De TTL ervaren door de client neemt ook niet toe als het aantal DNS resolvers tussen de client en de gezaghebbende DNS-server toeneemt. DNS resolvers 'aftellen' de TTL en alleen doorgeven van een TTL waarde die de verstreken tijd weerspiegelt sinds de record werd opgeslagen. Dit zorgt ervoor dat de DNS-record wordt vernieuwd bij de client na de TTL, ongeacht het aantal DNS Resolvers in de keten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [routeringsmethoden](../traffic-manager/traffic-manager-routing-methods.md)voor beheerbeheer .
- Meer informatie over [geneste Traffic Manager-profielen](../traffic-manager/traffic-manager-nested-profiles.md).
- Meer informatie over [endpoint-monitoring](../traffic-manager/traffic-manager-monitoring.md).
- Meer informatie over [herstelplannen](site-recovery-create-recovery-plans.md) om failover van toepassingen te automatiseren.
