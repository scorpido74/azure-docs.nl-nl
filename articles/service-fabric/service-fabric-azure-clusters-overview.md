---
title: Clusters maken op Windows Server en Linux
description: Service Fabric-clusters draaien op Windows Server en Linux, wat betekent dat u Service Fabric-toepassingen overal implementeren en hosten waar u Windows Server of Linux uitvoeren.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614669"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Overzicht van clusters van servicefabric op Azure
Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster, wordt een clusterknooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knooppunten. Als u nieuwe knooppunten aan het cluster toevoegt, brengt Service Fabric de replica's en instanties van de servicepartitie opnieuw in evenwicht in het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de discussie over de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, u het aantal knooppunten in het cluster verminderen. Service Fabric brengt de partitiereplica's en instanties opnieuw in evenwicht in het verminderde aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt.

Een knooppunttype definieert de grootte, het aantal en de eigenschappen voor een set knooppunten (virtuele machines) in het cluster. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Knooppunttypen worden gebruikt voor het definiëren van rollen voor een set clusterknooppunten, zoals 'front-end' of 'back-end'. Uw cluster kan meer dan één knooppunttype hebben, maar voor productieclusters moet het primaire knooppunttype ten minste vijf VM's bevatten (of ten minste drie VM's voor testclusters). [Service Fabric-systeemservices](service-fabric-technical-overview.md#system-services) worden op de knooppunten van het primaire knooppunttype geplaatst. 

## <a name="cluster-components-and-resources"></a>Clustercomponenten en -bronnen
Een Cluster Van Servicefabric op Azure is een Azure-bron die andere Azure-bronnen gebruikt en ermee communiceert:
* VM's en virtuele netwerkkaarten
* schaalsets voor virtuele machines
* virtuele netwerken
* load balancers
* opslagaccounts
* openbare IP-adressen

![Cluster servicestructuur][Image]

### <a name="virtual-machine"></a>Virtuele machine
Een [virtuele machine](/azure/virtual-machines/) die deel uitmaakt van een cluster wordt wel een knooppunt genoemd, maar technisch gezien is een clusterknooppunt een runtime-proces van Service Fabric. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken, zoals [plaatsingseigenschappen.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Elke machine of VM heeft een auto-start service, *FabricHost.exe*, die begint te draaien op de boot tijd en start dan twee executables, *Fabric.exe* en *FabricGateway.exe*, die deel uitmaken van het knooppunt. Een productie-implementatie is één knooppunt per fysieke of virtuele machine. Voor het testen van scenario's u meerdere knooppunten hosten op één machine of VM door meerdere exemplaren van *Fabric.exe* en *FabricGateway.exe*uit te voeren.

Elke VM is gekoppeld aan een virtuele netwerkinterfacekaart (NIC) en elke NIC krijgt een privé-IP-adres.  Een VM wordt toegewezen aan een virtueel netwerk en lokale balancer via de NIC.

Alle VM's in een cluster worden in een virtueel netwerk geplaatst.  Alle knooppunten in hetzelfde knooppunttype/schaalset worden op hetzelfde subnet op het virtuele netwerk geplaatst.  Deze knooppunten hebben alleen privé-IP-adressen en zijn niet direct adresseerbaar buiten het virtuele netwerk.  Clients hebben toegang tot services op de knooppunten via de Azure load balancer.

### <a name="scale-setnode-type"></a>Type schaalset/knooppunt
Wanneer u een cluster maakt, definieert u een of meer knooppunttypen.  De knooppunten of VM's in een knooppunttype hebben dezelfde grootte en kenmerken, zoals het aantal CPU's, het geheugen, het aantal schijven en de i/o van de schijf.  Bijvoorbeeld, een knooppunt type kan worden voor kleine, front-end VM's met poorten open voor het internet, terwijl een ander knooppunt type zou kunnen zijn voor grote, back-end VM's die gegevens verwerken. In Azure-clusters wordt elk knooppunttype toegewezen aan een [virtuele machineschaalset.](/azure/virtual-machine-scale-sets/)

U schaalsets gebruiken om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat u definieert in een Azure Service Fabric-cluster, stelt een aparte schaalset in. De runtime van Service Fabric wordt op elke virtuele machine in de schaalset gestart met Azure VM-extensies. U elk knooppunttype zelfstandig omhoog of omlaag schalen, de OS SKU wijzigen die op elk clusterknooppunt wordt uitgevoerd, verschillende sets poorten openen en verschillende capaciteitsstatistieken gebruiken. Een schaalset heeft vijf [upgradedomeinen](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) en vijf [foutdomeinen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) en kan maximaal 100 VM's hebben.  U maakt clusters van meer dan 100 knooppunten door meerdere schaalsets/knooppunttypen te maken.

> [!IMPORTANT]
> Het kiezen van het aantal knooppunttypen voor uw cluster en de eigenschappen van elk knooppunttype (grootte, primair, internetgericht, aantal VM's, enz.) is een belangrijke taak.  Lees voor meer informatie [overwegingen voor clustercapaciteitsplanning](service-fabric-cluster-capacity.md).

Lees voor meer informatie [servicestructuurtypen en virtuele machineschaalsets](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-exemplaren worden samengevoegd achter een [Azure-load balancer,](/azure/load-balancer/load-balancer-overview)die is gekoppeld aan een [openbaar IP-adres](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) en DNS-label.  Wanneer u een cluster met * &lt;&gt;clusternaam*indient, de DNS-naam, * &lt;&gt;clusternaam .&lt; locatie&gt;.cloudapp.azure.com* is het DNS-label dat is gekoppeld aan de load balancer voor de schaalset.

VM's in een cluster hebben alleen [privé-IP-adressen.](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)  Het verkeer en het serviceverkeer worden omgeleid via de openbare laadbalans.  Netwerkverkeer wordt naar deze machines doorgestuurd via NAT-regels (clients maken verbinding met specifieke knooppunten/instanties) of regels voor taakverdeling (verkeer gaat naar VM's rond roodborstje).  Een load balancer heeft een gekoppeld openbaar IP met een DNS-naam in de indeling: * &lt;clusternaam&gt;.&lt; locatie&gt;.cloudapp.azure.com*.  Een openbaar IP-adres is een andere Azure-bron in de brongroep.  Als u meerdere knooppunttypen in een cluster definieert, wordt voor elk knooppunttype/schaalset een load balancer gemaakt. U ook één load balancer instellen voor meerdere nodetypen.  Het primaire knooppunttype heeft de * &lt;clusternaam&gt;DNS-label .&lt; locatie&gt;.cloudapp.azure.com*, andere knooppunttypen hebben het * &lt;nodetype&gt;-&lt;DNS-labelclusternaam&gt;&lt; . locatie&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Opslagaccounts
Elk clusterknooppunttype wordt ondersteund door een [Azure-opslagaccount](/azure/storage/common/storage-introduction) en beheerde schijven.

## <a name="cluster-security"></a>Clusterbeveiliging
Een cluster van ServiceFabric is een bron waarvan u eigenaar bent.  Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voorkomen dat onbevoegde gebruikers er verbinding mee maken. Een beveiligd cluster is vooral belangrijk wanneer u productieworkloads op het cluster uitvoert. 

### <a name="node-to-node-security"></a>Beveiliging van knooppunt tot knooppunt
Beveiliging van knooppunten beveiligt de communicatie tussen de VM's of computers in een cluster. Dit beveiligingsscenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deelnemen aan hostingtoepassingen en -services in het cluster. Service Fabric gebruikt X.509-certificaten om een cluster te beveiligen en beveiligingsfuncties voor toepassingen te bieden.  Er is een clustercertificaat vereist om clusterverkeer te beveiligen en cluster- en serververificatie te bieden.  Zelfondertekende certificaten kunnen worden gebruikt voor testclusters, maar een certificaat van een vertrouwde certificaatautoriteit moet worden gebruikt om productieclusters te beveiligen.

Lees [de beveiliging van knooppunt tot knooppunt voor](service-fabric-cluster-security.md#node-to-node-security) meer informatie

### <a name="client-to-node-security"></a>Client-to-node beveiliging
Client-to-node beveiliging verifieert clients en helpt de communicatie tussen een client en afzonderlijke knooppunten in het cluster te beveiligen. Dit type beveiliging zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients worden uniek geïdentificeerd door middel van hun X.509 certificaat beveiligingsreferenties. Een willekeurig aantal optionele clientcertificaten kan worden gebruikt om beheerders- of gebruikersclients met het cluster te verifiëren.

Naast clientcertificaten kan Azure Active Directory ook worden geconfigureerd om clients met het cluster te verifiëren.

Lees voor meer informatie [client-to-nodebeveiliging](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer
Met RBAC (Role-Based Access Control) u fijnmazige toegangsbesturingselementen toewijzen aan Azure-bronnen.  U verschillende toegangsregels toewijzen aan abonnementen, brongroepen en resources.  RBAC-regels worden overgenomen in de resourcehiërarchie, tenzij deze op een lager niveau worden overschreven.  U elke gebruiker of gebruikersgroepen aan uw AAD toewijzen met RBAC-regels, zodat aangewezen gebruikers en groepen uw cluster kunnen wijzigen.  Lees voor meer informatie het [Azure RBAC-overzicht.](/azure/role-based-access-control/overview)

Service Fabric ondersteunt ook toegangscontrole om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster veiliger te maken. Er worden twee typen toegangsbeheer ondersteund voor clients die verbinding maken met een cluster: de rol Administrator en de rol Gebruiker.  

Lees voor meer informatie [Service Fabric Role-Based Access Control (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen 
Netwerkbeveiligingsgroepen (NSG's) beheren binnenkomend en uitgaand verkeer van een subnet, VM of specifieke NIC.  Wanneer meerdere VM's op hetzelfde virtuele netwerk worden geplaatst, kunnen ze standaard met elkaar communiceren via elke poort.  Als u de communicatie tussen de machines wilt beperken, u NSGs definiëren om het netwerk te segmenteren of VM's van elkaar te isoleren.  Als u meerdere knooppunttypen in een cluster hebt, u NSG's toepassen op subnetten om te voorkomen dat machines die tot verschillende knooppunttypen behoren, met elkaar communiceren.  

Lees voor meer informatie over [beveiligingsgroepen](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Schalen

Toepassingseisen veranderen in de loop van de tijd. Mogelijk moet u de clusterresources vergroten om te voldoen aan de toegenomen toepassingsworkload of het netwerkverkeer of clusterbronnen verlagen wanneer de vraag daalt. Nadat u een cluster servicestructuur hebt gemaakt, u het cluster horizontaal schalen (het aantal knooppunten wijzigen) of verticaal wijzigen (de bronnen van de knooppunten wijzigen). U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd. Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

Lees [Azure-clusters schalen](service-fabric-cluster-scaling.md)voor meer informatie.

## <a name="upgrading"></a>Upgraden
Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent, maar die gedeeltelijk door Microsoft wordt beheerd. Microsoft is verantwoordelijk voor het patchen van het onderliggende besturingssysteem en het uitvoeren van servicefabric-runtime-upgrades op uw cluster. U instellen dat uw cluster automatische runtime-upgrades ontvangt, wanneer Microsoft een nieuwe versie uitbrengt, of ervoor kiezen om een ondersteunde runtime-versie te selecteren die u wilt. Naast runtime-upgrades u ook clusterconfiguratie bijwerken, zoals certificaten of toepassingspoorten.

Lees voor meer informatie [clusters voor upgraden.](service-fabric-cluster-upgrade.md)

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
U clusters maken op virtuele machines waarop deze besturingssystemen worden uitgevoerd:

| Besturingssysteem | Vroegst ondersteunde Service Fabric-versie |
| --- | --- |
| Windows Server 2012 R2 | Alle versies |
| Windows Server 2016 | Alle versies |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Zie Ondersteunde [clusterversies in Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems) voor meer informatie

> [!NOTE]
> Als u besluit Service Fabric te implementeren op Windows Server 1709, moet u er rekening mee houden dat (1) het geen servicebranch voor lange termijn is, dus het kan zijn dat u in de toekomst versies moet verplaatsen en (2) als u containers implementeert, werken containers die op Windows Server 2016 zijn gebouwd, niet op Windows Server 1709, en vice versa (je moet ze opnieuw opbouwen om ze te implementeren).
>


## <a name="next-steps"></a>Volgende stappen
Lees meer over [het beveiligen,](service-fabric-cluster-security.md) [schalen](service-fabric-cluster-scaling.md)en [upgraden van](service-fabric-cluster-upgrade.md) Azure-clusters.

Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
