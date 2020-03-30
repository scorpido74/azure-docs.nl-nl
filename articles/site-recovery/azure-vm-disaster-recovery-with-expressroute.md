---
title: Azure ExpressRoute Azure VM-herstel na te herstellen integreren met Azure Site Recovery
description: Beschrijft hoe u noodherstel voor Azure VM's instellen met Azure Site Recovery en Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954089"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>ExpressRoute integreren met noodherstel voor Azure VM's


In dit artikel wordt beschreven hoe u Azure ExpressRoute integreren met [Azure Site Recovery](site-recovery-overview.md), wanneer u disaster recovery voor Azure VM's instelt in een secundairAzure-gebied.

Siteherstel maakt disaster recovery van Azure VM's mogelijk door Azure VM-gegevens te repliceren naar Azure.

- Als Azure VM's [azure beheerde schijven](../virtual-machines/windows/managed-disks-overview.md)gebruiken, worden VM-gegevens gerepliceerd naar een gerepliceerde beheerde schijf in de secundaire regio.
- Als Azure VM's geen beheerde schijven gebruiken, worden VM-gegevens gerepliceerd naar een Azure-opslagaccount.
- Replicatieeindpunten zijn openbaar, maar replicatieverkeer voor Azure VM's gaat niet door internet.

Met ExpressRoute u on-premises netwerken uitbreiden naar de Microsoft Azure-cloud via een privéverbinding, mogelijk gemaakt door een connectiviteitsprovider. Als u ExpressRoute hebt geconfigureerd, integreert deze als volgt met Site Recovery:

- **Tijdens replicatie tussen Azure-regio's**: Replicatieverkeer voor Azure VM-noodherstel valt alleen binnen Azure en ExpressRoute is niet nodig of wordt niet gebruikt voor replicatie. Als u echter verbinding maakt van een on-premises site met de Azure VM's in de primaire Azure-site, zijn er een aantal problemen waarmee u op de hoogte moet zijn wanneer u noodherstel instelt voor deze Azure VM's.
- **Failover tussen Azure-regio's**: Wanneer er storingen optreden, mislukt u boven Azure VM's van de primaire naar secundaire Azure-regio. Nadat u niet naar een secundaire regio is overgemaakt, zijn er een aantal stappen die moeten worden ondernomen om toegang te krijgen tot de Azure VM's in de secundaire regio via ExpressRoute.


## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u begint de volgende concepten:

