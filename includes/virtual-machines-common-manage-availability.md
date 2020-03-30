---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961546"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Informatie over het opnieuw opstarten van VM's - onderhoud versus downtime
Er zijn drie scenario's die ertoe kunnen leiden dat de virtuele machine in Azure wordt beïnvloed: ongepland hardwareonderhoud, onverwachte downtime en gepland onderhoud.

* **Gebeurtenis voor niet-gepland hardwareonderhoud** treedt op wanneer via het Azure-platform een fout wordt voorspeld op de hardware of in een platformonderdeel dat is gekoppeld aan een fysieke computer. Wanneer via het platform een fout wordt voorspeld, wordt een gebeurtenis voor niet-gepland hardwareonderhoud vrijgegeven om de impact op de virtuele machines die worden gehost op deze hardware, te beperken. Azure maakt gebruik [van Live Migration-technologie](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) om de virtuele machines te migreren van de falende hardware naar een gezonde fysieke machine. Livemigratie is een bewerking ter behoud van VM's waardoor de werking van een virtuele machine slechts korte tijd wordt onderbroken. Het geheugen, de geopende bestanden en de netwerkverbindingen blijven behouden, maar de prestaties vóór en/of na de gebeurtenis kunnen minder zijn. In gevallen waarbij livemigratie niet kan worden gebruikt, treedt er onverwachte downtime op de VM op, zoals hieronder wordt beschreven.


* **Een onverwachte downtime** is wanneer de hardware of de fysieke infrastructuur voor de virtuele machine onverwacht uitvalt. Dit kunnen lokale netwerkstoringen, lokale schijfstoringen of andere fouten op rackniveau zijn. Wanneer gedetecteerd, migreert het Azure-platform uw virtuele machine automatisch (geneest) naar een gezonde fysieke machine in hetzelfde datacenter. Tijdens deze procedure treedt downtime (opnieuw opstarten) op de virtuele machines op en in sommige gevallen gaat de tijdelijke schijf verloren. Het besturingssysteem en de gegevensschijven die zijn bijgevoegd, blijven altijd behouden.

  Virtuele machines kunnen ook downtime ervaren in het onwaarschijnlijke geval van een storing of ramp die een heel datacenter of zelfs een hele regio beïnvloedt. Voor deze scenario's biedt Azure beveiligingsopties, waaronder [beschikbaarheidszones](../articles/availability-zones/az-overview.md) en [gekoppelde regio's.](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)

