---
title: Service kosten verminderen met behulp van Azure Advisor
description: Gebruik Azure Advisor om de kosten van uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658574"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Service kosten verminderen met behulp van Azure Advisor

Advisor helpt u uw totale Azure-uitgaven te optimaliseren en te verlagen door inactieve en gelaagde bronnen te identificeren.U kunt aanbevelingen ontvangen op het tabblad **kosten** van het Advisor-dash board.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Uitgaven aan virtuele machines optimaliseren door de grootte ervan te wijzigen of door onderbenutte instanties af te sluiten 

Hoewel bepaalde toepassings scenario's kunnen leiden tot een laag gebruik door ontwerp, bespaart u vaak geld door de grootte en het aantal van uw virtuele machines te beheren. De geavanceerde evaluatie modellen van Advisor hebben betrekking op virtuele machines die worden afgesloten wanneer P95th van het maximum van de maximale waarde van CPU-gebruik minder dan 3% is en het netwerk gebruik minder dan 2% is gedurende een periode van 7 dagen. Virtuele machines worden beschouwd als een juiste grootte wanneer het mogelijk is om de huidige belasting in een kleinere SKU (binnen dezelfde SKU-familie) te passen, of een kleiner aantal exemplaren, zodat de huidige belasting niet meer dan 80% verbruikt wanneer niet-gebruikers gerichte workloads en niet meer dan 40% bij een gebruikers gerichte werk belasting. Hier wordt het type werk belasting bepaald door de kenmerken van het CPU-gebruik van de werk belasting te analyseren.

De aanbevolen acties zijn uitgeschakeld of worden verg root of verkleind, wat specifiek is voor de resource die wordt aanbevolen voor. Advisor toont u de geschatte kosten besparingen voor de aanbevolen acties/formaat wijzigen of afsluiten. Voor de aanbevolen actie voor verg Roten/verkleinen biedt Advisor ook huidige en doel-SKU-gegevens. 

Als u meer wilt weten over het identificeren van gefactureerde virtuele machines, kunt u de regel voor het CPU-gebruik aanpassen op basis van een abonnement.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>De uitgaven voor MariaDB-, MySQL-en PostgreSQL-servers optimaliseren door rechts te wijzigen 
Advisor analyseert uw gebruik en beveelt aan of uw MariaDB/MySQL/PostgreSQL-database server bronnen gedurende een lange periode in de afgelopen 7 dagen zijn ondergebruikt. Weinig resourcegebruik resulteert in ongewenste uitgaven die kunnen worden gecorrigeerd zonder aanzienlijke invloed op de prestaties. Om uw kosten te verlagen en uw resources efficiënt te beheren, kunt u het beste de rekengrootte (vCores) met de helft verlagen.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Kosten verlagen door oningerichte ExpressRoute-circuits te elimineren

Advisor identificeert ExpressRoute-circuits die de provider status hebben van *niet* meer dan één maand, en raadt u aan het circuit te verwijderen als u niet van plan bent om het circuit in te richten met uw connectiviteits provider.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Kosten verlagen door niet-actieve virtuele netwerkgateways te verwijderen of opnieuw te configureren

Advisor identificeert virtuele netwerk gateways die gedurende meer dan 90 dagen inactief zijn geweest. Omdat deze gateways per uur worden gefactureerd, moet u overwegen om deze te configureren of te verwijderen als u deze niet meer wilt gebruiken. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Gereserveerde instanties van virtuele machines kopen om kosten te besparen ten opzichte van betalen per gebruik

Advisor controleert uw gebruik van de virtuele machine in de afgelopen 30 dagen en bepaalt of u geld kunt besparen door een Azure-reserve ring aan te schaffen. Advisor toont u de regio's en grootten waar u mogelijk de meeste besparingen hebt en toont u de geschatte besparingen bij het aanschaffen van reserve ringen. Met Azure-reserve ringen kunt u de basis kosten voor uw virtuele machines vooraf aanschaffen. Kortingen worden automatisch toegepast op nieuwe of bestaande virtuele machines met dezelfde grootte en regio als uw reserve ringen. [Meer informatie over Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor waarschuwt u ook voor gereserveerde instanties die u in de komende 30 dagen verloopt. U wordt aangeraden nieuwe gereserveerde instanties te kopen om te voor komen dat betalen per gebruik wordt betaald.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Gereserveerde instanties voor verschillende resource typen kopen om te besparen op uw betalen per gebruik-kosten

