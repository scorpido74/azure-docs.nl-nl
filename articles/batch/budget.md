---
title: Kosten analyse en budget
description: Meer informatie over het ophalen van een kosten analyse en het instellen van een budget voor de onderliggende reken resources en software licenties die worden gebruikt voor het uitvoeren van uw batch-workloads.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 13c8cc508a4940b5e21570104527c40988879919
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725768"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kosten analyse en budgetten voor Azure Batch

Er zijn geen kosten verbonden aan Azure Batch, alleen de onderliggende reken resources en software licenties die worden gebruikt voor het uitvoeren van batch-workloads. Op hoog niveau worden de kosten berekend op basis van virtuele machines (Vm's) in een pool, gegevens overdracht van de virtuele machine of een invoer-of uitvoer gegevens die in de Cloud zijn opgeslagen. Laten we eens kijken naar enkele belang rijke onderdelen van batch om inzicht te krijgen in de kosten van, het instellen van een budget voor een groep of account en enkele technieken om uw batch-workloads rendabeler te maken.

## <a name="batch-resources"></a>Batch-resources

Virtuele machines zijn de belangrijkste bronnen die worden gebruikt voor batch verwerking. De kosten voor het gebruik van Vm's voor batch worden berekend op basis van het type, de hoeveelheid en de duur van het gebruik. De facturerings opties voor de virtuele machine omvatten [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of [reserve ring](../cost-management-billing/reservations/save-compute-costs-reservations.md) (vooraf betalen). Beide betalings opties hebben verschillende voor delen, afhankelijk van de reken werk belasting en beide betalings modellen beïnvloeden uw factuur anders.

Wanneer toepassingen worden geïmplementeerd op batch-knoop punten (Vm's) met behulp van [toepassings pakketten](batch-application-packages.md), worden er kosten in rekening gebracht voor de Azure storage resources die door uw toepassing worden gebruikt. U wordt ook gefactureerd voor de opslag van invoer-of uitvoer bestanden, zoals bron bestanden en andere logboek gegevens. Over het algemeen zijn de kosten voor opslag gegevens die aan de batch zijn gekoppeld, veel lager dan de kosten van reken resources. Elke VM in een groep die is gemaakt met **VirtualMachineConfiguration** heeft een gekoppelde besturingssysteem schijf die gebruikmaakt van door Azure beheerde schijven. Azure-beheerde schijven hebben extra kosten en andere schijf prestatie lagen hebben ook verschillende kosten.

Batch-Pools gebruiken netwerk bronnen. Met name voor **VirtualMachineConfiguration** -Pools worden standaard load balancers gebruikt, waarvoor statische IP-adressen zijn vereist. De load balancers die door batch worden gebruikt, zijn zichtbaar voor accounts voor **gebruikers abonnementen** , maar zijn niet zichtbaar voor **batch-service** accounts. Voor de standaard load balancers worden kosten in rekening gebracht voor alle gegevens die worden door gegeven aan en van Vm's uit de batch-groep; Selecteer Batch-Api's voor het ophalen van gegevens uit pool knooppunten (zoals taak/knooppunt bestand ophalen), taak toepassings pakketten, resource/uitvoer bestanden en container installatie kopieën.

### <a name="additional-services"></a>Extra services

Services met inbegrip van Vm's en opslag kunnen de kosten van uw batch-account in rekening worden.

Andere services die vaak worden gebruikt in combi natie met batch kunnen het volgende omvatten:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Vm's met grafische toepassingen

Afhankelijk van de services die u met uw batch-oplossing gebruikt, kunnen er extra kosten in rekening worden gebracht. Raadpleeg de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van elke extra service te bepalen.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kosten analyse en budget voor een pool

Via de Azure Portal kunt u budgetten en uitgaven voor uw batch-pool (s) of batch-account maken. Budgetten en waarschuwingen zijn handig voor het melden van belanghebbenden van eventuele Risico's van overuitgave. Het is mogelijk dat er een vertraging optreedt in bestedings waarschuwingen en dat er iets meer is dan een budget. In dit voor beeld wordt de kosten analyse van een afzonderlijke batch-pool weer gegeven.

1. Selecteer **Cost Management + facturering** in de linkernavigatiebalk in het Azure Portal.
1. Selecteer uw abonnement in de sectie **Mijn abonnementen**
1. Ga naar **kosten analyse** in het gedeelte **Cost Management** van de navigatie balk aan de linkerkant. dit ziet er als volgt uit:
1. Selecteer **filter toevoegen**. Selecteer in de eerste vervolg keuzelijst **resource** ![ de resource filter selecteren](./media/batch-budget/resource-filter.png)
1. Selecteer in de tweede vervolg keuzelijst de batch-pool. Wanneer de pool is geselecteerd, ziet de kosten analyse er ongeveer uit als de volgende analyse.
    ![Kosten analyse van een pool](./media/batch-budget/pool-cost-analysis.png)

In de analyse van de resulterende kosten worden de kosten van de pool weer gegeven, evenals de resources die bijdragen aan deze kosten. In dit voor beeld zijn de virtuele machines die in de pool worden gebruikt de meest dure resource.

Als u een budget voor de pool wilt maken, selecteert u **budget: geen**en selecteert u **nieuwe budget >maken **. Gebruik nu het venster om een specifiek budget voor uw groep te configureren.

Zie [Azure-budgetten maken en beheren](../cost-management-billing/costs/tutorial-acm-create-budgets.md)voor meer informatie over het configureren van een budget.

> [!NOTE]
> Azure Batch is gebaseerd op Azure Cloud Services en Azure Virtual Machines technologie. Wanneer u **Cloud Services configuratie**kiest, worden kosten in rekening gebracht op basis van de Cloud Services prijs structuur. Wanneer u de **configuratie van de virtuele machine**kiest, worden kosten in rekening gebracht op basis van de virtual machines prijs structuur. In het voor beeld op deze pagina wordt de configuratie van de **virtuele machine**gebruikt.

## <a name="minimize-cost"></a>Kosten minimaliseren

Het gebruik van verschillende Vm's en Azure-Services voor langere Peri Oden kan kostbaar zijn. Gelukkig zijn er services beschikbaar waarmee u uw uitgaven kunt verminderen, evenals strategieën voor het maximaliseren van de efficiëntie van uw werk belasting.

### <a name="low-priority-virtual-machines"></a>Virtuele machines met lage prioriteit

Virtuele machines met lage prioriteit verlagen de kosten van batch-workloads door gebruik te maken van de capaciteit van de overschot op Azure. Wanneer u virtuele machines met lage prioriteit in uw Pools opgeeft, gebruikt batch dit surplus om uw werk belasting uit te voeren. Er is een aanzienlijke kosten besparing door gebruik te maken van Vm's met lage prioriteit in plaats van toegewezen Vm's.

Meer informatie over het instellen van Vm's met lage prioriteit voor uw workload bij [gebruik van vm's met lage prioriteit met batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Schijf type van het besturings systeem van de virtuele machine

Er zijn meerdere [schijf typen voor VM-besturings systemen](../virtual-machines/windows/disks-types.md). De meeste VM-Series hebben grootten die ondersteuning bieden voor zowel Premium als standaard opslag. Wanneer de VM-grootte voor een groep is geselecteerd, worden met de batch Premium SSD-besturingssysteem schijven geconfigureerd. Wanneer de VM-grootte ' niet-s ' is geselecteerd, wordt het type goed koper, standaard HDD-schijf gebruikt. Premium SSD-besturingssysteem schijven worden bijvoorbeeld gebruikt voor `Standard_D2s_v3` en standaard schijven voor harde schijven worden gebruikt voor `Standard_D2_v3` .

Premium-SSD-schijven van het besturings systeem zijn duurder, maar ze hebben hogere prestaties en virtuele machines met Premium-schijven kunnen iets sneller beginnen dan Vm's met standaard schijven voor harde schijven. Met batch wordt de besturingssysteem schijf vaak niet gebruikt, omdat de toepassingen en taak bestanden zich op de tijdelijke schijf van de virtuele machine bevinden. In veel gevallen is het niet nodig om de verhoogde kosten te betalen voor de Premium-SSD die is ingericht wanneer de VM-grootte van een is opgegeven.

### <a name="reserved-virtual-machine-instances"></a>Gereserveerde exemplaren van virtuele machines

Als u batch wilt gebruiken gedurende een lange periode, kunt u besparen op de kosten van Vm's met behulp van [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) voor uw workloads. Een reserverings tarief is aanzienlijk lager dan het tarief voor betalen naar gebruik. Voor exemplaren van virtuele machines die worden gebruikt zonder een reserve ring, wordt het tarief voor betalen naar gebruik in rekening gebracht. Als u een reserve ring aanschaft, wordt de reserverings korting toegepast en worden er geen kosten meer berekend op basis van de betalen naar gebruik-tarieven.

### <a name="automatic-scaling"></a>Automatische schaalaanpassing

Met [automatisch schalen](batch-automatic-scaling.md) wordt het aantal vm's in de batch-pool dynamisch geschaald op basis van de vereisten van de huidige taak. Door de pool te schalen op basis van de levens duur van een taak, zorgt u er automatisch voor dat Vm's alleen omhoog worden geschaald en gebruikt wanneer er een taak wordt uitgevoerd. Wanneer de taak is voltooid of als er geen taken zijn, worden de Vm's automatisch omlaag geschaald om reken resources op te slaan. Met schalen kunt u de totale kosten van uw batch-oplossing verlagen door alleen de benodigde resources te gebruiken.

Zie [reken knooppunten automatisch schalen in een Azure batch groep](batch-automatic-scaling.md)voor meer informatie over automatisch schalen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [batch-api's en hulpprogram ma's](batch-apis-tools.md) die beschikbaar zijn voor het maken en bewaken van batch-oplossingen.  

- Meer informatie over [virtuele machines met lage prioriteit met batch](batch-low-pri-vms.md).
