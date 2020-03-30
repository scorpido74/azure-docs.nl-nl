---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128508"
---
## <a name="limitations"></a>Beperkingen

- Virtuele machineschaalsets worden momenteel niet ondersteund op speciale hosts.

## <a name="benefits"></a>Voordelen 

Het reserveren van de gehele host biedt de volgende voordelen:

-   Hardwareisolatie op fysiek serverniveau. Er worden geen andere VM's op uw hosts geplaatst. Speciale hosts worden geïmplementeerd in dezelfde datacenters en delen dezelfde netwerk- en onderliggende opslaginfrastructuur als andere, niet-geïsoleerde hosts.
-   Beheer over onderhoudsgebeurtenissen die zijn geïnitieerd door het Azure-platform. Hoewel de meeste onderhoudsgebeurtenissen weinig tot geen invloed hebben op uw virtuele machines, zijn er enkele gevoelige workloads waar elke seconde pauze een impact kan hebben. Met speciale hosts u zich aanmelden voor een onderhoudsvenster om de impact op uw service te verminderen.
-   Met het hybride voordeel van Azure u uw eigen licenties voor Windows en SQL naar Azure brengen. Het gebruik van de hybride voordelen biedt u extra voordelen. Zie [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)voor meer informatie.


## <a name="groups-hosts-and-vms"></a>Groepen, hosts en VM's  