We analyseren het gebruiks patroon voor de onderstaande lijst met resources, in de afgelopen 30 dagen en raden een aankoop van gereserveerde capaciteit aan om uw besparingen te maximaliseren. 
### <a name="cosmos-db-reserved-capacity"></a>Gereserveerde Cosmos DB-capaciteit
We hebben uw gebruikspatroon van Cosmos DB gedurende de afgelopen 30 dagen geanalyseerd en u kunt het beste een gereserveerde capaciteit kopen om de kosten te optimaliseren. Met gereserveerde capaciteit kunt u het uurlijkse gebruik van Cosmos DB vooraf aanschaffen en bespaart u op de kosten voor betalen per gebruik. Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De besparingsschattingen worden berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en door het gebruikspatroon te extrapoleren dat in de afgelopen 30 dagen wordt geobserveerd. Aanbevelingen voor gedeelde reserveringsbereiken zijn beschikbaar in de ervaring voor reserveringsaankoop en kunnen u helpen nog meer te besparen.

### <a name="sql-paas-reserved-capacity"></a>Gereserveerde capaciteit voor SQL PaaS
We analyseren elastische pools voor SQL PaaS en het gebruikspatroon van het beheerde exemplaar over de afgelopen 30 dagen en raden u aan om een gereserveerde capaciteit aan te schaffen waarmee uw besparingen worden gemaximaliseerd. Met gereserveerde capaciteit kunt u het uurlijkse gebruik van SQL DB vooraf aanschaffen en bespaart u op de kosten voor SQL-berekeningen. Er worden afzonderlijk kosten gerekend voor SQL-licenties en de reservering geeft hierop geen korting. Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De besparingsschattingen worden berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en door het gebruikspatroon te extrapoleren dat in de afgelopen 30 dagen wordt geobserveerd. Aanbevelingen voor gedeelde reserveringsbereiken zijn beschikbaar in de ervaring voor reserveringsaankoop en kunnen u helpen nog meer te besparen.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Gereserveerde capaciteit van de app service Stamp-prijs
We analyseren uw App Service gebruiks patroon voor geïsoleerde omgevings stempels gedurende de afgelopen 30 dagen en raden een aankoop van gereserveerde capaciteit aan om uw besparingen te maximaliseren. Met gereserveerde capaciteit kunt u de zegelkosten voor gebruik van de geïsoleerde omgeving vooraf betalen en bespaart u op de kosten voor betalen per gebruik. Houd er rekening mee dat gereserveerde capaciteit alleen van toepassing is op de zegelkosten en niet op de App Service-exemplaren. Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De geschatte besparing wordt berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en het gebruikspatroon van de afgelopen 30 dagen.

