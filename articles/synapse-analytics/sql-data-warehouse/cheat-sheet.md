---
title: Spiekblad voor Azure Synapse Analytics (voorheen SQL DW)
description: Vind koppelingen en aanbevolen procedures om snel uw Azure Synapse Analytics-oplossingen (voorheen SQL DW)-oplossingen te bouwen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 55b00af9afeafb2a3fa7992cc457819dc1dcb2b2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631290"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Spiekblad voor Azure Synapse Analytics (voorheen SQL DW)

Dit spiekbriefje bevat handige tips en aanbevolen procedures voor het bouwen van Azure Synapse-oplossingen.

De volgende afbeelding geeft het proces weer voor het ontwerpen van een datawarehouse:

![Schema](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Query's en bewerkingen op tabellen

Als u van tevoren de primaire bewerkingen en query's kent die in uw datawarehouse kunnen worden uitgevoerd, kunt u prioriteiten stellen voor deze bewerkingen in uw datawarehouse-architectuur. Deze query's en bewerkingen zijn onder andere:

* Een of meerdere feitentabellen met dimensietabellen samenvoegen, de gecombineerde tabel filteren en vervolgens de resultaten aan een datamart toevoegen.
* Grote of kleine updates in uw feitelijke omzet aanbrengen.
* Alleen gegevens aan uw tabellen toevoegen.

Als u de typen bewerkingen van tevoren kent, kunt u het ontwerp van uw tabellen beter optimaliseren.

## <a name="data-migration"></a>Gegevensmigratie

Laad eerst uw gegevens in [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of Azure Blob Storage. Gebruik vervolgens PolyBase om uw gegevens in faseringstabellen te laden. Gebruik de volgende configuratie:

| Ontwerp | Aanbeveling |
|:--- |:--- |
| Distributie | Round robin |
| Indexeren | Heap |
| Partitionering | Geen |
| Resourceklasse | largerc of xlargerc |

Meer informatie over [gegevensmigratie](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [gegevens laden](design-elt-data-loading.md), en het [ELT-proces (extraheren, laden en transformeren)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Gedistribueerde of gerepliceerde tabellen

Gebruik de volgende strategieën, afhankelijk van de eigenschappen van de tabel:

| Type | Zeer geschikt voor...| Kijk uit met...|
|:--- |:--- |:--- |
| Gerepliceerd | * Kleine dimensie tabellen in een ster schema met minder dan 2 GB opslag na compressie (~ 5x compressie) |* Veel schrijftransacties zijn op tafel (zoals invoegen, upsert, verwijderen, bijwerken)<br></br>* U wijzigt de provisioning (Data Warehouse Units) regelmatig<br></br>* U gebruikt slechts 2-3 kolommen, maar uw tabel heeft veel kolommen<br></br>* U indexeert een gerepliceerde tabel |
| Round robin (standaard) | * Tijdelijke/faseringstabel<br></br> * Geen duidelijke toetreding sleutel of goede kandidaat kolom |* Prestaties zijn traag als gevolg van gegevensbeweging |
| Hash | * Feitentabellen<br></br>* Tabellen met grote afmetingen |* De distributiesleutel kan niet worden bijgewerkt |

**Tips:**

* Begin met round robin, maar ga voor een hashdistributiestrategie om te profiteren van een massively parallel-architectuur.
* Zorg ervoor dat algemene hashsleutels dezelfde gegevensindeling hebben.
* Niet distribueren op varchar formaat.
* Dimensietabellen met een algemene hashsleutel voor een feitentabel met regelmatige samenvoegbewerkingen kunnen met hash worden verdeeld.
* Gebruik *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)* om eventuele asymmetrie in gegevens te analyseren.
* Gebruik *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)* om gegevensverplaatsingen achter query's te analyseren, de tijdbroadcast te bewaken en de opnamevolgorde van bewerkingen te wijzigen. Dit is handig om uw distributiestrategie te controleren.

Meer informatie over [gerepliceerde tabellen](design-guidance-for-replicated-tables.md) en [gedistribueerde tabellen](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Uw tabel indexeren

Indexeren is handig voor het snel lezen van tabellen. Er bestaat een unieke reeks technologieën die u op basis van uw behoeften kunt gebruiken:

| Type | Zeer geschikt voor... | Kijk uit met...|
|:--- |:--- |:--- |
| Heap | * Staging/tijdelijke tabel<br></br>* Kleine tafels met kleine lookups |* Elke lookup scant de volledige tabel |
| Geclusterde index | * Tabellen met maximaal 100 miljoen rijen<br></br>* Grote tafels (meer dan 100 miljoen rijen) met slechts 1-2 kolommen zwaar gebruikt |* Gebruikt op een gerepliceerde tabel<br></br>* Je hebt complexe query's met meerdere join- en Groeps-op-activiteiten<br></br>* U maakt updates op de geïndexeerde kolommen: het duurt geheugen |
| Geclusterde columnstore-index (CCI) (standaardinstelling) | * Grote tafels (meer dan 100 miljoen rijen) | * Gebruikt op een gerepliceerde tabel<br></br>* U maakt enorme update operaties op uw tafel<br></br>* U overpartitioneert uw tabel: rijgroepen beslaan niet over verschillende distributieknooppunten en partities |

**Tips:**

* Mogelijk wilt u boven op een geclusterde index een niet-geclusterde index toevoegen aan een kolom die veel wordt gebruikt voor filteren.
* Wees voorzichtig met hoe u het geheugen van een tabel met CCI beheert. Wanneer u gegevens laadt, wilt u dat de gebruiker (of de query) profiteert van een grote resourceklasse. Voorkom bijsnijden en het maken van veel kleine gecomprimeerde rijgroepen.
* Bij Gen2 worden CCI-tabellen lokaal in de cache op de rekenknooppunten opgeslagen om de prestaties te maximaliseren.
* Bij CCI kunnen trage prestaties optreden vanwege slechte compressie van de rijgroepen. Als dit het geval is, herbouwt u uw CCI of ordent u deze opnieuw. U hebt ten minste 100.000 rijen per gecomprimeerde rijgroepen nodig. Ideaal is 1 miljoen rijen in een rijgroep.
* Afhankelijk van de frequentie en grootte van incrementeel laden, wilt u automatiseren wanneer u uw indexen opnieuw indeelt of herbouwt. Een lenteschoonmaak is altijd handig.
* Ga strategisch te werk bij het inkorten van een rijgroep. Hoe groot zijn de open rijgroepen? Hoeveel gegevens verwacht u dat er in de komende dagen worden geladen?

Meer informatie over [indexen](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Partitionering

U kunt uw tabel partitioneren wanneer het een grote feitentabel is (groter dan 1 miljard rijen). De partitiesleutel moet in 99 procent van de gevallen worden gebaseerd op datum. Zorg ervoor dat geen overpartitionering plaatsvindt, vooral wanneer u een geclusterde columnstore-index hebt.

Met faseringstabellen waarvoor ELT is vereist, kan partitionering voordelen opleveren. Het vergemakkelijkt het beheer van de gegevenslevenscyclus.
Zorg ervoor dat u uw gegevens niet overpartitioneert, vooral bij een geclusterde columnstore-index.

Meer informatie over [partities](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Incrementeel laden

Als u uw gegevens incrementeel laadt, moet u eerst grotere resourceklassen toewijzen voor het laden van uw gegevens.  Dit is vooral belangrijk bij het laden in tabellen met geclusterde kolomarchiefindexen.  Zie [resourceklassen](resource-classes-for-workload-management.md) voor meer informatie.  

We raden u aan PolyBase en ADF V2 te gebruiken voor het automatiseren van uw ELT-pijplijnen in uw datawarehouse.

Voor een grote batch updates in uw historische gegevens u overwegen een [CTAS](sql-data-warehouse-develop-ctas.md) te gebruiken om de gegevens te schrijven die u in een tabel wilt bewaren in plaats van INSERT, UPDATE en DELETE te gebruiken.

## <a name="maintain-statistics"></a>Statistieken bijhouden

 Totdat automatische statistieken algemeen beschikbaar zijn, is handmatig onderhoud van statistieken vereist. Het is belangrijk om uw statistieken bij te werken wanneer er *significante* wijzigingen optreden in uw gegevens. Dit helpt u om uw queryplannen te optimaliseren. Als u vindt dat het onderhouden van al uw statistieken te lang duurt, kunt u selectiever zijn over welke kolommen statistieken bevatten.

U kunt ook de frequentie van de updates definiëren. Zo wilt u datumkolommen, waar nieuwe waarden kunnen zijn toegevoegd, misschien dagelijks bijwerken. U haalt het meeste voordeel uit statistieken bij kolommen die onderdeel uitmaken van samenvoegingen, kolommen met het WHERE-component en kolommen in GROUP BY.

Meer informatie over [statistieken](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Resourceklasse

Resourcegroepen worden gebruikt als een manier om geheugen toe te wijzen aan query's. Als u meer geheugenruimte nodig hebt voor het verbeteren van de query- of laadsnelheid, kunt u hogere resourceklassen toewijzen. Aan de andere kant hebben grotere resourceklassen een impact op de gelijktijdigheid. Denk hier goed over na voordat u alle gebruikers naar een grote resourceklasse verplaatst.

Als u merkt dat query's te lang duren, controleert u of uw gebruikers niet in grote resourceklassen worden uitgevoerd. Grote resourceklassen nemen veel gelijktijdigheidssleuven in beslag. Ze kunnen ervoor zorgen dat andere query's in de wachtrij komen.

Ten slotte krijgt elke resourceklasse met behulp van Gen2 van [SQL-pool](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)2,5 keer meer geheugen dan Gen1.

Meer informatie over het werken met [resourceklassen en gelijktijdigheid](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Uw kosten verlagen

Een belangrijk kenmerk van Azure Synapse is de mogelijkheid om rekenbronnen te [beheren.](sql-data-warehouse-manage-compute-overview.md) U SQL-groep onderbreken wanneer u deze niet gebruikt, waardoor de facturering van rekenbronnen wordt gestopt. U kunt de schaal van resources aanpassen om te voldoen aan uw prestatievereisten. Voor onderbreken gebruikt u [Azure Portal](pause-and-resume-compute-portal.md) of [PowerShell](pause-and-resume-compute-powershell.md). Voor schaal aanpassen gebruikt u [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) of een [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

U kunt nu op elk moment automatisch de schaal aanpassen met Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>De architectuur optimaliseren voor betere prestaties

We raden u aan SQL Database en Azure Analysis Services in een hub-en-spoke-architectuur te gebruiken. Deze oplossing kan workloadisolatie bieden tussen verschillende gebruikersgroepen en tegelijkertijd geavanceerde beveiligingsfuncties van SQL Database en Azure Analysis Services gebruiken. Dit is ook een manier om grenzeloze gelijktijdigheid te bieden aan uw gebruikers.

Meer informatie over [typische architecturen die profiteren van Azure Synapse.](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/)

Implementeer in één klik uw spaken in SQL-databases vanuit SQL-groep:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>