- [ExpressRoute-circuits](../expressroute/expressroute-circuit-peerings.md)
- [Routeringsdomeinen expressroute](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [locaties](../expressroute/expressroute-locations.md).
- Azure [VM-replicatiearchitectuur](azure-to-azure-architecture.md)
- Replicatie [instellen](azure-to-azure-tutorial-enable-replication.md) voor Azure VM's.
- Hoe te [mislukken over](azure-to-azure-tutorial-failover-failback.md) Azure VM's.


## <a name="general-recommendations"></a>Algemene aanbevelingen

Voor best practices en om te zorgen voor efficiënte Hersteltijddoelstellingen (RTO's) voor herstel na noodgevallen, raden we u aan het volgende te doen wanneer u Site Recovery instelt om te integreren met ExpressRoute:

- Netwerkcomponenten inrichten voordat deze mislukt zijn in een secundaire regio:
    - Wanneer u replicatie inschakelt voor Azure VM's, kan Site Recovery automatisch netwerkbronnen implementeren, zoals netwerken, subnetten en gateways in het doelazure-gebied, op basis van bronnetwerkinstellingen.
    - Site recovery kan niet automatisch netwerkbronnen instellen, zoals VNet-gateways.
    - We raden u aan deze extra netwerkbronnen in te richten voordat u een fail-over maakt. Er is een kleine downtime gekoppeld aan deze implementatie en dit kan van invloed zijn op de algehele hersteltijd als u er geen account van hebt gemaakt tijdens de implementatieplanning.
- Voer regelmatig noodhersteloefeningen uit:
    - Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. Het helpt last-minute configuratieproblemen te voorkomen die een negatieve invloed kunnen hebben op RTO.
    - Wanneer u een testfailover uitvoert voor de drill, raden we u aan een apart Azure VM-netwerk te gebruiken in plaats van het standaardnetwerk dat is ingesteld wanneer u replicatie inschakelt.
- Gebruik verschillende IP-adresruimten als u één ExpressRoute-circuit hebt.
    - We raden u aan een andere IP-adresruimte te gebruiken voor het virtuele doelnetwerk. Dit voorkomt problemen bij het leggen van verbindingen tijdens regionale uitval.
    - Als u geen aparte adresruimte gebruiken, moet u de mislukte testvoor noodhersteluitvoeren op een apart testnetwerk met verschillende IP-adressen uitvoeren. U twee VNets met overlappende IP-adresruimte niet verbinden met hetzelfde ExpressRoute-circuit.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Azure VM's repliceren bij gebruik van ExpressRoute


Als u replicatie voor Azure VM's op een primaire site wilt instellen en u verbinding maakt met deze VM's vanaf uw on-premises site via ExpressRoute, moet u het volgende doen:

1. [Replicatie inschakelen](azure-to-azure-tutorial-enable-replication.md) voor elke Azure VM.
2. Laat optioneel Site Recovery netwerken instellen:
    - Wanneer u replicatie configureert en inschakelt, stelt Site Recovery netwerken, subnetten en gateway-subnetten in het doelazure-gebied in, zodat deze in het brongebied overeenkomen. Site Recovery brengt ook kaarten in kaart tussen de bron en doelvirtuele netwerken.
    - Als u niet wilt dat Siteherstel dit automatisch doet, maakt u de netwerkbronnen aan de doelzijde voordat u replicatie inschakelt.
3. Andere netwerkelementen maken:
    - Siteherstel maakt geen routetabellen, VNet-gateways, VNet-gatewayverbindingen, VNet-peering of andere netwerkbronnen en verbindingen in het secundaire gebied.
    - U moet deze extra netwerkelementen in het secundaire gebied maken, op elk moment voordat u een failover uitvoert vanuit de primaire regio.
    - U [herstelplannen](site-recovery-create-recovery-plans.md) en automatiseringsscripts gebruiken om deze netwerkbronnen in te stellen en met elkaar te verbinden.
1. Als u een netwerkvirtueel toestel (NVA) hebt geïmplementeerd om de stroom van netwerkverkeer te beheren, moet u er rekening mee houden dat:
    - De standaardsysteemroute van Azure voor Azure VM-replicatie is 0.0.0.0/0.
    - NvA-implementaties definiëren doorgaans ook een standaardroute (0.0.0.0/0) die uitgaand internetverkeer dwingt om door de NVA te stromen. De standaardroute wordt gebruikt wanneer er geen andere specifieke routeconfiguratie kan worden gevonden.
    - Als dit het geval is, kan de NVA overbelast raken als al het replicatieverkeer door de NVA gaat.
    - Dezelfde beperking geldt ook bij het gebruik van standaardroutes voor het routeren van al het Azure VM-verkeer naar on-premises implementaties.
    - In dit scenario raden we u aan een eindpunt van [de netwerkservice](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in uw virtuele netwerk te maken voor de Microsoft.Storage-service, zodat het replicatieverkeer azure-grens niet verlaat.

## <a name="replication-example"></a>Voorbeeld van replicatie

Doorgaans hebben bedrijfsimplementaties workloads verdeeld over meerdere Azure VNets, met een centrale verbindingshub voor externe connectiviteit met het internet en on-premises sites. Een hub en spaak topologie wordt meestal gebruikt in combinatie met ExpressRoute.

![On-premises-to-Azure met ExpressRoute voor failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Regio**. Apps worden geïmplementeerd in de regio Azure Oost-Azië.
- **Spaak vNets**. Apps worden geïmplementeerd in twee spaakvNets:
    - **Bron vNet1**: 10.1.0.0/24.
    - **Bron vNet2**: 10.2.0.0/24.
    - Elk gesproken virtueel netwerk is aangesloten op **Hub vNet.**
- **Hub vNet**. Er is een hub vNet **Source Hub vNet**: 10.10.10.0/24.
  - Deze hub vNet fungeert als poortwachter.
  - Alle communicatie via subnetten gaat via deze hub.
    - **Hub vNet-subnetten**. De hub vNet heeft twee subnetten:
    - **NVA-subnet**: 10.10.10.0/25. Dit subnet bevat een NVA (10.10.10.10).
    - **Gateway subnet**: 10.10.10.128/25. Dit subnet bevat een ExpressRoute-gateway die is verbonden met een ExpressRoute-verbinding die via een privé-peering-routeringsdomein naar de on-premises site leidt.
- Het on-premises datacenter heeft een ExpressRoute-circuitverbinding via een partnerrand in Hong Kong.
- Alle routering wordt beheerd via Azure-routetabellen (UDR).
- Al het uitgaande verkeer tussen vNets of naar het on-premises datacenter wordt omgeleid via de NVA.

### <a name="hub-and-spoke-peering-settings"></a>Hub- en spaakpeerinstellingen

#### <a name="spoke-to-hub"></a>Spoke naar hub

**Richting** | **Instelling** | **Staat**
--- | --- | ---
Spoke naar hub | Virtueel netwerkadres toestaan | Ingeschakeld
Spoke naar hub | Doorgestuurd verkeer toestaan | Ingeschakeld
Spoke naar hub | Gateway-doorvoer toestaan | Uitgeschakeld
Spoke naar hub | Verwijdergateways gebruiken | Ingeschakeld

 ![Spoke to hub peering configuration Spoke to hub peering configuration Spoke to hub peering configuration Spoke to](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub naar spoke

**Richting** | **Instelling** | **Staat**
--- | --- | ---
Hub naar spoke | Virtueel netwerkadres toestaan | Ingeschakeld
Hub naar spoke | Doorgestuurd verkeer toestaan | Ingeschakeld
Hub naar spoke | Gateway-doorvoer toestaan | Ingeschakeld
Hub naar spoke | Verwijdergateways gebruiken | Uitgeschakeld

 ![Hub naar spaak peering configuratie](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Voorbeeldstappen

In ons voorbeeld moet het volgende gebeuren wanneer replicatie voor Azure VM's in het bronnetwerk wordt ingesloten:

1. U [schakelt replicatie](azure-to-azure-tutorial-enable-replication.md) in voor een virtuele machine.
2. Site herstel maakt replica vNets, subnetten en gateway subnetten in het doelgebied.
3. Siteherstel maakt toewijzingen tussen de bronnetwerken en de replicadoelnetwerken die worden gemaakt.
4. U maakt handmatig virtuele netwerkgateways, virtuele netwerkgatewayverbindingen, virtuele netwerkpeering of andere netwerkbronnen of -verbindingen.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Fail over Azure VM's bij het gebruik van ExpressRoute

Nadat azure VM's zijn mislukt naar de doelregio Azure met siteherstel, u deze openen met behulp van [Privé-peering van](../expressroute/expressroute-circuit-peerings.md#privatepeering)ExpressRoute.

- Je moet ExpressRoute verbinden met het doel vNet met een nieuwe verbinding. De bestaande ExpressRoute-verbinding wordt niet automatisch overgedragen.
- De manier waarop u uw ExpressRoute-verbinding met het doel vNet instelt, is afhankelijk van uw ExpressRoute-topologie.


### <a name="access-with-two-circuits"></a>Toegang met twee circuits

#### <a name="two-circuits-with-two-peering-locations"></a>Twee circuits met twee peering locaties

Deze configuratie beschermt ExpressRoute-circuits tegen regionale rampen. Als uw primaire peeringlocatie uitvalt, kunnen verbindingen vanaf de andere locatie worden voortgezet.

- Het circuit verbonden met de productie-omgeving is meestal de primaire. Het secundaire circuit heeft doorgaans een lagere bandbreedte, die kan worden verhoogd als zich een ramp voordoet.
- Na failover u verbindingen leggen van het secundaire ExpressRoute-circuit naar het doelvNet. U ook verbindingen hebben ingesteld en klaar in geval van een ramp, om de algehele hersteltijd te verkorten.
- Met gelijktijdige verbindingen met zowel primaire als doelvNets, moet u ervoor zorgen dat uw on-premises routering alleen gebruik maakt van het secundaire circuit en de verbinding na failover.
- De bron- en doelvNets kunnen nieuwe IP-adressen ontvangen of dezelfde na failover behouden. In beide gevallen kunnen de secundaire verbindingen worden tot stand gebracht voordat de failover wordt uitgevoerd.


#### <a name="two-circuits-with-single-peering-location"></a>Twee circuits met enkele peeringlocatie

Deze configuratie helpt te beschermen tegen het uitvallen van het primaire ExpressRoute-circuit, maar niet als de enkele ExpressRoute-peeringlocatie naar beneden gaat, wat beide circuits beïnvloedt.

- U gelijktijdige verbindingen hebben van het on-premises datacenter naar de bron vNEt met het primaire circuit en naar het doel vNet met het secundaire circuit.
- Met gelijktijdige verbindingen met primaire en doel, zorg ervoor dat on-premises routing alleen gebruik maakt van de secundaire circuit en verbinding na failover.
-   U beide circuits niet op dezelfde vNet aansluiten wanneer circuits op dezelfde peeringlocatie worden gemaakt.

### <a name="access-with-a-single-circuit"></a>Toegang met één circuit

In deze configuratie is er slechts één Expressroute circuit. Hoewel het circuit een redundante verbinding heeft in het geval dat er een uitvalt, biedt een enkel routecircuit geen veerkracht als uw peering-regio uitvalt. Opmerking:

- U Azure VM's repliceren naar een Azure-regio op [dezelfde geografische locatie.](azure-to-azure-support-matrix.md#region-support) Als het doelazure-gebied zich niet op dezelfde locatie als de bron bevindt, moet u ExpressRoute Premium inschakelen als u één ExpressRoute-circuit gebruikt. Meer informatie over [ExpressRoute-locaties](../expressroute/expressroute-locations.md) en [ExpressRoute-prijzen](https://azure.microsoft.com/pricing/details/expressroute/).
- U geen bron- en doelvNets tegelijkertijd met het circuit verbinden als dezelfde IP-adresruimte wordt gebruikt op het doelgebied. In dit scenario geldt het volgende:    
    -  Koppel de verbinding aan de bronzijde los en stel vervolgens de doelzijdeverbinding vast. Deze verbindingswijziging kan worden gescript als onderdeel van een herstelplan voor siteherstel. Opmerking:
        - Als een regionale fout niet toegankelijk is, kan de ontkoppelingsbewerking mislukken als de primaire regio niet toegankelijk is. Dit kan gevolgen hebben voor het maken van verbindingen met de doelregio.
        - Als u de verbinding in het doelgebied hebt gemaakt en het primaire gebied later herstelt, u packet drops ervaren als twee gelijktijdige verbindingen proberen verbinding te maken met dezelfde adresruimte.
        - Om dit te voorkomen, beëindigt u de primaire verbinding onmiddellijk.
        - Nadat VM failback naar het primaire gebied is, kan de primaire verbinding opnieuw worden tot stand gebracht, nadat u de secundaire verbinding hebt losgekoppeld.
-   Als een ander adres spaties wordt gebruikt op het doel vNet, u tegelijkertijd verbinding maken met de bron en doel vNets van dezelfde ExpressRoute circuit.


## <a name="failover-example"></a>Voorbeeld van failover

In ons voorbeeld gebruiken we de volgende topologie:

- Twee verschillende ExpressRoute circuits op twee verschillende peering locaties.
- Bewaar privé-IP-adressen voor de Azure VM's na failover.
- De doelherstelregio is Azure SouthEast Asia.
- Een secundaire ExpressRoute circuit verbinding wordt vastgesteld door middel van een partner rand in Singapore.

Voor een eenvoudige topologie die gebruik maakt van één ExpressRoute-circuit, met hetzelfde IP-adres na failover, [bekijkt u dit artikel.](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)

### <a name="example-steps"></a>Voorbeeldstappen
Als u herstel in dit voorbeeld wilt automatiseren, moet u het voorbeeld als volgt doen:

1. Volg de stappen om replicatie in te stellen.
2. [Fail over de Azure VM's,](azure-to-azure-tutorial-failover-failback.md)met deze extra stappen tijdens of na de failover.

    a. Maak de Azure ExpressRoute Gateway in de doelregiohub VNet. Dit is nodig om de doelhub vNet aan te sluiten op het ExpressRoute-circuit.

    b. Maak de verbinding van de doelhub vNet naar het doelExpressRoute-circuit.

    c. Stel de VNet-peerings in tussen de hub van de doelregio en sprak virtuele netwerken. De peering-eigenschappen op het doelgebied zijn dezelfde als die in het brongebied.

    d. Stel de UDR's in de hub VNet, en de twee sprak VNets.

    - De eigenschappen van de doelzijde UDR's zijn dezelfde als die aan de bronzijde bij het gebruik van dezelfde IP-adressen.
    - Bij verschillende doel-IP-adressen moeten de UDR's dienovereenkomstig worden gewijzigd.


De bovenstaande stappen kunnen worden gescript als onderdeel van een [herstelplan](site-recovery-create-recovery-plans.md). Afhankelijk van de vereisten voor de connectiviteit van de toepassing en de hersteltijd kunnen de bovenstaande stappen ook worden voltooid voordat de failover wordt gestart.

#### <a name="after-recovery"></a>Na herstel

Na het herstellen van de VM's en het voltooien van de connectiviteit, is de herstelomgeving als volgt.

![On-premises-to-Azure met ExpressRoute na failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [herstelplannen](site-recovery-create-recovery-plans.md) om het mislukken van apps te automatiseren.