### <a name="blob-storage-reserved-capacity"></a>Gereserveerde capaciteit van Blob-opslag
We hebben uw gebruik van Azure Blob- en Datalake-opslag in de afgelopen 30 dagen geanalyseerd en de aankoop van gereserveerde capaciteit berekend waarmee u optimaal zou kunnen besparen. Met gereserveerde capaciteit kunt u het uurlijkse gebruik vooraf aanschaffen en bespaart u op uw huidige on-demand kosten. Gereserveerde capaciteit voor Blob-opslag is alleen van toepassing op gegevens die zijn opgeslagen in Azure Blob (GPv2) en Azure Data Lake Storage (Gen 2). Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De geschatte besparing wordt berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en het gebruikspatroon dat in de afgelopen 30 dagen is geobserveerd. Aanbevelingen voor gedeelde reserveringsbereiken zijn beschikbaar in de ervaring voor reserveringsaankoop en kunnen u helpen nog meer te besparen.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Gereserveerde capaciteit voor MariaDB, MySQL en PostgreSQL
We analyseren uw Azure Database for MariaDB-, MySQL-en PostgreSQL gebruiks patroon gedurende de afgelopen 30 dagen en raden u aan om een gereserveerde capaciteit te kopen die uw besparingen maximaliseert. Met gereserveerde capaciteit kunt u MariaDB, MySQL en PostgreSQL per uur kopen en besparen op uw kosten. Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De geschatte besparing wordt berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en het gebruikspatroon van de afgelopen 30 dagen. Aanbevelingen voor gedeelde reserveringsbereiken zijn beschikbaar in de ervaring voor reserveringsaankoop en kunnen u helpen nog meer te besparen.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>Gereserveerde capaciteit van Synapse Analytics (voorheen SQL DW)
We analyseren uw Azure Synapse Analytics-gebruiks patroon gedurende de afgelopen 30 dagen en raden u aan om een gereserveerde capaciteit te kopen die uw besparingen maximaliseert. Met gereserveerde capaciteit kunt u het uurlijkse gebruik van Synapse Analytics vooraf aanschaffen en bespaart u op uw on-demand kosten. Gereserveerde capaciteit is een factureringsvoordeel en wordt automatisch toegepast op nieuwe of bestaande implementaties. De geschatte besparing wordt berekend voor afzonderlijke abonnementen met een reserveringsprijs van drie jaar en het gebruikspatroon dat in de afgelopen 30 dagen is geobserveerd. Aanbevelingen voor gedeelde reserveringsbereiken zijn beschikbaar in de ervaring voor reserveringsaankoop en kunnen u helpen nog meer te besparen.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Niet-gekoppelde open bare IP-adressen verwijderen om geld te besparen

Advisor identificeert open bare IP-adressen die momenteel niet zijn gekoppeld aan Azure-resources, zoals load balancers of Vm's. Deze open bare IP-adressen worden gefactureerd met een nominale belasting. Als u niet van plan bent om ze te gebruiken, kunnen de kosten worden bespaard als ze worden verwijderd.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Azure Data Factory-pijplijnen verwijderen die niet werken

Azure Advisor detecteert Azure Data Factory pijp lijnen die herhaaldelijk mislukken en raadt u aan om de problemen op te lossen of de mislukte pijp lijnen te verwijderen als ze niet meer nodig zijn. U wordt gefactureerd voor deze pijp lijnen, zelfs als ze niet voor u zijn. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Standaard momentopnamen gebruiken voor Managed Disks
Sla uw momentopnamen op in Standard Storage, ongeacht het opslagtype van de bovenliggende schijf, om een kostenbesparing van 60% te realiseren. Deze optie is de standaard optie voor het Managed Disks moment opnamen. Azure Advisor worden moment opnamen geïdentificeerd die zijn opgeslagen Premium Storage en raden u aan om uw moment opname te migreren van Premium naar standaard opslag. [Meer informatie over prijzen voor beheerde schijven](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Levenscyclusbeheer gebruiken
Azure Advisor maakt gebruik van informatie over het aantal Azure Blob Storage-objecten, de totale grootte en de trans acties om te detecteren of een of meer van uw opslag accounts het beste geschikt zijn om levenscyclus beheer in te scha kelen voor het laag maken van gegevens. U wordt gevraagd om levenscyclus beheer regels te maken om uw gegevens automatisch te voorzien van koud of archief om uw opslag kosten te optimaliseren en uw gegevens in Azure Blob-opslag te bewaren voor toepassings compatibiliteit.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Aanbeveling voor maken van een tijdelijke besturingssysteemschijf
Met een [kortstondige besturingssysteem schijf](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)kunnen klanten profiteren van de volgende voor delen: Bespaar op de opslag kosten voor de besturingssysteem schijf. Lagere lees- en schrijflatentie voor besturingssysteemschijf. Sneller terugzetten van VM-installatiekopie door besturingssysteem (en de tijdelijke schijf) opnieuw in te stellen op de oorspronkelijke toestand. Het is beter om tijdelijke besturingssysteem schijven te gebruiken voor verkorte IaaS Vm's of Vm's met stateless workloads. Advisor heeft een aanbeveling voor bronnen die voor delen kunnen hebben met een tijdelijke besturingssysteem schijf. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Toegang tot kosten aanbevelingen in Azure Advisor

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek en selecteer [**Advisor**](https://aka.ms/azureadvisordashboard) op elke pagina.

1. Op het **Advisor** -dash board selecteert u het tabblad **kosten** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)