* **Geplande onderhoudsgebeurtenissen** zijn periodieke updates die door Microsoft zijn uitgevoerd op het onderliggende Azure-platform om de algehele betrouwbaarheid, prestaties en beveiliging van de platforminfrastructuur waarop uw virtuele machines worden uitgevoerd, te verbeteren. Veel van deze updates worden uitgevoerd zonder dat dit van invloed is op uw virtuele machines of Cloud Services (zie [Onderhoud ter behoud van VM's](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Wanneer mogelijk maakt het Azure-platform gebruik van Onderhoud ter behoud van VM's. In zeldzame gevallen kan het echter noodzakelijk zijn om de virtuele machine opnieuw op te starten om de vereiste updates toe te passen op de onderliggende infrastructuur. In dit geval kunt u gepland onderhoud van Azure gebruiken met de bewerking Onderhoud-Opnieuw implementeren door het onderhoud voor de betrokken VM's te initiëren in het geschikte tijdvenster. Zie [Gepland onderhoud voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/) voor meer informatie.


Om de gevolgen van downtime vanwege een of meer van deze gebeurtenissen te beperken, raden we aan voor uw virtuele machines de volgende aanbevolen procedures voor hoge beschikbaarheid te volgen:

* [Configureer meerdere virtuele machines in een beschikbaarheidsset voor redundantie]
* [Beheerde schijven voor VM's in een beschikbaarheidsset gebruiken]
* [Geplande gebeurtenissen gebruiken om proactief te reageren op VM-impactgebeurtenissen](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configureer elke toepassingslaag in afzonderlijke beschikbaarheidssets]
* [Een load balancer combineren met beschikbaarheidssets]
* [Beschikbaarheidszones gebruiken om te beschermen tegen fouten op datacenterniveau]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Beschikbaarheidszones gebruiken om te beschermen tegen fouten op datacenterniveau

[Beschikbaarheidszones](../articles/availability-zones/az-overview.md) breiden het controleniveau uit dat u hebt om de beschikbaarheid van de toepassingen en gegevens op uw VM's te behouden. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om veerkracht te garanderen, zijn er in alle ingeschakelde regio's minimaal drie afzonderlijke zones. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zoneredundante services repliceren uw toepassingen en gegevens in beschikbaarheidszones om te beschermen tegen single-points-of-failure.

Een beschikbaarheidszone in een Azure-gebied is een combinatie van een **foutdomein** en een **updatedomein.** Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. Door uw oplossingen te ontwerpen voor het gebruik van gerepliceerde VM's in zones, u uw toepassingen en gegevens beschermen tegen het verlies van een datacenter. Als de ene zone is gecompromitteerd, zijn gerepliceerde apps en gegevens direct beschikbaar in een andere zone.

![Beschikbaarheidszones](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Meer informatie over het implementeren van een [Windows-](../articles/virtual-machines/windows/create-powershell-availability-zone.md) of [Linux-vm](../articles/virtual-machines/linux/create-cli-availability-zone.md) in een beschikbaarheidszone.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configureer meerdere virtuele machines in een beschikbaarheidsset voor redundantie
Beschikbaarheidssets zijn een andere datacenterconfiguratie om VM-redundantie en beschikbaarheid te bieden. Deze configuratie binnen een datacenter zorgt ervoor dat tijdens een geplande of ongeplande onderhoudsgebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de 99,95% Azure SLA. Zie de [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/) voor meer informatie.

> [!IMPORTANT]
> Een virtuele machine met één exemplaar in een beschikbaarheid die op zichzelf is ingesteld, moet Premium SSD of Ultra Disk gebruiken voor alle schijven en gegevensschijven van het besturingssysteem om in aanmerking te komen voor de SLA voor virtual machine-connectiviteit van ten minste 99,9%.

Elke virtuele machine in uw beschikbaarheidsset krijgt een **updatedomein** en een **foutdomein** toegewezen door het onderliggende Azure-platform. Voor iedere beschikbaarheidsset worden standaard vijf updatedomeinen toegewezen die niet door gebruiker te bewerken zijn (voor Resource Manager-implementaties kan dit aantal worden opgehoogd tot 20 updatedomeinen), om groepen virtuele machines en onderliggende fysieke hardware aan te duiden die op hetzelfde moment opnieuw kunnen worden opgestart. Wanneer in één beschikbaarheidsset meer dan vijf virtuele machines worden geconfigureerd, wordt de zesde virtuele machine in hetzelfde updatedomein geplaatst als de eerste virtuele machine, de zevende in hetzelfde updatedomein als de tweede virtuele machine, enzovoort. De volgorde waarin updatedomeinen opnieuw worden opgestart, verloopt tijdens gepland onderhoud niet altijd sequentieel, maar er wordt slechts één updatedomein tegelijk opnieuw opgestart. Een updatedomein dat opnieuw is opgestart, heeft 30 minuten om te herstellen voordat onderhoud wordt geïnitieerd op een ander updatedomein.

Foutdomeinen duiden de groep virtuele machines aan die een gemeenschappelijke voeding en switch delen. Standaard worden bij Resource Manager-implementaties de virtuele machines die zijn geconfigureerd in uw beschikbaarheidsset verdeeld over maximaal drie foutdomeinen (twee foutdomeinen bij klassieke implementaties). Hoewel het in een beschikbaarheidsset plaatsen van uw virtuele machines uw toepassing niet beschermt tegen problemen met het besturingssysteem of de toepassing zelf, worden zo wel de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen beperkt.

<!--Image reference-->
   ![Concepttekening van een configuratie met een updatedomein en een foutdomein](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Beheerde schijven voor VM's in een beschikbaarheidsset gebruiken
Als u momenteel VM's met niet-beheerde schijven gebruikt, raden wij u ten zeerste aan [VM's in een beschikbaarheidsset te converteren voor het gebruik van beheerde schijven](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Beheerde schijven](../articles/virtual-machines/windows/managed-disks-overview.md) bieden een betere betrouwbaarheid voor beschikbaarheidssets door ervoor te zorgen dat de schijven van VM's in een beschikbaarheidsset voldoende van elkaar zijn verwijderd, waardoor een SPOF (Single Point Of Failure) wordt voorkomen. Dit gebeurt door de schijven automatisch in verschillende opslagfoutdomeinen (opslagclusters) te plaatsen en ze uit te lijnen op het VM-foutdomein. Als een domein van een opslagfout uitvalt door hardware- of softwarefouten, mislukt alleen de VM-instantie met schijven op het domein van de opslagfout.
![Gemanaged schijven-ssd's](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Het aantal foutdomeinen voor beheerde beschikbaarheidssets varieert per regio: twee of drie per regio. U het foutdomein voor elke regio bekijken door de volgende scripts uit te voeren.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Onder bepaalde omstandigheden kunnen 2 VM's in dezelfde AvailabilitySet hetzelfde FaultDomain delen. Dit kan worden bevestigd door in te gaan op uw beschikbaarheidset en de kolom **Foutdomein te** controleren.
> Dit kan de oorzaak zijn van de volgende reeks tijdens het implementeren van de VM's:
> - De eerste VM implementeren
> - De eerste vm stoppen/detoewijzen
> - Implementeer de 2e VM Onder deze omstandigheden kan de OS-schijf van de 2e VM worden gemaakt op hetzelfde foutdomein als de 1e VM, zodat de 2e VM ook op hetzelfde FaultDomain terecht komt. 
> Om dit probleem te voorkomen, wordt aanbevolen om de VM's tussen implementaties niet te stoppen/detoewijzen.

Als u vm's met onbeheerde schijven wilt gebruiken, volgt u hieronder aanbevolen procedures voor opslagaccounts waarbij virtuele harde schijven (VHD's) van VM's worden opgeslagen als [paginablobs.](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)

1. **Zorg dat alle schijven (gegevens en besturingssysteem) worden gekoppeld aan een virtuele machine op hetzelfde opslagaccount**
2. **Bekijk de [limieten](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) voor het aantal niet-beheerde schijven in een Azure Storage-account** voordat u meer VHD's toevoegt aan een opslagaccount
3. **Gebruik een apart opslagaccount voor elke virtuele machine in een beschikbaarheidsset.** Deel opslagaccounts met meerdere VM's niet in dezelfde beschikbaarheidsset. Het is aanvaardbaar dat VM's in verschillende beschikbaarheidssets opslagaccounts ![delen als bovenstaande best practices worden gevolgd Onbeheerde schijven-ssd's](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Geplande gebeurtenissen gebruiken om proactief te reageren op VM-impactgebeurtenissen

Wanneer u zich abonneert op [geplande gebeurtenissen,](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)wordt uw VM op de hoogte gesteld van aankomende onderhoudsgebeurtenissen die van invloed kunnen zijn op uw VM. Wanneer geplande gebeurtenissen zijn ingeschakeld, krijgt uw virtuele machine een minimale hoeveelheid tijd voordat de onderhoudsactiviteit wordt uitgevoerd. Host OS-updates die van invloed kunnen zijn op uw VM staan bijvoorbeeld in de wachtrij als gebeurtenissen die de impact opgeven, evenals een tijdstip waarop het onderhoud wordt uitgevoerd als er geen actie wordt ondernomen. Gebeurtenissen in het schema staan ook in de wachtrij wanneer Azure een dreigende hardwarefout detecteert die van invloed kan zijn op uw VM, waarmee u beslissen wanneer de genezing moet worden uitgevoerd. Klanten kunnen de gebeurtenis gebruiken om taken voorafgaand aan het onderhoud uit te voeren, zoals de status opslaan, niet over naar de secundaire, enzovoort. Nadat u uw logica hebt voltooid voor het op een elegante manier afhandelen van de onderhoudsgebeurtenis, u de uitstaande geplande gebeurtenis goedkeuren zodat het platform door kan gaan met onderhoud.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Elke toepassingslaag configureren in afzonderlijke beschikbaarheidszones of beschikbaarheidssets
Als uw virtuele machines bijna identiek zijn en hetzelfde doel voor uw toepassing dienen, raden we u aan een beschikbaarheidszone of beschikbaarheidsset voor elke laag van uw toepassing te configureren.  Als u twee verschillende lagen in dezelfde beschikbaarheidszone of -set plaatst, kunnen alle virtuele machines in dezelfde toepassingslaag in één keer opnieuw worden opgestart. Door ten minste twee virtuele machines in een beschikbaarheidszone of -set voor elke laag te configureren, garandeert u dat er ten minste één virtuele machine in elke laag beschikbaar is.

U bijvoorbeeld alle virtuele machines in de voorkant van uw toepassing met IIS, Apache en Nginx in één beschikbaarheidszone of -set plaatsen. Zorg ervoor dat alleen front-end virtuele machines in dezelfde beschikbaarheidszone of -set worden geplaatst. Zorg er ook voor dat alleen virtuele machines op dataniveau in hun eigen beschikbaarheidszone of -set worden geplaatst, zoals uw gerepliceerde virtuele SQL Server-machines of uw Virtuele MySQL-machines.

<!--Image reference-->
   ![Toepassingslagen](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Een load balancer combineren met beschikbaarheidszones of -sets
Combineer de [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) met een beschikbaarheidszone of stel in op de meeste toepassingstolerantie. De Azure Load Balancer verdeelt het verkeer tussen meerdere virtuele machines. De Azure Load Balancer is voor virtuele machines uit de prijscategorie Standard bij de prijs inbegrepen. De Azure Load Balancer is niet bij alle prijscategorieën voor virtuele machines inbegrepen. Zie voor meer informatie over het gebruik van load balancers voor uw virtuele machines [Taakverdeling voor virtuele machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Als de load balancer niet is geconfigureerd om het verkeer te verdelen over meerdere virtuele machines, heeft iedere geplande onderhoudsgebeurtenis invloed op de enkele virtuele machine die uw verkeer afhandelt, waardoor uw applicatielaag onbeschikbaar wordt. Door meerdere virtuele machines van dezelfde laag onder te brengen bij dezelfde load balancer en beschikbaarheidsset, zorgt u ervoor dat verkeer altijd door ten minste één instantie kan worden afgehandeld.

Zie [Vm's voor laadbalans in alle beschikbaarheidszones met behulp van de Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md)voor een zelfstudie over het laden van saldo-VM's voor alle beschikbaarheidszones.


<!-- Link references -->
[Configureer meerdere virtuele machines in een beschikbaarheidsset voor redundantie]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configureer elke toepassingslaag in afzonderlijke beschikbaarheidssets]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Een load balancer combineren met beschikbaarheidssets]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Beheerde schijven voor VM's in een beschikbaarheidsset gebruiken]: #use-managed-disks-for-vms-in-an-availability-set
[Beschikbaarheidszones gebruiken om te beschermen tegen fouten op datacenterniveau]: #use-availability-zones-to-protect-from-datacenter-level-failures
