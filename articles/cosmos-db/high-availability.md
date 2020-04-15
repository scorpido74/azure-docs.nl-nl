---
title: Hoge beschikbaarheid in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe Azure Cosmos DB hoge beschikbaarheid biedt
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382711"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hoge beschikbaarheid met Azure Cosmos DB

Azure Cosmos DB repliceert uw gegevens op transparante wijze in alle Azure-regio's die zijn gekoppeld aan uw Cosmos-account. Cosmos DB maakt gebruik van meerdere redundantielagen voor uw gegevens, zoals weergegeven in de volgende afbeelding:

![Fysieke partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- De gegevens binnen de containers Cosmos [worden horizontaal verdeeld.](partitioning-overview.md)

- Binnen elke regio wordt elke partitie beschermd door een replica-set met alle schrijft gerepliceerd en blijvend begaan door een meerderheid van de replica's. Replica's worden verdeeld over maar liefst 10-20 foutdomeinen.

- Elke partitie in alle regio's wordt gerepliceerd. Elke regio bevat alle gegevenspartities van een Cosmos-container en kan schrijft accepteren en leest serveren.  

Als uw Cosmos-account is verdeeld over *N* Azure-regio's, worden er ten minste *N* x 4-kopieën van al uw gegevens weergegeven. Naast het bieden van lage latentie gegevens toegang en schalen schrijf /lees doorvoer over de regio's die zijn gekoppeld aan uw Cosmos-account, met meer regio's (hogere *N)* verder verbetert de beschikbaarheid.  

## <a name="slas-for-availability"></a>SLA's voor beschikbaarheid

Als een wereldwijd gedistribueerde database biedt Cosmos DB uitgebreide SLA's die doorvoer, latentie op het 99e percentiel, consistentie en hoge beschikbaarheid omvatten. De onderstaande tabel toont de garanties voor hoge beschikbaarheid die Cosmos DB biedt voor accounts met één en meerdere regio's. Voor hoge beschikbaarheid configureert u uw Cosmos-accounts altijd om meerdere schrijfregio's te hebben.

|Bewerkingstype  | Eén regio |Multiregio 's -regio ', schrijft één regio'|Multi-regio (multi-regio schrijft) |
|---------|---------|---------|-------|
|Schrijft    | 99,99    |99,99   |99,999|
|Leest     | 99,99    |99,999  |99,999|

> [!NOTE]
> In de praktijk is de werkelijke schrijfbeschikbaarheid voor begrensde staleness, sessie, consistent voorvoegsel en uiteindelijke consistentiemodellen aanzienlijk hoger dan de gepubliceerde SLA's. De werkelijke leesbeschikbaarheid voor alle consistentieniveaus is aanzienlijk hoger dan de gepubliceerde SLA's.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hoge beschikbaarheid met Cosmos DB in het geval van regionale storingen

Regionale storingen zijn niet ongewoon. Met Azure Cosmos DB is uw database altijd maximaal beschikbaar. De volgende details leggen het gedrag van Cosmos DB vast tijdens een storing, afhankelijk van de configuratie van uw Cosmos-account:

- Met Cosmos DB worden de gegevens blijvend vastgelegd door een quorum replica's binnen de regio dat de schrijfbewerkingen accepteert voordat een schrijfbewerking wordt erkend.

- Multi-region accounts geconfigureerd met meerdere schrijfregio's zijn zeer beschikbaar voor zowel schrijft als leest. Regionale failovers zijn onmiddellijk en vereisen geen wijzigingen van de toepassing.

- Accounts met één regio kunnen hun beschikbaarheid verliezen als gevolg van een regionale storing. Het wordt altijd aanbevolen om **ten minste twee regio's** (bij voorkeur ten minste twee schrijfregio's) in te stellen met uw Cosmos-account om te allen tijde een hoge beschikbaarheid te garanderen.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Accounts met meerdere regio's met één schrijfgebied (uitval van het schrijfgebied)

