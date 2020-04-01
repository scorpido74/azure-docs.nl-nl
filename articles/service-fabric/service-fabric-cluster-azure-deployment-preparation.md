---
title: Een Azure Service Fabric-clusterimplementatie plannen
description: Meer informatie over het plannen en voorbereiden van een implementatie van een productieservicefabriccluster naar Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: ad6a7a6ea9a90bea4a3b6bc553da67a46144dc03
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422287"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Een clusterimplementatie plannen en voorbereiden

Het plannen en voorbereiden van een productieclusterimplementatie is erg belangrijk.  Er zijn veel factoren om rekening mee te houden.  In dit artikel vindt u de stappen van het voorbereiden van uw clusterimplementatie.

## <a name="read-the-best-practices-information"></a>Lees de informatie over de aanbevolen procedures
Om Azure Service Fabric-toepassingen en clusters succesvol te beheren, zijn er bewerkingen die we u ten zeerste aanbevelen uit te voeren om de betrouwbaarheid van uw productieomgeving te optimaliseren.  Lees [servicefabric-toepassing en clusteraanbevolen procedures](service-fabric-best-practices-overview.md)voor meer informatie.

## <a name="select-the-os-for-the-cluster"></a>Het besturingssysteem voor het cluster selecteren
Service Fabric maakt het mogelijk om Service Fabric-clusters te maken op alle VM's of computers met Windows Server of Linux.  Voordat u uw cluster implementeert, moet u het besturingssysteem kiezen: Windows of Linux.  Elk knooppunt (virtuele machine) in het cluster draait hetzelfde besturingssysteem, u geen Windows- en Linux-VM's in hetzelfde cluster mengen.

## <a name="capacity-planning"></a>Capaciteitsplanning
Bij elke implementatie voor productie is capaciteitsplanning van groot belang. Hier volgen enkele aandachtspunten voor dat proces.

