---
title: Azure-plaatsingsgroepen voor SAP-toepassingen | Microsoft Documenten
description: Beschrijft SAP-implementatiescenario's met Azure-plaatsingsgroepen voor nabijheid
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277602"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure proximity placement groepen voor optimale netwerklatentie met SAP-toepassingen
SAP-toepassingen op basis van de SAP NetWeaver- of SAP S/4HANA-architectuur zijn gevoelig voor netwerklatentie tussen de SAP-toepassingslaag en de SAP-databaselaag. Deze gevoeligheid is het resultaat van de meeste bedrijfslogica die in de toepassingslaag wordt uitgevoerd. Omdat de SAP-toepassingslaag de bedrijfslogica uitvoert, worden query's met een hoge frequentie met een snelheid van duizenden of tienduizenden per seconde aan de databaselaag uitgevoerd. In de meeste gevallen is de aard van deze query's eenvoudig. Ze kunnen vaak worden uitgevoerd op de database laag in 500 microseconden of minder.

De tijd die aan het netwerk wordt besteed om een dergelijke query van de toepassingslaag naar de databaselaag te verzenden en het ingestelde resultaat te ontvangen, heeft een grote invloed op de tijd die nodig is om bedrijfsprocessen uit te voeren. Deze gevoeligheid voor netwerklatentie is de reden waarom u een optimale netwerklatentie moet bereiken in SAP-implementatieprojecten. Zie [SAP Note #1100926 - FAQ: Netwerkprestaties](https://launchpad.support.sap.com/#/notes/1100926/E) voor richtlijnen voor het classificeren van de netwerklatentie.

In veel Azure-regio's is het aantal datacenters gegroeid. Deze groei is ook veroorzaakt door de invoering van beschikbaarheidszones. Tegelijkertijd gebruiken klanten, vooral voor high-end SAP-systemen, meer speciale VM SKU's in de M-serie- of HANA Large Instances. Deze Azure-typen voor virtuele machines zijn niet beschikbaar in alle datacenters in een specifieke Azure-regio. Door deze twee tendensen hebben klanten netwerklatentie ervaren die niet in het optimale bereik ligt. In sommige gevallen resulteert deze latentie in suboptimale prestaties van hun SAP-systemen.

Om deze problemen te voorkomen, biedt Azure [nabijheidsplaatsingsgroepen](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Deze nieuwe functionaliteit is al gebruikt om verschillende SAP-systemen te implementeren. Zie het artikel waarnaar wordt verwezen aan het begin van deze alinea voor beperkingen op plaatsingsgroepen voor nabijheidsplaatsing. In dit artikel worden de SAP-scenario's bewerkt waarin azure-plaatsingsgroepen voor nabijheid kunnen of moeten worden gebruikt.

## <a name="what-are-proximity-placement-groups"></a>Wat zijn nabijheidsplaatsingsgroepen? 
Een Azure-plaatsingsgroep voor nabijheid is een logische constructie. Wanneer een azure-regio en een Azure-brongroep zijn gedefinieerd, is deze afhankelijk van een Azure-regio en een Azure-brongroep. Wanneer VM's worden geïmplementeerd, wordt een groep voor nabijheidsplaatsing verwezen door:

- De eerste Azure VM geïmplementeerd in het datacenter. U de eerste virtuele machine zien als een 'scope VM' die wordt geïmplementeerd in een datacenter op basis van Azure-toewijzingsalgoritmen die uiteindelijk worden gecombineerd met gebruikersdefinities voor een specifieke beschikbaarheidszone.
- Alle volgende VM's die verwijzen naar de plaatsingsgroep voor nabijheid, om alle vervolgens geïmplementeerde Azure VM's in hetzelfde datacenter te plaatsen als de eerste virtuele machine.

> [!NOTE]
> Als er geen hosthardware is geïmplementeerd die een specifiek VM-type kan uitvoeren in het datacenter waar de eerste VM is geplaatst, wordt de implementatie van het gevraagde VM-type niet uitgevoerd. Je krijgt een foutmelding.

Aan één [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kunnen meerdere plaatsingsgroepen voor nabijheid worden toegewezen. Een plaatsingsgroep voor nabijheid kan echter slechts aan één Azure-brongroep worden toegewezen.

Wanneer u nabijheidsplaatsingsgroepen gebruikt, houdt u rekening met deze overwegingen:

- Wanneer u streeft naar optimale prestaties voor uw SAP-systeem en zich beperkt tot één Azure-datacenter voor het systeem met behulp van nabijheidsplaatsingsgroepen, u mogelijk niet alle typen VM-families binnen de plaatsingsgroep combineren. Deze beperkingen treden op omdat de hosthardware die nodig is om een bepaald VM-type uit te voeren, mogelijk niet aanwezig is in het datacenter waarop de 'scoped VM' van de plaatsingsgroep is geïmplementeerd.
- Tijdens de levenscyclus van zo'n SAP-systeem kan je gedwongen worden om het systeem naar een ander datacenter te verplaatsen. Deze verplaatsing kan nodig zijn als u besluit dat uw scale-out HANA DBMS-laag bijvoorbeeld moet worden verplaatst van vier knooppunten naar 16 knooppunten en er niet genoeg capaciteit is om 12 VM's te krijgen van het type dat u in het datacenter hebt gebruikt.
- Vanwege de ontmanteling van hardware kan Microsoft capaciteit opbouwen voor een VM-type dat u in een ander datacenter hebt gebruikt, in plaats van het type dat u in eerste instantie hebt gebruikt. In dat scenario moet u mogelijk de vm's van de hele nabijheidsplaatsingsgroep naar een ander datacenter verplaatsen.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Nabijheidsplaatsingsgroepen met SAP-systemen die alleen Azure VM's gebruiken
De meeste SAP NetWeaver- en S/4HANA-systeemimplementaties op Azure maken geen gebruik van [HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Voor implementaties die geen HANA Large Instances gebruiken, is het belangrijk om optimale prestaties te bieden tussen de SAP-toepassingslaag en de DBMS-laag. Definieer hiervoor een Azure-plaatsingsgroep voor nabijheid alleen voor het systeem.

In de meeste klantimplementaties bouwen klanten één [Azure-brongroep](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) voor SAP-systemen. In dat geval is er een één-op-één relatie tussen bijvoorbeeld de brongroep voor productie-ERP-systemen en de plaatsingsgroep voor nabijheid. In andere gevallen organiseren klanten hun resourcegroepen horizontaal en verzamelen ze alle productiesystemen in één resourcegroep. In dit geval hebt u een één-op-één relatie tussen uw resourcegroep voor productieSAP-systemen en verschillende nabijheidsplaatsingsgroepen voor uw productie SAP ERP, SAP BW, enzovoort.

Vermijd het bundelen van verschillende SAP-productie- of niet-productiesystemen in één nabijheidsplaatsingsgroep. Wanneer een klein aantal SAP-systemen of een SAP-systeem en sommige omliggende toepassingen een netwerkcommunicatie met een lage latentie moeten hebben, u overwegen deze systemen in één nabijheidsplaatsingsgroep te verplaatsen. U moet bundels van systemen vermijden, omdat hoe meer systemen u groepeert in een nabijheidsplaatsingsgroep, hoe groter de kans:

- Dat u een VM-type nodig hebt dat niet kan worden uitgevoerd in het specifieke datacenter waarin de plaatsingsgroep voor nabijheid is ingericht.
- Dat resources van niet-mainstream VM's, zoals VM's uit de M-serie, uiteindelijk onvervuld kunnen zijn wanneer u meer nodig hebt omdat u software toevoegt aan een nabijheidsplaatsingsgroep na verloop van tijd.

Zo ziet de ideale configuratie eruit, zoals beschreven, eruit:

![Plaatsingsgroepen voor nabijheid met alleen Azure VM's](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In dit geval worden afzonderlijke SAP-systemen gegroepeerd in één resourcegroep, met elk één nabijheidsplaatsingsgroep. Er is geen afhankelijkheid van de vraag of u HANA scale-out of DBMS scale-up configuraties gebruikt.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Nabijheidsplaatsingsgroepen en HANA-grote instanties
Als sommige van uw SAP-systemen afhankelijk zijn van [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor de toepassingslaag, u aanzienlijke verbeteringen in netwerklatentie tussen de EENHEID HANA Large Instances en Azure VM's ervaren wanneer u HANA Large Instances-eenheden gebruikt die zijn geïmplementeerd in [revisie 4 rijen of stempels.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Een verbetering is dat HANA Large Instances-eenheden, zoals ze worden geïmplementeerd, worden geïmplementeerd met een nabijheidsplaatsingsgroep. U die nabijheidsplaatsingsgroep gebruiken om vm's van de toepassingslaag te implementeren. Als gevolg hiervan worden deze VM's geïmplementeerd in hetzelfde datacenter dat uw HANA Large Instances-eenheid host.

Als u wilt bepalen of uw HANA-eenheid voor grote instanties is geïmplementeerd in een revisie 4-stempel of -rij, controleert u het artikel [azure HANA Large Instances-besturingselement via Azure-portal.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit) In het kenmerkenoverzicht van uw HANA-eenheid voor grote instanties u ook de naam van de groep nabijheidsplaatsing bepalen omdat deze is gemaakt toen uw HANA-eenheid voor grote instanties is geïmplementeerd. De naam die wordt weergegeven in het kenmerkenoverzicht is de naam van de groep nabijheidsplaatsing waarin u de VM's van de toepassingslaag moet implementeren.

In vergelijking met SAP-systemen die alleen gebruik maken van virtuele Azure-machines, hebt u bij het gebruik van HANA Large Instances minder flexibiliteit bij het bepalen van het aantal [Azure-brongroepen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) dat u wilt gebruiken. Alle eenheden HANA Large Instances van een [HANA Large Instances-tenant](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) zijn gegroepeerd in één resourcegroep, zoals [beschreven in dit artikel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Tenzij u in verschillende tenants implementeert om bijvoorbeeld productie- en niet-productiesystemen of andere systemen te scheiden, worden al uw HANA Large Instances-eenheden geïmplementeerd in één HANA Large Instances-tenant. Deze tenant heeft een één-op-één relatie met een resourcegroep. Maar voor elk van de afzonderlijke eenheden wordt een afzonderlijke plaatsingsgroep voor nabijheid gedefinieerd.

Als gevolg hiervan worden de relaties tussen Azure-brongroepen en nabijheidsplaatsingsgroepen voor één tenant weergegeven zoals hier wordt weergegeven:

![Nabijheidsplaatsingsgroepen en HANA-grote instanties](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Voorbeeld van implementatie met nabijheidsplaatsingsgroepen
Hieronder volgen enkele PowerShell-opdrachten die u gebruiken om uw VM's te implementeren met Azure-plaatsingsgroepen voor nabijheid.

De eerste stap, nadat u zich hebt aangemeld bij [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)is om te controleren of u zich in het Azure-abonnement bevindt dat u wilt gebruiken voor de implementatie:

<pre><code>
Get-AzureRmContext
</code></pre>

Als u wilt overstappen op een ander abonnement, u dit doen door deze opdracht uit te voeren:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Maak een nieuwe Azure-brongroep door deze opdracht uit te voeren:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Maak de nieuwe groep nabijheidsplaatsing door deze opdracht uit te voeren:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Implementeer uw eerste VM in de groep nabijheidsplaatsing met een opdracht als deze:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

De vorige opdracht implementeert een vm op basis van Windows. Nadat deze VM-implementatie is geslaagd, wordt het datacenterbereik van de groep nabijheidsplaatsing gedefinieerd binnen de Azure-regio. Alle volgende VM-implementaties die verwijzen naar de plaatsingsgroep voor nabijheid, zoals weergegeven in de vorige opdracht, worden geïmplementeerd in hetzelfde Azure-datacenter, zolang het VM-type kan worden gehost op hardware die in dat datacenter is geplaatst en de capaciteit voor dat VM-type is Beschikbaar.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Beschikbaarheidssets en beschikbaarheidszones combineren met plaatsingsgroepen voor nabijheid
Een van de nadelen van het gebruik van beschikbaarheidszones voor SAP-systeemimplementaties is dat u de SAP-toepassingslaag niet implementeren met beschikbaarheidssets binnen de specifieke zone. U wilt dat de SAP-toepassingslaag wordt geïmplementeerd in dezelfde zones als de DBMS-laag. Een verwijzing naar een beschikbaarheidszone en een beschikbaarheidsset bij het implementeren van één virtuele machine wordt niet ondersteund. Dus voorheen werd u gedwongen om uw toepassingslaag te implementeren door te verwijzen naar een zone. U hebt de mogelijkheid verloren om ervoor te zorgen dat de VM's van de toepassingslaag zijn verspreid over verschillende update- en foutdomeinen.

Door nabijheidsplaatsingsgroepen te gebruiken, u deze beperking omzeilen. Hier is de implementatiereeks:

- Maak een plaatsingsgroep voor nabijheid.
- Implementeer uw anker-VM, meestal de DBMS-server, door te verwijzen naar een beschikbaarheidszone.
- Maak een beschikbaarheidsset die verwijst naar de azure-nabijheidsgroep. (Zie de opdracht later in dit artikel.)
- Implementeer de VM's van de toepassingslaag door te verwijzen naar de beschikbaarheidsset en de plaatsingsgroep voor nabijheid.

In plaats van de eerste VM te implementeren zoals in de vorige sectie is aangetoond, verwijst u naar een beschikbaarheidszone en de groep nabijheidsplaatsing wanneer u de VM implementeert:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Een succesvolle implementatie van deze virtuele machine zou de database-instantie van het SAP-systeem hosten in één beschikbaarheidszone. Het bereik van de groep nabijheidsplaatsing is bevestigd aan een van de datacenters die de beschikbaarheidszone vertegenwoordigen die u hebt gedefinieerd.

Stel dat u de VM's voor centrale services op dezelfde manier implementeert als de DBMS-VM's, waarbij wordt verwezen naar dezelfde zone of zones en dezelfde plaatsingsgroepen voor nabijheid. In de volgende stap moet u de beschikbaarheidssets maken die u wilt gebruiken voor de toepassingslaag van uw SAP-systeem.

U moet de plaatsingsgroep voor nabijheid definiëren en maken. De opdracht voor het maken van de beschikbaarheidsset vereist een extra verwijzing naar de groeps-id voor nabijheidsplaatsing (niet de naam). U de id van de groep nabijheidsplaatsing opdoen met behulp van deze opdracht:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Wanneer u de beschikbaarheidsset maakt, moet u rekening houden met aanvullende parameters wanneer u beheerde schijven gebruikt (standaard tenzij anders opgegeven) en plaatsingsgroepen voor nabijheid:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealiter moet u drie foutdomeinen gebruiken. Maar het aantal ondersteunde foutdomeinen kan per regio verschillen. In dit geval is het maximum aantal foutdomeinen dat mogelijk is voor de specifieke regio's twee. Als u de VM's van de toepassingslaag wilt implementeren, moet u een verwijzing toevoegen naar de naam van uw beschikbaarheidsset en de naam van de plaatsingsgroep in de nabijheid, zoals hier wordt weergegeven:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Het resultaat van deze implementatie is:
- Een DBMS-laag en Centrale Services voor uw SAP-systeem dat zich in een specifieke beschikbaarheidszone of beschikbaarheidszones bevindt.
- Een SAP-toepassingslaag die zich bevindt via beschikbaarheidssets in dezelfde Azure-datacenters als de DBMS VM of VM's.

> [!NOTE]
> Omdat u één DBMS VM in één zone en de tweede DBMS VM in een andere zone implementeert om een configuratie met hoge beschikbaarheid te maken, hebt u voor elk van de zones een andere plaatsingsgroep voor de nabijheid nodig. Hetzelfde geldt voor elke beschikbaarheidset die u gebruikt.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Een bestaand systeem verplaatsen naar plaatsingsgroepen in de nabijheid
Als u al SAP-systemen hebt geïmplementeerd, wilt u misschien de netwerklatentie van sommige van uw kritieke systemen optimaliseren en de toepassingslaag en de DBMS-laag in hetzelfde datacenter lokaliseren. Als u de VM's van een volledige Azure-beschikbaarheidsset wilt verplaatsen naar een bestaande plaatsingsgroep voor nabijheid die al is ingesteld, moet u alle VM's van de beschikbaarheidsset afsluiten en de beschikbaarheidsset toewijzen aan de bestaande plaatsingsgroep voor nabijheid via Azure-portal, PowerShell of CLI. Als u een vm wilt verplaatsen die geen deel uitmaakt van een beschikbaarheidsset in een bestaande groep voor plaatsing in de nabijheid, hoeft u de VM alleen maar af te sluiten en toe te wijzen aan een bestaande groep voor plaatsing in de nabijheid. 


## <a name="next-steps"></a>Volgende stappen
Bekijk de documentatie:

- [SAP-workloads op Azure: checklist voor planning en implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Voorbeeld: VM's implementeren voor nabijheidsplaatsingsgroepen met Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Voorbeeld: VM's implementeren voor nabijheidsplaatsingsgroepen met PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

