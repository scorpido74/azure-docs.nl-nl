---
title: Kostenanalyse en budget - Azure Batch
description: Meer informatie over het uitvoeren van een kostenanalyse en het instellen van een budget voor de onderliggende compute resources en softwarelicenties die worden gebruikt om uw Batch-workloads uit te voeren.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022712"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kostenanalyse en budgetten voor Azure Batch

Er zijn geen kosten verbonden aan Azure Batch zelf, alleen de onderliggende compute resources en softwarelicenties die worden gebruikt om Batch-workloads uit te voeren. Op een hoog niveau worden kosten gemaakt van virtuele machines (VM's) in een pool, gegevensoverdracht van de VM of invoer- of uitvoergegevens die in de cloud zijn opgeslagen. Laten we eens kijken naar enkele belangrijke onderdelen van Batch om te begrijpen waar de kosten vandaan komen, hoe u een budget instelt voor een pool of account en enkele technieken om uw Batch-workloads kostenefficiënter te maken.

## <a name="batch-resources"></a>Batchbronnen

Virtuele machines zijn de belangrijkste bron die wordt gebruikt voor batchverwerking. De kosten voor het gebruik van VM's voor batch worden berekend op basis van het type, de hoeveelheid en de gebruiksduur. VM-factureringsopties omvatten [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of [reservering](../cost-management-billing/reservations/save-compute-costs-reservations.md) (vooraf betalen). Beide betalingsopties hebben verschillende voordelen, afhankelijk van uw rekenwerkbelasting, en beide betalingsmodellen hebben een andere invloed op uw factuur.

Wanneer toepassingen worden geïmplementeerd in Batch-knooppunten (VM's) met [toepassingspakketten,](batch-application-packages.md)worden er kosten in rekening gebracht voor de Azure Storage-bronnen die uw toepassingspakketten verbruiken. U wordt ook gefactureerd voor de opslag van invoer- of uitvoerbestanden, zoals bronbestanden en andere logboekgegevens. Over het algemeen zijn de kosten van opslaggegevens die aan Batch zijn gekoppeld veel lager dan de kosten van rekenresources. Elke vm in een groep die is gemaakt met **VirtualMachineConfiguration** heeft een bijbehorende OS-schijf die door Azure beheerde schijven gebruikt. Door Azure beheerde schijven hebben extra kosten en andere schijfprestatielagen hebben ook verschillende kosten.

Batchgroepen maken gebruik van netwerkbronnen. In het bijzonder worden voor **VirtualMachineConfiguration-pools** standaard load balancers gebruikt, waarvoor statische IP-adressen nodig zijn. De load balancers die door Batch worden gebruikt, zijn zichtbaar voor **gebruikersabonnementsaccounts,** maar zijn niet zichtbaar voor **Batch Service-accounts.** Standaardloadsenmaken kosten voor alle gegevens die van en naar batchgroepVM's worden doorgegeven; selecteer Batch-API's die gegevens ophalen uit poolknooppunten (zoals Taak/knooppuntbestand ophalen), taaktoepassingspakketten, bron-/uitvoerbestanden en containerafbeeldingen worden in rekening gebracht.

### <a name="additional-services"></a>Extra services

Services die vm's en opslag niet bevatten, kunnen rekening houden met de kosten van uw Batch-account.

Andere services die vaak met Batch worden gebruikt, kunnen het volgende omvatten:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- VM's met grafische toepassingen

Afhankelijk van welke services u met uw Batch-oplossing gebruikt, u extra kosten in rekening brengen. Raadpleeg de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van elke extra service te bepalen.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kostenanalyse en budget voor een pool

Via de Azure-portal u budgetten en uitgavenwaarschuwingen maken voor uw Batch-groep(en) of Batch-account. Budgetten en waarschuwingen zijn nuttig om belanghebbenden op de hoogte te stellen van eventuele risico's van overbesteding. Het is mogelijk dat er een vertraging in de uitgaven waarschuwingen en iets hoger zijn dan een budget. In dit voorbeeld bekijken we de kostenanalyse van een afzonderlijke batchgroep.

1. Selecteer **kostenbeheer + facturering in** de Azure-portal op de linkernavigatiebalk.
1. Selecteer uw abonnement in de sectie **Mijn abonnementen**
1. Ga naar **Kostenanalyse** onder de sectie **Kostenbeheer** van de linkernavigatiebalk, waarin een weergave als volgt wordt weergegeven:
1. Selecteer **Filter toevoegen**. Selecteer resourceselecteren in de eerste vervolgkeuzelijst **Het** ![resourcefilter selecteren](./media/batch-budget/resource-filter.png)
1. Selecteer in de tweede vervolgkeuzelijst de batchgroep. Wanneer de groep is geselecteerd, ziet de kostenanalyse er ongeveer gelijk uit aan de volgende analyse.
    ![Kostenanalyse van een pool](./media/batch-budget/pool-cost-analysis.png)

De resulterende kostenanalyse toont de kosten van de pool en de resources die bijdragen aan deze kosten. In dit voorbeeld zijn de VM's die in de groep worden gebruikt de duurste bron.

Als u een budget voor de groep wilt maken, selecteert u **Budget: geen**en selecteert u **Vervolgens Nieuw budget maken >**. Gebruik nu het venster om een budget specifiek voor uw pool te configureren.

Zie [Azure-budgetten maken en beheren](../cost-management-billing/costs/tutorial-acm-create-budgets.md)voor meer informatie over het configureren van een budget.

> [!NOTE]
> Azure Batch is gebouwd op Azure Cloud Services en Azure Virtual Machines-technologie. Wanneer u **cloudservicesconfiguratie kiest,** worden er kosten in rekening gebracht op basis van de prijsstructuur van Cloud Services. Wanneer u **Virtual Machine Configuration**kiest, worden er kosten in rekening gebracht op basis van de prijsstructuur voor virtuele machines. Het voorbeeld op deze pagina maakt gebruik van de **virtuele machineconfiguratie**.

## <a name="minimize-cost"></a>Kosten minimaliseren

Het gebruik van meerdere VM's en Azure-services voor langere tijd kan kostbaar zijn. Gelukkig zijn er diensten beschikbaar om uw uitgaven te verminderen, evenals strategieën voor het maximaliseren van de efficiëntie van uw werklast.

### <a name="low-priority-virtual-machines"></a>Virtuele machines met lage prioriteit

Vm's met lage prioriteit verlagen de kosten van Batch-workloads door gebruik te maken van overtollige computercapaciteit in Azure. Wanneer u VM's met lage prioriteit opgeeft in uw groepen, gebruikt Batch dit overschot om uw werkbelasting uit te voeren. Er is een aanzienlijke kostenbesparing door gebruik te maken van vm's met lage prioriteit in plaats van speciale VM's.

Meer informatie over het instellen van VM's met lage prioriteit voor uw werkbelasting bij [Vm's met lage prioriteit gebruiken met Batch.](batch-low-pri-vms.md)

### <a name="virtual-machine-os-disk-type"></a>Schijftype virtuele machine-besturingssysteem

Er zijn meerdere [VM OS-schijftypen](../virtual-machines/windows/disks-types.md). De meeste VM-series hebben formaten die zowel premium als standaard opslag ondersteunen. Wanneer een VM-grootte 's' is geselecteerd voor een groep, configureert Batch premium SSD OS-schijven. Wanneer de 'non-s' VM-grootte is geselecteerd, wordt het goedkopere, standaard SCHIJFtype hdd gebruikt. Bijvoorbeeld, premium SSD OS schijven `Standard_D2s_v3` worden gebruikt voor en `Standard_D2_v3`standaard HDD OS schijven worden gebruikt voor .

Premium SSD OS schijven zijn duurder, maar hebben hogere prestaties en VM's met premium schijven kunnen iets sneller beginnen dan VM's met standaard HDD OS-schijven. Met Batch wordt de OS-schijf vaak niet veel gebruikt omdat de toepassingen en taakbestanden zich op de tijdelijke SSD-schijf van DE's bevinden. Daarom is het in veel gevallen niet nodig om de hogere kosten te betalen voor de premium SSD die wordt ingericht wanneer een VM-grootte van 's' wordt opgegeven.

### <a name="reserved-virtual-machine-instances"></a>Gereserveerde virtuele machine-exemplaren

Als u Batch voor een langere periode wilt gebruiken, u besparen op de kosten van VM's door [Azure-reserveringen](../cost-management-billing/reservations/save-compute-costs-reservations.md) te gebruiken voor uw workloads. Een reserveringstarief is aanzienlijk lager dan een pay-as-you-go tarief. Virtuele machine-exemplaren die zonder reservering worden gebruikt, worden in rekening gebracht tegen een pay-as-you-go-tarief. Als u een reservering koopt, wordt de reserveringskorting toegepast en worden er geen kosten meer in rekening gebracht tegen de betalen per gebruik-tarieven.

### <a name="automatic-scaling"></a>Automatische schaalaanpassing

[Automatisch schalen](batch-automatic-scaling.md) schaalt dynamisch het aantal VM's in uw batchgroep op basis van de vereisten van de huidige taak. Door de groep te schalen op basis van de levensduur van een taak, zorgt automatisch schalen ervoor dat VM's worden opgeschaald en alleen worden gebruikt wanneer er een taak moet worden uitgevoerd. Wanneer de taak is voltooid of er geen taken zijn, worden de VM's automatisch verkleind om rekenresources op te slaan. Met Schalen u de totale kosten van uw Batch-oplossing verlagen door alleen de resources te gebruiken die u nodig hebt.

Zie Compute nodes automatisch [schalen in een Azure Batch-groep](batch-automatic-scaling.md)voor meer informatie over automatisch schalen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Batch API's en tools](batch-apis-tools.md) die beschikbaar zijn voor het bouwen en bewaken van Batch-oplossingen.  

- Meer informatie over [vm's met lage prioriteit met Batch](batch-low-pri-vms.md).