* Het beginaantal knooppunttypen voor uw cluster 
* De eigenschappen van elk knooppunttype (grootte, aantal exemplaren, primair, internetgericht, aantal VM's, enz.)
* De betrouwbaarheid en duurzaamheid van de clusterkenmerken

### <a name="select-the-initial-number-of-node-types"></a>Het beginnummer van knooppunttypen selecteren
Eerst moet u uitzoeken waarvoor het cluster dat u maakt, wordt gebruikt. Welke soorten toepassingen bent u van plan te implementeren in dit cluster? Heeft uw applicatie meerdere diensten, en heeft een van hen nodig om openbaar of internet geconfronteerd? Hebben uw diensten (die deel uitmaken van uw toepassing) verschillende infrastructuurbehoeften zoals meer RAM of hogere CPU-cycli? Een cluster servicestructuur kan bestaan uit meer dan één knooppunttype: een primair knooppunttype en een of meer niet-primaire knooppunttypen. Elk knooppunttype wordt toegewezen aan een virtuele machineschaalset. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. [De eigenschappen en plaatsingsbeperkingen][placementconstraints] van het knooppunt kunnen worden ingesteld om specifieke services te beperken tot specifieke knooppunttypen.  Lees voor meer informatie [het aantal knooppunttypen waarmee uw cluster moet beginnen](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)met .

### <a name="select-node-properties-for-each-node-type"></a>Knooppunteigenschappen selecteren voor elk knooppunttype
Node-typen definiëren de VM-SKU, het getal en de eigenschappen van de VM's in de bijbehorende schaalset.

De minimale grootte van VM's voor elk knooppunttype wordt bepaald door de [duurzaamheidslaag die][durability] u kiest voor het knooppunttype.

Het minimumaantal VM's voor het primaire knooppunttype wordt bepaald door de [betrouwbaarheidslaag die][reliability] u kiest.

Zie de minimale aanbevelingen voor [primaire knooppunttypen,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance) [stateful workloads op niet-primaire knooppunttypen](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)en [stateloze workloads op niet-primaire knooppunttypen](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Meer dan het minimumaantal knooppunten moet worden gebaseerd op het aantal replica's van de toepassing / services die u wilt uitvoeren in dit knooppunttype.  [Met capaciteitsplanning voor Service Fabric-toepassingen](service-fabric-capacity-planning.md) u de resources schatten die u nodig hebt om uw toepassingen uit te voeren. U het cluster later altijd omhoog of omlaag schalen om u aan te passen voor het wijzigen van de toepassingswerkbelasting. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Tijdelijke OS-schijven gebruiken voor virtuele machineschaalsets

*Tijdelijke OS-schijven* zijn opslag die is gemaakt op de lokale virtuele machine (VM) en niet worden opgeslagen in externe Azure Storage. Ze worden aanbevolen voor alle servicefabric-knooppunttypen (primair en secundair), omdat in vergelijking met traditionele permanente schijven van het besturingssysteem, kortstondige OS-schijven:

* Lees-/schrijflatentie naar OS-schijf verminderen
* Snellere beheerbewerkingen voor knooppuntbeheer inschakelen/opnieuw bekijken
* Verlaag de totale kosten (de schijven zijn gratis en kosten geen extra opslagruimte)

Kortstondige OS-schijven is geen specifieke functie voor servicefabric, maar eerder een functie van de *Azure-virtuele machineschaalsets* die zijn toegewezen aan servicestructuurknooppunttypen. Als u ze gebruikt met Service Fabric, moet u het volgende gebruiken in de sjabloon Azure Resource Manager van het cluster:

1. Zorg ervoor dat uw knooppunttypen [ondersteunde Azure VM-formaten](../virtual-machines/windows/ephemeral-os-disks.md) opgeven voor ephemerale OS-schijven en dat de VM-grootte voldoende cachegrootte heeft om de grootte van de OS-schijf te ondersteunen (zie *Opmerking* hieronder.) Bijvoorbeeld:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Zorg ervoor dat u een VM-grootte selecteert met een cachegrootte die gelijk is aan of groter is dan de schijfgrootte van de virtuele machine zelf, anders kan uw Azure-implementatie fouten opleveren (zelfs als deze in eerste instantie wordt geaccepteerd).

2. Geef een versie met`vmssApiVersion`virtuele `2018-06-01` machineschaalset op ( ) of later:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Geef in het gedeelte met de virtuele `Local` machineschaalset van uw implementatiesjabloon de optie op voor: `diffDiskSettings`

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Gebruikerstoepassingen mogen geen afhankelijkheid/bestand/artefact op de OS-schijf hebben, omdat de osschijf verloren zou gaan in het geval van een os-upgrade.
> Daarom is het niet aan te raden om [PatchOrchestrationApplication](https://github.com/microsoft/Service-Fabric-POA) te gebruiken met kortstondige schijven.
>

> [!NOTE]
> Bestaande niet-kortstondige VMSS kan niet worden geüpgraded om kortstondige schijven te gebruiken.
> Als u wilt migreren, moeten gebruikers een nieuw nodeType met tijdelijke schijven [toevoegen](./virtual-machine-scale-set-scale-node-type-scale-out.md) en de workloads verplaatsen naar het nieuwe knooppunt & het bestaande knooppunt [verwijderen.](./service-fabric-how-to-remove-node-type.md)
>

Zie [Efemere OS-schijven voor Azure VM's voor](../virtual-machines/windows/ephemeral-os-disks.md) meer informatie en verdere configuratieopties 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecteer de duurzaamheids- en betrouwbaarheidsniveaus voor het cluster
De duurzaamheidslaag wordt gebruikt om aan het systeem de bevoegdheden aan te geven die uw VM's hebben met de onderliggende Azure-infrastructuur. Met deze bevoegdheid kan Service Fabric in het primaire knooppunttype alle infrastructuuraanvragen op VM-niveau onderbreken (zoals een vm-reboot, vm-reimage of VM-migratie) die van invloed zijn op de quorumvereisten voor de systeemservices en uw stateful services. In de niet-primaire knooppunttypen kan Service Fabric met deze bevoegdheid alle infrastructuuraanvragen op VM-niveau (zoals vm-reboot, vm-reimage en VM-migratie) pauzeren die van invloed zijn op de quorumvereisten voor uw stateful services.  Voor voordelen van de verschillende niveaus en aanbevelingen op welk niveau te gebruiken en wanneer, zie [De duurzaamheidskenmerken van het cluster][durability].

De betrouwbaarheidslaag wordt gebruikt om het aantal replica's van de systeemservices in te stellen dat u in dit cluster wilt uitvoeren op het primaire knooppunttype. Hoe meer het aantal replica's, hoe betrouwbaarder de systeemservices in uw cluster zijn.  Voor voordelen van de verschillende niveaus en aanbevelingen op welk niveau te gebruiken en wanneer, zie [De betrouwbaarheid kenmerken van het cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Omgekeerde proxy en/of DNS inschakelen
Services die verbinding maken met elkaar binnen een cluster, hebben over het algemeen rechtstreeks toegang tot de eindpunten van andere services omdat de knooppunten in een cluster zich op hetzelfde lokale netwerk bevinden. Om het gemakkelijker te maken om verbinding te maken tussen services, biedt Service Fabric aanvullende services: een [DNS-service](service-fabric-dnsservice.md) en een [reverse proxy-service.](service-fabric-reverseproxy.md)  Beide services kunnen worden ingeschakeld bij het implementeren van een cluster.

Aangezien veel services, met name containeriseerde services, een bestaande URL-naam kunnen hebben, is het handig om deze op te lossen met behulp van het standaard DNS-protocol (in plaats van het Naming Service-protocol), vooral in toepassings "lift and shift"-scenario's. Dit is precies wat de DNS-service doet. Hiermee u DNS-namen toewijzen aan een servicenaam en dus eindpuntIP-adressen oplossen.

De omgekeerde proxy adressen services in het cluster die HTTP-eindpunten (inclusief HTTPS) blootleggen. De omgekeerde proxy vereenvoudigt het aanroepen van andere services sterk door een specifiek URI-formaat aan te bieden.  De omgekeerde proxy verwerkt ook de stappen voor het oplossen, verbinden en opnieuw proberen die nodig zijn om de ene service met een andere service te laten communiceren.

## <a name="prepare-for-disaster-recovery"></a>Voorbereiden op herstel na noodgeval
Een essentieel onderdeel van het leveren van hoge beschikbaarheid is ervoor te zorgen dat services alle verschillende soorten storingen kunnen overleven. Dit is vooral belangrijk voor storingen die ongepland en buiten uw controle vallen. [Bereid je voor op herstel na noodgevallen](service-fabric-disaster-recovery.md) beschrijft een aantal veelvoorkomende foutmodi die rampen kunnen zijn als ze niet correct zijn gemodelleerd en beheerd. Het bespreekt ook mitigaties en maatregelen te nemen als een ramp toch gebeurd.

## <a name="production-readiness-checklist"></a>Controlelijst voor productiegereedheid
Is uw toepassing en cluster klaar om productieverkeer te genereren? Voer de [checklist productiegereedheid](service-fabric-production-readiness-checklist.md)door voordat u uw cluster in productie zet. Houd uw toepassing en cluster soepel door de items in deze checklist te doorlopen. We raden aan om al deze artikelen af te vinken voordat ze in productie gaan.

## <a name="next-steps"></a>Volgende stappen
* [Een cluster van Servicefabric met Windows maken](service-fabric-best-practices-overview.md)
* [Een Service Fabric-cluster met Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster