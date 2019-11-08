---
title: Server Resource limieten
description: In dit artikel vindt u een overzicht van de limieten voor de Azure SQL Database-Server bronnen voor afzonderlijke data bases en elastische Pools. Het bevat ook informatie over wat er gebeurt wanneer deze resource limieten worden bereikt of overschreden.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: 907fc89c0d9af01865037f650c407edd97e96645
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821144"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Resource limieten voor Azure SQL Database Server SQL Database

Dit artikel bevat een overzicht van de SQL Database resource limieten voor een SQL Database-Server die afzonderlijke data bases en elastische Pools beheert. Het bevat ook informatie over wat er gebeurt wanneer deze resource limieten worden bereikt of overschreden.

> [!NOTE]
> Zie [SQL database resource limieten voor beheerde instanties](sql-database-managed-instance-resource-limits.md)voor de limieten voor beheerde exemplaren.

## <a name="maximum-resource-limits"></a>Maximum aantal resource limieten

| Resource | Limiet |
| :--- | :--- |
| Data bases per server | 5000 |
| Standaard aantal servers per abonnement in een wille keurige regio | 20 |
| Maximum aantal servers per abonnement in een wille keurige regio | 200 |  
| DTU/eDTU-quotum per server | 54.000 |  
| vCore quotum per Server/exemplaar | 540 |
| Maximum aantal groepen per server | Beperkt door het aantal Dtu's of vCores. Als bijvoorbeeld elke groep 1000 Dtu's is, kan een server 54 groepen ondersteunen.|
|||

> [!NOTE]
> Voor het verkrijgen van meer DTU-/eDTU quotum, vCore-quota of meer servers dan de standaard hoeveelheid, kan een nieuwe ondersteunings aanvraag worden verzonden in de Azure Portal voor het abonnement met het probleem type ' quotum '. Het DTU/eDTU-quotum en de limiet voor de data base per server beperkt het aantal elastische Pools per server.
> [!IMPORTANT]
> Wanneer het aantal data bases de limiet per SQL Database Server nadert, kunnen de volgende problemen optreden:
> - De latentie verhogen bij het uitvoeren van query's op de hoofd database.  Dit geldt ook voor weer gaven van gegevens over het resource gebruik, zoals sys. resource_stats.
> - Het verg Roten van latentie in beheer bewerkingen en het weer geven van portal-gezichts punten waarbij de data bases op de server worden geïnventariseerd.

### <a name="storage-size"></a>Opslag grootte
- Voor afzonderlijke data bases rources raadpleegt u op [DTU gebaseerde resource limieten](sql-database-dtu-resource-limits-single-databases.md) of [op vCore gebaseerde resource limieten](sql-database-vcore-resource-limits-single-databases.md) voor de maximale opslag groottes per prijs categorie.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Wat er gebeurt wanneer de database resource limieten zijn bereikt

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (Dtu's en Edtu's/vCores)

Wanneer het database Compute-gebruik (gemeten door Dtu's en Edtu's, of vCores) hoog wordt, neemt de query latentie toe en kan zelfs een time-out oplopen. Onder deze omstandigheden kunnen query's door de service in de wachtrij worden geplaatst en worden resources voor uitvoering beschikbaar gesteld als bronnen gratis worden.
Wanneer het gebruik van hoge berekeningen wordt tegengekomen, zijn de volgende opties voor risico beperking:

- De reken grootte van de data base of elastische pool verg Roten om de data base te voorzien van meer compute-resources. Zie bronnen van [één data base schalen](sql-database-single-database-scale.md) en [elastische pool resources schalen](sql-database-elastic-pool-scale.md).
- Query's optimaliseren om het resource gebruik van elke query te verminderen. Zie [query tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting)(Engelstalig) voor meer informatie.

### <a name="storage"></a>Storage

Wanneer gebruikte database ruimte de maximale grootte bereikt, worden invoeg bewerkingen en updates die de omvang van de gegevens overschrijden, door data bases ingevoegd en bijgewerkt en ontvangen clients een [fout bericht](sql-database-develop-error-messages.md). Data base selecteren en verwijderen gaat verder.

Wanneer u gebruik maakt van hoge ruimte, kunt u onder andere het volgende doen:

- Verg root de maximale grootte van de data base of elastische pool of voeg meer opslag toe. Zie bronnen van [één data base schalen](sql-database-single-database-scale.md) en [elastische pool resources schalen](sql-database-elastic-pool-scale.md).
- Als de data base zich in een elastische pool bevindt, kan de data base buiten de pool worden geplaatst, zodat de opslag ruimte niet wordt gedeeld met andere data bases.
- Een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md) voor meer informatie.

### <a name="sessions-and-workers-requests"></a>Sessies en werk nemers (aanvragen)

