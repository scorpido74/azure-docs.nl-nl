---
title: Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen | Microsoft Docs
description: Beschrijft SAP-implementatie scenario's met Azure proximity placement groups
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077988"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure-nabijheidplaatsingsgroepen voor optimale netwerklatentie met SAP-toepassingen
SAP-toepassingen die zijn gebaseerd op de architectuur van SAP NetWeaver of SAP S/4HANA, zijn gevoelig voor netwerk latentie tussen de SAP-toepassingslaag en de SAP-gegevenslaag. De reden voor deze gevoeligheid van deze architecturen wordt geroot in het feit dat het meren deel van de bedrijfs logica wordt uitgevoerd in de toepassingslaag. Als gevolg van het uitvoeren van de bedrijfs logica, verzendt de SAP-toepassings laag query's naar de database laag in hoge frequentie van duizenden en tien duizenden per seconde. In de meeste gevallen is de aard van deze query's eenvoudig. En kunnen vaak worden uitgevoerd op de database laag in minder dan 500 micro seconden of zelfs minder. De tijd die wordt besteed aan het netwerk om een dergelijke query vanuit de toepassingslaag naar de databaserol te verzenden en de resultatenset terug te halen uit de databaserol, heeft aanzienlijke invloed op de tijd die nodig is om bedrijfs processen uit te voeren. Deze gevoeligheid voor netwerk latentie is de reden waarom de tijd moet worden besteed aan SAP-implementatie projecten om een optimale netwerk latentie te krijgen. In [SAP-notitie #1100926-Veelgestelde vragen: Netwerk prestaties](https://launchpad.support.sap.com/#/notes/1100926/E), SAP heeft enkele richt lijnen gepubliceerd voor het classificeren van de netwerk latentie.

Aan de één zijde, in veel Azure-regio's, is het aantal data centers gegroeid, ook geactiveerd door de introductie van Beschikbaarheidszones. Aan de andere kant, klanten, met name voor hoogwaardige SAP-systemen, gebruikte meer speciale VM-Sku's uit de M-serie of HANA grote instanties. Typen virtuele machines van Azure die niet aanwezig zijn in alle data centers in een specifieke Azure-regio. Als gevolg van deze twee Tendencies hebben klanten cases aangetroffen, waarbij de netwerk latentie niet in het optimale bereik ligt en in sommige gevallen de prestaties van hun SAP-systemen in het ideale geval zijn geresulteerd.

Om dit probleem te voor komen, biedt Azure een constructie die een [Azure proximity-plaatsings groep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)wordt genoemd. Deze nieuwe functionaliteit is al gebruikt voor het implementeren van verschillende verschillende SAP-systemen. Raadpleeg het artikel waarnaar wordt verwezen voor beperkingen van proximity-plaatsings groepen. Dit artikel heeft betrekking op de verschillende SAP-scenario's waarbij Azure proximity-plaatsings groepen kunnen worden gebruikt of moeten worden gebruikt.

## <a name="what-are-proximity-placement-groups"></a>Wat zijn proximity-plaatsings groepen 
Een Azure proximity-plaatsings groep is een logische construct, die tijdens de definitie fase is gebonden aan een Azure-regio en een Azure-resource groep. Tijdens de implementatie van virtuele machines wordt naar een plaatsings groep voor nabijheid verwezen door:

- De eerste geïmplementeerde Azure-VM die moet worden afgewikkeld op het Data Center. De eerste virtuele machine kan worden gezien als een anker-VM die wordt geïmplementeerd in een Data Center op basis van Azure-toewijzings algoritmen die uiteindelijk worden gecombineerd met de gebruikers definities voor een specifieke Azure-beschikbaarheids zone.
- Door alle volgende Vm's die zijn geïmplementeerd, wordt verwezen naar de locatie van de Proximity-plaatsing om alle volgende geïmplementeerde Azure-Vm's in hetzelfde Data Center te plaatsen als waarop de eerste virtuele machine is geplaatst.

> [!NOTE]
> Als er geen host-hardware is geïmplementeerd waardoor een specifiek VM-type kan worden uitgevoerd in hetzelfde Data Center als waarop de eerste VM is geplaatst, mislukt de implementatie van het aangevraagde VM-type en eindigt met een fout bericht. Dit kunnen gevallen zijn waarin meer niet-mainstream Vm's, zoals virtuele machines met Gpu's of HPC-VM-typen, moeten worden gecentreerd, bijvoorbeeld een virtuele machine uit de M-serie die is geïmplementeerd als eerste VM-type

Aan één [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kunnen meerdere proximity-plaatsings groepen worden toegewezen. Een proximity-plaatsings groep kan echter slechts worden toegewezen aan één Azure-resource groep.

U moet rekening houden met behulp van proximity placement groups:

- Wanneer u voor de meest optimale prestaties voor uw SAP-systeem beschikt en u beperkt tot een enkel Azure-Data Center voor dit systeem met behulp van proximity-plaatsings groepen, is het mogelijk dat u niet alle typen VM-families kunt combi neren in een dergelijke plaatsings groep. Als gevolg hiervan is het mogelijk dat bepaalde host-hardware die nodig is om uitsluitend een bepaald VM-type uit te voeren, niet aanwezig is in het Data Center uw ' anchor-VM ' van de plaatsings groep die is geïmplementeerd
- In de levens cyclus van een dergelijk SAP-systeem kunt u het systeem gedwongen verplaatsen naar een ander Data Center. Een dergelijke verplaatsing kan worden geforceerd in gevallen waarin u hebt besloten dat uw scale-out HANA DBMS-laag bijvoorbeeld van vier knoop punten moet worden verplaatst naar 16 knoop punten. Maar er is niet voldoende capaciteit meer voor het verkrijgen van een extra 12 Vm's van het type dat u al in hetzelfde Data Center gebruikt.
- Als gevolg van het buiten gebruik stellen van hardware, kan micro soft capaciteit bouwen voor de VM-typen die u hebt gebruikt in een ander Data Center, in plaats van hetzelfde Data Center. Een dergelijk voorval kan betekenen dat u de virtuele machines van de plaatsings groep nabij naar een ander Data Center kunt verplaatsen.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure-plaatsings groepen met SAP-systemen die exclusief gebruikmaken van Azure Vm's
De meeste gevallen van SAP NetWeaver en S/4HANA Systems-implementaties in azure gebruiken geen [Hana grote instanties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Voor implementaties van dergelijke systemen is het belang rijk om de meest optimale prestaties te bieden tussen de SAP-toepassingslaag en de DBMS-laag. Als u dit wilt doen, definieert u een Azure proximity-plaatsings groep voor dit systeem. In de meeste implementaties van klanten hebben klanten besloten één [Azure-resource groep](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) te bouwen voor SAP-systemen. In dat geval zou er een 1:1-relatie zijn tussen, bijvoorbeeld de resource groepen van de productie ERP-systeem en de plaatsings groep voor de Proximity. In sommige andere implementatie cases hebben klanten hun resource groepen horizon taal geordend en alle productie systemen verzameld in één resource groep. In dat geval zou u een 1 tot veel relatie hebben tussen de resource groep voor productie-SAP-systemen en verschillende proximity-plaatsings groepen van uw productie-SAP ERP, SAP BW, enzovoort. Het bundelen van meerdere of zelfs alle SAP-productie-of niet-productie systemen in één proximity-plaatsings groep moet worden vermeden. In uitzonde ringen, waarbij een klein aantal SAP-systemen of een SAP-systeem en sommige omringende toepassingen een netwerk communicatie met een lage latentie nodig hebben, kunt u overwegen om deze systemen te verplaatsen naar één proximity-plaatsings groep. De reden hiervoor is dat de meer systemen die u groepeert in een proximity-plaatsings groep, hoe hoger de kans is:

- U hebt een VM-type nodig dat niet kan worden uitgevoerd in het specifieke Data Center. de Proximity-plaatsings groep is verankerd in.
- Deze bronnen van bepaalde niet-mainstream Vm's, zoals M-serie, kunnen uiteindelijk niet meer worden vervuld wanneer u meer vragen krijgt wanneer u extra software toevoegt aan een bestaande plaatsings groep in de loop van de tijd.

Het ideale gebruik zoals wordt beschreven, ziet er als volgt uit:

![Proximity placemen-groepen voor alle Azure-Vm's](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In dit geval worden één SAP-systeem gegroepeerd in één resource groep elk met één proximity-plaatsings groep. Er is geen afhankelijkheid van het gebruik van HANA scale-out-of DBMS-schaal configuraties.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure proximity-plaatsings groepen en HANA grote instanties
Voor de gevallen waarin sommige van uw SAP-systemen zijn gebaseerd op [Hana grote instanties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) als Application Layer, kunnen ernstige verbeteringen in de netwerk latentie tussen de Hana-grote exemplaar-eenheid en Azure-vm's worden gerealiseerd wanneer u Hana grote exemplaar eenheden gebruikt die zijn geïmplementeerd in [revisie 4 rijen of stem pels](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Een van de verbeteringen is dat HANA-eenheden voor grote instanties, wanneer ze zijn geïmplementeerd, een proximity-plaatsings groep ophalen die al is geïmplementeerd. U kunt die proximity-plaatsings groep gebruiken om uw Application Layer-Vm's te implementeren. Als gevolg hiervan worden deze virtuele machines geïmplementeerd in hetzelfde Data Center dat als host fungeert voor uw HANA grote instantie-eenheid.

Als u wilt detecteren of uw HANA grote instantie-eenheid is geïmplementeerd in een stempel of rij met revisie 4, raadpleegt u het artikel [Azure Hana-besturings element voor grote instanties via Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). In het overzicht kenmerken van uw grote exemplaar-eenheid HANA, kunt u ook de naam van de locatie van de Proximity-plaatsings groep bepalen, omdat deze is gemaakt tijdens de implementatie voor uw HANA grote exemplaar-eenheid. De naam die wordt weer gegeven in het kenmerk overzicht, is de naam van de plaatsings groep voor proximity. u moet gebruiken om uw Application Layer-Vm's in te implementeren.

In tegens telling tot SAP-systemen die alleen gebruikmaken van virtuele machines van Azure, wordt beslist hoeveel [Azure-resource groepen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) er moeten worden gebruikt, van u tot een diploma van de grootte van Hana grote instanties verwijderd. Alle HANA grote exemplaar eenheden van een een [Hana grote instantie-Tenant](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) worden gegroepeerd in één Azure-resource groep, zoals [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)wordt beschreven. Tenzij u een implementatie in verschillende tenants wilt scheiden, bijvoorbeeld productie en niet-productie of bepaalde systemen, worden alle HANA grote instantie-eenheden in één HANA-Tenant voor grote instanties geïmplementeerd, die opnieuw een 1:1-relatie heeft met een Azure resource groep. Dat voor alle afzonderlijke eenheden een afzonderlijke proximity-plaatsings groep is gedefinieerd. 

Als gevolg hiervan ziet de groepering tussen Azure-resource groepen en proximity-plaatsings groepen voor één Tenant er als volgt uit:

![Proximity-plaatsings groepen voor alle virtuele Azure-machines](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Kort voor beeld van de implementatie met Azure proximity placement groups
Om te laten zien hoe u Azure proximity placement Groups kunt gebruiken om uw virtuele machine te implementeren, vindt u hier een lijst met Power shell-opdrachten voor een eerste oefening met Azure proximity placement groups.

De eerste stap nadat u zich hebt aangemeld met uw [Azure Cloud shell](https://azure.microsoft.com/features/cloud-shell/) is te controleren of u zich in het juiste Azure-abonnement bevindt dat u wilt gebruiken voor de implementatie met de opdracht:

<pre><code>
Get-AzureRmContext
</code></pre>

Als u naar een ander abonnement moet overschakelen, kunt u dit doen door de volgende opdracht uit te voeren:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Als derde stap wilt u een nieuwe Azure-resource groep maken met deze opdracht:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

U kunt nu de nieuwe proximity-plaatsings groep maken met:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

U kunt nu beginnen met het implementeren van uw eerste VM in deze proximity-plaatsings groep met een opdracht als:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Met de bovenstaande opdracht wordt een op Windows gebaseerde VM geïmplementeerd. Nadat deze VM-implementatie is voltooid, wordt het Data Center-bereik van de Proximity-plaatsings groep gedefinieerd in de Azure-regio. Alle volgende VM-implementaties die verwijzen naar de locatie van nabijheid in de laatste opdracht, worden geïmplementeerd in hetzelfde Azure-Data Center, zolang het VM-type kan worden gehost op hardware die in dat Data Center is geplaatst en/of capaciteit voor dat VM-type beschikbaar is.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Beschikbaarheids sets en Beschikbaarheidszones combi neren met proximity-plaatsings groepen 
Met Beschikbaarheidszones voor SAP-systeem implementaties is een van de nadelen het feit dat de SAP-toepassingslaag niet kan worden beheerd met behulp van beschikbaarheids sets in de specifieke zone. Omdat u de SAP-toepassingslaag wilt implementeren in dezelfde zones als de DBMS-laag en het verwijzen naar een beschikbaarheids zone en een beschikbaarheidsset bij het implementeren van één virtuele machine niet wordt ondersteund, bent u gedwongen uw toepassingslaag te implementeren door te verwijzen naar een zone en de mogelijkheid om ervoor te zorgen dat de Application Layer-Vm's zijn verdeeld over verschillende update-en fout domeinen. Met behulp van proximity-plaatsings groepen kunt u deze beperking oplossen. De volg orde van de implementaties ziet er als volgt uit:

- Een nabijheidsplaatsingsgroep maken
- Implementeer uw ' anchor VM ', meestal de DBMS-server door te verwijzen naar een bepaalde Azure-beschikbaarheids zone
- Een beschikbaarheidsset maken die verwijst naar de buurt van de Azure-groep (zie hieronder)
- Implementeer de Application Layer-Vm's door te verwijzen naar de beschikbaarheidsset en de plaatsings groep voor nabijheid

In plaats van de eerste virtuele machine te implementeren zoals hierboven wordt beschreven, verwijst u naar een Azure-beschikbaarheids zone en de locatie van de Proximity-groep bij het implementeren van de VM, zoals:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Een geslaagde implementatie van deze virtuele machine die als host optreedt voor het data base-exemplaar van mijn SAP-systeem in één Azure-beschikbaarheids zone, het bereik van de plaatsings groep voor proximity is vast in een van de data centers die de beschikbaarheids zone vertegenwoordigen die u hebt gedefinieerd .

We gaan ervan uit dat u de Vm's van de centrale Services op dezelfde manier als de DBMs-Vm's implementeert door te verwijzen naar dezelfde zone (s) als voor de DBMS-Vm's en dezelfde proximity-plaatsings groepen. In de volgende stap moet u de beschikbaarheidsset (en) maken die u wilt gebruiken voor de toepassingslaag van uw SAP-systeem.
De plaatsings groep voor proximity moet worden gedefinieerd en gemaakt. Voor de opdracht voor het maken van de beschikbaarheidsset is een aanvullende verwijzing naar de groeps-ID voor proximity-plaatsing (niet de naam) vereist. U kunt de ID van de plaatsings groep voor proximity ophalen met:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Wanneer u de beschikbaarheidsset maakt, moet u extra para meters overwegen wanneer u beheerde schijven gebruikt (standaard tenzij anders opgegeven) en proximity placement groups:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

In het ideale geval moet u drie fout domeinen gebruiken. Het aantal ondersteunde fout domeinen kan echter variëren van regio tot regio. In dit geval is het maximum aantal fout domeinen dat mogelijk is voor de specifieke regio's twee. Voor het implementeren van uw Application Layer-Vm's moet u een verwijzing naar de naam van de beschikbaarheidsset en de naam van de locatie van de plaatsings groep toevoegen, zoals hier wordt getoond:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Het eind resultaat van deze reeks is een DBMS-laag en centrale services van uw SAP-systeem dat zich bevindt in een specifieke beschikbaarheids zone (s) en een SAP-toepassingslaag die zich bevindt in beschikbaarheids sets in dezelfde Azure-Data Centers als de DBMS-VM ('s) die is ontvangen geïmplementeerd.

> [!NOTE]
> Wanneer u één DBMS-VM implementeert in één zone en de tweede DBMS-VM in een andere zone om een configuratie met een hoge Beschik baarheid te maken, moet u verschillende proximity-plaatsings groepen voor elk van de zones vereisen. Dezelfde si-waarde voor de beschikbaarheidsset die u kunt gebruiken

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Een bestaand systeem ophalen in azure proximity-plaatsings groepen
Als u SAP-systemen al hebt geïmplementeerd, wilt u mogelijk de netwerk latentie van sommige van uw kritieke systemen optimaliseren en de toepassingslaag en de DBMS-laag in hetzelfde Data Center vinden. In het stadium van een open bare preview-functie van proximity, is het verwijderen van de virtuele machines en het maken van een nieuwe virtuele machine nood zakelijk om een dergelijke overstap naar proximity-plaatsings groepen uit te voeren. In deze fase van de functionaliteit is het niet voldoende genoeg om de Vm's uit te scha kelen zodat deze virtuele machines voor het afsluiten kunnen worden toegewezen aan proximity-plaatsings groepen.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de documentatie:

- [SAP-werk belasting op de Azure-planning en implementatie Controlelijst](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Voorbeeld: Vm's implementeren op proximity-plaatsings groepen met behulp van Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Voorbeeld: Vm's implementeren op proximity-plaatsings groepen met Power shell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