![Bekijk de nieuwe bronnen voor toegewijde hosts.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Een **hostgroep** is een bron die een verzameling toegewijde hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheidszone en voegt er hosts aan toe.

Een **host** is een bron die is toegewezen aan een fysieke server in een Azure-datacenter. De fysieke server wordt toegewezen wanneer de host wordt gemaakt. Er wordt een host gemaakt binnen een hostgroep. Een host heeft een SKU die beschrijft welke VM-formaten kunnen worden gemaakt. Elke host kan meerdere VM's hosten, van verschillende grootte, zolang ze uit dezelfde groottereeks zijn.

Wanneer u een VM maakt in Azure, u selecteren welke dedicated host u voor uw vm wilt gebruiken. U hebt volledige controle over welke VM's op uw hosts worden geplaatst.


## <a name="high-availability-considerations"></a>Overwegingen met hoge beschikbaarheid 

Voor hoge beschikbaarheid moet u meerdere VM's implementeren, verspreid over meerdere hosts (minimaal 2). Met Azure Dedicated Hosts hebt u verschillende opties om uw infrastructuur in te richten om de grenzen van uw foutisolatie vorm te geven.

### <a name="use-availability-zones-for-fault-isolation"></a>Beschikbaarheidszones gebruiken voor foutisolatie

Beschikbaarheidszones zijn unieke fysieke locaties binnen een Azure-gebied. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Er wordt een hostgroep gemaakt in één beschikbaarheidszone. Zodra alle hosts zijn gemaakt, worden ze in die zone geplaatst. Om een hoge beschikbaarheid tussen zones te bereiken, moet u meerdere hostgroepen (één per zone) maken en uw hosts dienovereenkomstig verspreiden.

Als u een hostgroep toewijst aan een beschikbaarheidszone, moeten alle VM's die op die host zijn gemaakt, in dezelfde zone worden gemaakt.

### <a name="use-fault-domains-for-fault-isolation"></a>Foutdomeinen gebruiken voor foutisolatie

Een host kan worden gemaakt in een specifiek foutdomein. Net als VM in een schaalset of beschikbaarheidsset worden hosts in verschillende foutdomeinen op verschillende fysieke racks in het datacenter geplaatst. Wanneer u een hostgroep maakt, moet u het aantal foutdomeinen opgeven. Wanneer u hosts binnen de hostgroep maakt, wijst u voor elke host het foutdomein toe. De VM's vereisen geen foutdomeintoewijzing.

Foutdomeinen zijn niet hetzelfde als collocatie. Het hebben van dezelfde fout domein voor twee hosts betekent niet dat ze in de nabijheid van elkaar.

Foutdomeinen worden beperkt tot de hostgroep. U mag geen veronderstelling maken over anti-affiniteit tussen twee hostgroepen (tenzij ze zich in verschillende beschikbaarheidszones bevinden).

VM's geïmplementeerd om hosts met verschillende foutdomeinen, hebben hun onderliggende managed disks services op meerdere opslagstempels, om de bescherming van de foutisolatie te verhogen.

### <a name="using-availability-zones-and-fault-domains"></a>Beschikbaarheidszones en foutdomeinen gebruiken

U beide mogelijkheden samen gebruiken om nog meer foutisolatie te bereiken. In dit geval geeft u de beschikbaarheidszone en het aantal foutdomeinen op voor elke hostgroep, wijst u een foutdomein toe aan elk van uw hosts in de groep en wijst u een beschikbaarheidszone toe aan elk van uw VM's

De voorbeeldsjabloon Resourcemanager [die hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) wordt gevonden, gebruikt zones en foutdomeinen om hosts te verspreiden voor maximale tolerantie in een regio.

## <a name="maintenance-control"></a>Onderhoudsbeheer

De infrastructuur die uw virtuele machines ondersteunt, kan af en toe worden bijgewerkt om de betrouwbaarheid, prestaties, beveiliging en nieuwe functies te verbeteren. Het Azure-platform probeert de impact van platformonderhoud waar mogelijk te minimaliseren, maar klanten met *onderhoudsgevoelige* workloads kunnen niet eens enkele seconden tolereren dat de VM moet worden bevroren of losgekoppeld voor onderhoud.

**Maintenance Control** biedt klanten de mogelijkheid om regelmatige platformupdates die zijn gepland op hun speciale hosts over te slaan en deze vervolgens toe te passen op het moment van hun keuze binnen een rollend venster van 35 dagen.

> [!NOTE]
>  Onderhoudscontrole is momenteel in openbare preview. Zie **Updates met onderhoudsbeheer beheren met [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) of [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** voor meer informatie.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

Zodra een dedicated host is ingericht, wijst Azure deze toe aan de fysieke server. Dit garandeert de beschikbaarheid van de capaciteit wanneer u uw VM moet inrichten. Azure gebruikt de volledige capaciteit in de regio (of zone) om een fysieke server voor uw host te kiezen. Het betekent ook dat klanten kunnen verwachten dat ze hun specifieke hostfootprint kunnen laten groeien zonder dat ze zich zorgen maken dat er geen ruimte meer is in het cluster.

## <a name="quotas"></a>Quota

Er is een standaardquotumlimiet van 3000 vCPU's voor dedicated hosts per regio. Het aantal hosts dat u implementeren, wordt echter ook beperkt door het quotum voor de vm-groottefamilie die voor de host wordt gebruikt. Een Betalen **per gebruik-abonnement** heeft bijvoorbeeld alleen een quotum van 10 vCPU's beschikbaar voor de Dsv3-groottereeks in de regio Oost-VS. In dit geval moet u een quotumverhoging aanvragen tot ten minste 64 vCPU's voordat u een dedicated host implementeren. Selecteer de knop **Verhoging aanvragen** in de rechterbovenhoek om indien nodig een verzoek in te dienen.

![Schermafbeelding van de pagina gebruik en quota in de portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Zie [VCPU-quota virtuele machine voor](/azure/virtual-machines/windows/quotas)meer informatie .

Gratis proefversie- en MSDN-abonnementen hebben geen quotum voor Azure Dedicated Hosts.

## <a name="pricing"></a>Prijzen

Gebruikers worden in rekening gebracht per dedicated host, ongeacht het aantal VM's dat wordt geïmplementeerd. In uw maandelijkse overzicht ziet u een nieuw factureerbaar resourcetype hosts. De VM's op een speciale host worden nog steeds weergegeven in uw verklaring, maar zullen een prijs van 0 dragen.

De hostprijs is ingesteld op basis van vm-familie, type (hardwaregrootte) en regio. Een hostprijs is relatief ten opzichte van de grootste VM-grootte die op de host wordt ondersteund.

Softwarelicenties, opslag en netwerkgebruik worden apart van de host en VM's gefactureerd. Er is geen wijziging in deze factureerbare items.

Zie [Azure Dedicated Host-prijzen](https://aka.ms/ADHPricing)voor meer informatie.

U ook kosten besparen met een [gereserveerdexemplaar van Azure Dedicated Hosts.](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
 
## <a name="sizes-and-hardware-generations"></a>Generaties van maten en hardware

Een SKU is gedefinieerd voor een host en vertegenwoordigt de vm-groottereeks en -type. U meerdere VM's van verschillende groottes mengen binnen één host, zolang ze van dezelfde groottereeks zijn. 

Het *type* is de hardware generatie. Verschillende hardwaretypen voor dezelfde VM-serie zijn afkomstig van verschillende CPU-leveranciers en hebben verschillende CPU-generaties en het aantal cores. 

De grootte en hardwaretypen verschillen per regio. Raadpleeg de [pagina hostprijzen](https://aka.ms/ADHPricing) voor meer informatie.


## <a name="host-life-cycle"></a>Gastlevenscyclus


Azure bewaakt en beheert de status van uw hosts. De volgende statussen worden geretourneerd wanneer u uw host opvraagt:

| Gezondheidstoestand   | Beschrijving       |
|----------|----------------|
| Host beschikbaar     | Er zijn geen bekende problemen met uw host.   |
| Gastheer in onderzoek  | We hebben wat problemen met de gastheer die we onderzoeken. Dit is een overgangsstatus die vereist is voor Azure om te proberen de scope en de hoofdoorzaak voor het geïdentificeerde probleem te identificeren. Virtuele machines die op de host draaien, kunnen worden beïnvloed. |
| Host in afwachting van de toewijzing   | Azure kan de host niet terugzetten naar een gezonde status en u vragen uw virtuele machines opnieuw uit deze host te implementeren. Als `autoReplaceOnFailure` deze is ingeschakeld, worden uw virtuele machines *door de service genezen van* gezonde hardware. Anders kan het zijn dat uw virtuele machine draait op een host die op het punt staat te mislukken.|
| Host deallocated  | Alle virtuele machines zijn uit de host verwijderd. U wordt niet langer in rekening gebracht voor deze host sinds de hardware uit de rotatie is gehaald.   |