Het maximum aantal sessies en werk rollen wordt bepaald door de servicelaag en de berekenings grootte (Dtu's en Edtu's). Nieuwe aanvragen worden afgewezen wanneer de limiet voor sessies of werk nemers is bereikt en clients ontvangen een fout bericht. Hoewel het aantal beschik bare verbindingen kan worden beheerd door de toepassing, is het aantal gelijktijdige werk nemers vaak moeilijker om te schatten en te beheren. Dit geldt met name voor piek belasting perioden wanneer database resource limieten zijn bereikt en werk nemers worden opgedeeld als gevolg van meer actieve query's.

Wanneer u het gebruik van hoge sessies of werk nemers ondervindt, kunt u de volgende beperkende opties gebruiken:

- De servicelaag of de reken grootte van de data base of elastische pool wordt verhoogd. Zie bronnen van [één data base schalen](sql-database-single-database-scale.md) en [elastische pool resources schalen](sql-database-elastic-pool-scale.md).
- Het optimaliseren van query's om het resource gebruik van elke query te verminderen als de oorzaak van het verhoogde werk nemer is vanwege de conflicten voor reken bronnen. Zie [query tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting)(Engelstalig) voor meer informatie.

## <a name="transaction-log-rate-governance"></a>Beleid voor transactie logboek belasting 
Het tarief voor transactie Logboeken is een proces in Azure SQL Database dat wordt gebruikt om een hoge opname snelheid te beperken voor werk belastingen, zoals bulksgewijs invoegen, selecteren en index builds. Deze limieten worden bijgehouden en afgedwongen op het niveau van de subseconde voor het genereren van het aantal logboek records dat door Voer wordt beperkt, ongeacht het aantal IOs-aanvragen dat kan worden uitgegeven aan gegevens bestanden.  Tarieven voor het genereren van transactie logboeken worden momenteel lineair geschaald naar een punt dat afhankelijk is van de hardware, waarbij de maximale logboek snelheid van 96 MB/s met het vCore-aankoop model wordt toegestaan. 

> [!NOTE]
> De daad werkelijke fysieke IOs-naar-transactie logboek bestanden zijn niet gebonden of beperkt. 

De logboek tarieven zijn zodanig ingesteld dat ze in verschillende scenario's kunnen worden bereikt en onderhouden, terwijl het hele systeem de functionaliteit kan behouden met een geminimaliseerd effect op de belasting van de gebruiker. Log rate governance zorgt ervoor dat back-ups van transactie logboeken binnen de gepubliceerde herstel-Sla's blijven.  Dit bestuur voor komt ook een buitensporige achterstand op secundaire replica's.

Wanneer er logboek records worden gegenereerd, wordt elke bewerking geëvalueerd en beoordeeld of deze moet worden uitgesteld om een maximale gewenste logboek frequentie (MB/s per seconde) te behouden. De vertragingen worden niet toegevoegd wanneer de logboek records worden leeg gemaakt naar de opslag, maar de log rate governance wordt toegepast tijdens het genereren van de logboek registratie.

De werkelijke generatie tarieven voor logboek registratie die tijdens de uitvoering zijn opgelegd, kunnen ook worden beïnvloed door de feedback mechanismen, waardoor de toegestane logboek tarieven tijdelijk worden verminderd zodat het systeem kan stabiliseren. Beheer van de ruimte van het logboek bestand, voor komen dat de logboek ruimte wordt gebruikt en de replicatie mechanismen voor beschikbaarheids groepen kunnen de algehele systeem limieten tijdelijk verlagen. 

De verkeers vormgeving van de logboek frequentie wordt geoppereerd via de volgende wacht typen (beschikbaar in de [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) dmv):

| Wacht type | Opmerkingen |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Data base beperken |
| POOL_LOG_RATE_GOVERNOR | Beperking van groepen |
| INSTANCE_LOG_RATE_GOVERNOR | Beperking op exemplaar niveau |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback beheer, fysieke replicatie van beschikbaarheids groepen in Premium/Bedrijfskritiek niet bijhouden |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback beheer, beperkende tarieven om te voor komen dat er een fout optreedt in de logboek ruimte |
|||

Houd rekening met de volgende opties als er een frequentie limiet optreedt die de gewenste schaal baarheid belemmert:
- Schaal omhoog naar een grotere laag om de maximale logboek frequentie van 96 MB/s te verkrijgen. 
- Als gegevens die worden geladen tijdelijk zijn, d.w.z. faserings gegevens in een ETL-proces, kan deze worden geladen in TempDB (dit is mini maal geregistreerd). 
- Voor analytische scenario's laadt u in een geclusterde column Store-gedekte tabel. Dit reduceert de vereiste logboek frequentie vanwege compressie. Deze techniek verhoogt het CPU-gebruik en is alleen van toepassing op gegevens sets die profiteren van geclusterde column Store-indexen. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [dtu's en edtu's](sql-database-purchase-models.md#dtu-based-purchasing-model)voor informatie over Dtu's en edtu's.
- Zie [tempdb in Azure SQL database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie over de limieten voor TempDB-grootte.