- Tijdens een uitval van het schrijfgebied wordt een secundaire regio automatisch gepromoot als het nieuwe primaire schrijfgebied wanneer **automatische failover wordt** ingeschakeld op het Azure Cosmos-account. Wanneer deze is ingeschakeld, vindt de failover plaats in een andere regio in de volgorde van de regioprioriteit die u hebt opgegeven.
- Wanneer het eerder getroffen gebied weer online is, worden alle schrijfgegevens die niet zijn gerepliceerd toen de regio is mislukt, beschikbaar gesteld via de [conflictfeed](how-to-manage-conflicts.md#read-from-conflict-feed). Toepassingen kunnen de conflictfeed lezen, de conflicten oplossen op basis van de toepassingsspecifieke logica en de bijgewerkte gegevens zo nodig terugschrijven naar de Azure Cosmos-container.
- Zodra het eerder getroffen schrijfgebied herstelt, wordt het automatisch beschikbaar als leesregio. U terugschakelen naar het herstelde gebied als het schrijfgebied. U de regio's schakelen met [PowerShell, Azure CLI of Azure-portal.](how-to-manage-database-account.md#manual-failover) Er is **geen gegevens of beschikbaarheidsverlies** voor, tijdens of na het wisselen van schrijfgebied en uw toepassing blijft zeer beschikbaar.

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen om de Azure Cosmos-accounts te configureren die worden gebruikt voor productieworkloads om **automatische failover mogelijk**te maken. Handmatige failover vereist connectiviteit tussen secundair en primair schrijfgebied om een consistentiecontrole uit te vullen om ervoor te zorgen dat er geen gegevensverlies is tijdens de failover. Als het primaire gebied niet beschikbaar is, kan deze consistentiecontrole niet worden voltooid en wordt de handmatige failover niet geslaagd, waardoor de schrijfbeschikbaarheid verloren gaat.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Accounts met meerdere regio's met één schrijfgebied (regiostoring lezen)

- Tijdens een leesregio-uitval blijven Cosmos-accounts met behulp van een consistentieniveau of een sterke consistentie met drie of meer gelezen regio's zeer beschikbaar voor lezen en schrijven.
- Het getroffen gebied wordt automatisch losgekoppeld en wordt offline gemarkeerd. De [Azure Cosmos DB SDKs](sql-api-sdk-dotnet.md) leiden leesoproepen om naar het volgende beschikbare gebied in de lijst met voorkeursregio's.
- Als geen van de regio's in de lijst met voorkeursregio's beschikbaar is, vallen aanroepen automatisch terug naar de huidige schrijfregio.
- Er zijn geen wijzigingen vereist in uw toepassingscode om de uitval van de leesregio af te handelen. Wanneer de in beïnvloede leesregio weer online is, wordt deze automatisch gesynchroniseerd met het huidige schrijfgebied en is het weer beschikbaar om leesverzoeken weer te geven.
- Latere leesbewerkingen worden omgeleid naar de herstelde regio zonder dat er wijzigingen in uw toepassingscode nodig zijn. Tijdens zowel failover als het opnieuw samenvoegen van een eerder mislukte regio, lees consistentie garanties blijven worden gerespecteerd door Cosmos DB.

> [!IMPORTANT]
> Azure Cosmos-accounts met een sterke consistentie met twee of minder leesregio's verliezen de schrijfbeschikbaarheid tijdens een leesregio-storing, maar behouden de leesbeschikbaarheid voor resterende regio's.

- Zelfs in een zeldzame en ongelukkige gebeurtenis wanneer de Azure-regio permanent onherstelbaar is, is er geen gegevensverlies als uw Cosmos-account met meerdere regio's is geconfigureerd met *sterke* consistentie. In het geval van een permanent onherstelbaar schrijfgebied, een cosmos-account met meerdere regio's, geconfigureerd met consistentie van begrensde staleness, is het venster voor potentiële gegevensverlies beperkt tot het venster *(K* of *T)* waarin K=100.000 updates en T=5 minuten zijn. Voor sessie-, consistent-voorvoegsel en uiteindelijke consistentieniveaus is het venster voor potentiële gegevensverlies beperkt tot maximaal 15 minuten. Zie [Consistentieniveaus en gegevensduurzaamheid](consistency-levels-tradeoffs.md#rto) voor meer informatie over RTO- en RPO-doelen voor Azure Cosmos DB

## <a name="availability-zone-support"></a>Ondersteuning voor beschikbaarheidszone

Naast de tolerantie voor verschillende regio's u nu **ook zoneredundantie** inschakelen bij het selecteren van een regio die u wilt koppelen aan uw Azure Cosmos-database.

Met ondersteuning voor beschikbaarheidszone zorgt Azure Cosmos DB ervoor dat replica's in meerdere zones binnen een bepaald gebied worden geplaatst om hoge beschikbaarheid en veerkracht te bieden tijdens zonale fouten. Er zijn geen wijzigingen in latentie en andere SLA's in deze configuratie. In het geval van een fout in één zone biedt zoneredundantie volledige duurzaamheid van gegevens met RPO=0 en beschikbaarheid met RTO=0.

Zoneredundantie is een *aanvullende mogelijkheid voor* de [replicatiefunctie met meerdere masters.](how-to-multi-master.md) Zone redundantie alleen kan niet worden ingeroepen om regionale veerkracht te bereiken. In het geval van regionale uitval of toegang tot lage latentie in de regio's wordt bijvoorbeeld geadviseerd om meerdere schrijfregio's te hebben naast zoneredundantie.

Wanneer u schrijft voor meerdere regio's configureert voor uw Azure Cosmos-account, u zich zonder extra kosten aanmelden voor zoneredundantie. Anders zie je de onderstaande notitie met betrekking tot de prijzen voor zoneredundantieondersteuning. U zoneredundantie inschakelen op een bestaand gebied van uw Azure Cosmos-account door de regio te verwijderen en het toe te voegen met de zoneredundantie ingeschakeld.

Deze functie is beschikbaar in volgende Azure-regio's:

- Verenigd Koninkrijk Zuid

- Azië - zuidoost

- VS - oost

- VS - oost 2

- VS - centraal

- Europa -west

- VS - west 2

> [!NOTE]
> Als u beschikbaarheidszones inschakelt voor een Azure Cosmos-account met één regio, worden kosten in rekening gebracht die overeenkomen met het toevoegen van een extra regio aan uw account. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/) en de [multiregiokosten in Azure Cosmos DB-artikelen](optimize-cost-regions.md) voor meer informatie over de prijzen.

In de volgende tabel wordt een overzicht van de hoge beschikbaarheidsmogelijkheden van verschillende accountconfiguraties:

|KPI  |Eén regio zonder beschikbaarheidszones (niet-AZ)  |Eén regio met beschikbaarheidszones (AZ)  |Multi-regio schrijft met Availability Zones (AZ, 2 regio's) – Meest aanbevolen instelling |
|---------|---------|---------|---------|
|Schrijf beschikbaarheid SLA | 99,99% | 99,99% | 99,999% |
|Lees beschikbaarheid SLA  | 99,99% | 99,99% | 99,999% |
|Prijs | Factureringstarief voor één regio | Factureringspercentage beschikbaarheidszone voor één regio | Factureringspercentage voor meerdere regio's |
|Zonefouten – gegevensverlies | Gegevensverlies | Geen gegevensverlies | Geen gegevensverlies |
|Zonefouten – beschikbaarheid | Beschikbaarheidsverlies | Geen verlies van beschikbaarheid | Geen verlies van beschikbaarheid |
|Leeslatentie | Cross regio | Cross regio | Laag |
|Latentie schrijven | Cross regio | Cross regio | Laag |
|Regionale storing – dataverlies | Gegevensverlies |  Gegevensverlies | Gegevensverlies <br/><br/> Bij het gebruik van begrensde staleness consistentie met multi master en meer dan één regio, is gegevensverlies beperkt tot de begrensde staleness die op uw account zijn geconfigureerd <br /><br />U gegevensverlies tijdens een regionale storing voorkomen door een sterke consistentie met meerdere regio's te configureren. Deze optie wordt geleverd met trade-offs die de beschikbaarheid en prestaties beïnvloeden. Het kan alleen worden geconfigureerd op accounts die zijn geconfigureerd voor schrijft met één regio. |
|Regionale storing – beschikbaarheid | Beschikbaarheidsverlies | Beschikbaarheidsverlies | Geen verlies van beschikbaarheid |
|Doorvoer | X RU/s ingerichte doorvoer | X RU/s ingerichte doorvoer | 2X RU/s ingerichte doorvoer <br/><br/> Deze configuratiemodus vereist twee keer de hoeveelheid doorvoer in vergelijking met één regio met beschikbaarheidszones omdat er twee regio's zijn. |

> [!NOTE]
> Als u ondersteuning voor beschikbaarheidszone wilt inschakelen voor een Azure Cosmos-account met meerdere regio's, moet het account multi-master schrijfbewerkingen hebben ingeschakeld.

U zoneredundantie inschakelen wanneer u een regio toevoegt aan nieuwe of bestaande Azure Cosmos-accounts. Als u zoneredundantie op uw Azure `isZoneRedundant` Cosmos-account wilt inschakelen, moet u de vlag instellen op `true` een specifieke locatie. U deze vlag instellen in de eigenschap locaties. Met het volgende PowerShell-fragment wordt bijvoorbeeld zoneredundantie voor de regio 'Zuidoost-Azië' mogelijk gemaakt:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

In de volgende opdracht ziet u hoe u zoneredundantie inschakelt voor de regio's 'EastUS' en 'WestUS2':

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

U beschikbaarheidszones inschakelen door azure-portal te gebruiken bij het maken van een Azure Cosmos-account. Wanneer u een account maakt, moet u de **georedundantie**, **schrijft met meerdere regio's**inschakelen en een regio kiezen waar beschikbaarheidszones worden ondersteund:

![Beschikbaarheidszones inschakelen met Azure-portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Bouwen van zeer beschikbare toepassingen

- Als u wilt zorgen voor een hoge schrijf- en leesbeschikbaarheid, configureert u uw Cosmos-account op ten minste twee regio's met regio's met meerdere schrijfgebieden. Deze configuratie biedt de hoogste beschikbaarheid, laagste latentie en de beste schaalbaarheid voor zowel lees- als schrijfbewerkingen die worden ondersteund door SLA's. Zie voor meer informatie hoe [u uw Cosmos-account configureert met meerdere schrijfregio's.](tutorial-global-distribution-sql-api.md)

- Voor cosmos-accounts met meerdere regio's die zijn geconfigureerd met één schrijfgebied, [schakelt u automatische failover in met Azure CLI of Azure-portal.](how-to-manage-database-account.md#automatic-failover) Nadat u automatische failover hebt ingeschakeld, wanneer er een regionale ramp is, mislukt Cosmos DB automatisch over uw account.  

- Zelfs als uw Azure Cosmos-account zeer beschikbaar is, is uw toepassing mogelijk niet correct ontworpen om zeer beschikbaar te blijven. Als u de end-to-end hoge beschikbaarheid van uw toepassing wilt testen, u als onderdeel van uw drills voor het testen van toepassingen of disaster recovery (DR) de automatische failover voor het account tijdelijk uitschakelen, de handmatige failover aanroepen [door PowerShell, Azure CLI of Azure-portal](how-to-manage-database-account.md#manual-failover)te gebruiken en vervolgens de failover van uw toepassing te controleren. Eenmaal voltooid, u niet terug naar de primaire regio en herstellen automatisch failover voor het account.

- Binnen een wereldwijd gedistribueerde databaseomgeving is er een direct verband tussen het consistentieniveau en de duurzaamheid van gegevens in aanwezigheid van een regio-brede uitval. Terwijl u uw bedrijfscontinuïteitsplan ontwikkelt, moet u de maximaal aanvaardbare tijd begrijpen voordat de toepassing volledig herstelt na een storende gebeurtenis. De tijd die nodig is voor een aanvraag om volledig te herstellen staat bekend als recovery time objective (RTO). U moet ook de maximale periode van recente gegevensupdates begrijpen die de toepassing kan tolereren wanneer deze herstelt na een storende gebeurtenis. Deze periode wordt het beoogde herstelpunt (RPO) genoemd. Zie [Consistentieniveaus en gegevensduurzaamheid](consistency-levels-tradeoffs.md#rto) voor de RPO en RTO voor Azure Cosmos DB

## <a name="next-steps"></a>Volgende stappen

Vervolgens u de volgende artikelen lezen:

- [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
- [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
- [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)
- [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)
- [Uw Cosmos-account configureren met meerdere schrijfregio's](how-to-multi-master.md)
