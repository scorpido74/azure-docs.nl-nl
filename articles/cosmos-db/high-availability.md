---
title: Hoge beschikbaarheid in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe Azure Cosmos DB hoge Beschik baarheid biedt
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 9667d82551e169dcc4a4bfd3ac79b15390f58aa0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609218"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hoge beschikbaarheid met Azure Cosmos DB

Azure Cosmos DB uw gegevens transparant repliceert in alle Azure-regio's die zijn gekoppeld aan uw Cosmos-account. Cosmos DB maakt gebruik van meerdere lagen van redundantie voor uw gegevens, zoals wordt weer gegeven in de volgende afbeelding:

![Fysieke partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- De gegevens in Cosmos-containers worden [horizon taal gepartitioneerd](partitioning-overview.md).

- Binnen elke regio wordt elke partitie beveiligd door een replicaset waarbij alle schrijf bewerkingen worden gerepliceerd en blijvend vastgelegd door een meerderheid van replica's. Replica's worden verdeeld over Maxi maal 10-20 fout domeinen.

- Elke partitie in alle regio's wordt gerepliceerd. Elke regio bevat alle gegevens partities van een Cosmos-container en kan schrijf bewerkingen accepteren en lees bewerkingen uitvoeren.  

Als uw Cosmos-account wordt gedistribueerd over *n* Azure-regio's, zijn er ten minste *n* x 4 kopieën van al uw gegevens. Naast het bieden van gegevens toegang met lage latentie en het schalen van schrijf-en lees doorvoer voor de regio's die zijn gekoppeld aan uw Cosmos-account, met meer regio's (hogere *N*), verbetert de beschik baarheid.  

## <a name="slas-for-availability"></a>Sla's voor Beschik baarheid

Als een wereld wijd gedistribueerde data base biedt Cosmos DB uitgebreide service overeenkomsten die de door Voer, latentie bij het 99e percentiel, consistentie en hoge Beschik baarheid omvatten. In de volgende tabel ziet u de garanties voor maximale Beschik baarheid van Cosmos DB voor accounts met meerdere regio's. Configureer voor hoge Beschik baarheid altijd uw Cosmos-accounts zodat er meerdere schrijf regio's zijn.

|Bewerkings type  | Enkele regio |Meerdere regio's (schrijf bewerkingen in één regio)|Meerdere regio's (schrijf bewerkingen met meerdere regio's) |
|---------|---------|---------|-------|
|Schrijfopdrachten    | 99,99    |99,99   |99,999|
|Titel     | 99,99    |99,999  |99,999|

> [!NOTE]
> In de praktijk is de daad werkelijke schrijf Beschik baarheid voor gebonden veroudering, sessie, consistent voor voegsel en uiteindelijke consistentie modellen aanzienlijk hoger dan de gepubliceerde Sla's. De werkelijke Lees Beschik baarheid voor alle consistentie niveaus is aanzienlijk hoger dan de gepubliceerde Sla's.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hoge beschikbaarheid met Cosmos DB in het geval van regionale storingen

Regionale storingen zijn niet ongewoon. Met Azure Cosmos DB is uw database altijd maximaal beschikbaar. De volgende details vastleggen Cosmos DB gedrag tijdens een storing, afhankelijk van de configuratie van uw Cosmos-account:

- Met Cosmos DB voordat een schrijf bewerking wordt bevestigd aan de client, worden de gegevens blijvend vastgelegd door een quorum van replica's binnen de regio die de schrijf bewerkingen accepteert.

- Multi-regio-accounts die zijn geconfigureerd met meerdere-schrijf regio's, zijn Maxi maal beschikbaar voor schrijf bewerkingen en lees bewerkingen. Regionale failovers zijn onmiddellijk en vereisen geen wijzigingen van de toepassing.

- Accounts met een enkele regio kunnen Beschik baarheid verliezen na een regionale storing. Het is altijd aanbevolen om ten **minste twee regio's** (bij voor keur, ten minste twee schrijf regio's) in te stellen met uw Cosmos-account om te allen tijde hoge Beschik baarheid te garanderen.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Accounts met meerdere regio's met een regio voor één schrijf bewerking (schrijf regio-uitval)

- Tijdens een onderbreking van de schrijf regio wordt een secundaire regio automatisch bevorderd als de nieuwe primaire schrijf regio als **automatische failover inschakelen** is geconfigureerd voor het Azure Cosmos-account. Wanneer deze functie is ingeschakeld, wordt de failover uitgevoerd naar een andere regio in de volg orde van de regio prioriteit die u hebt opgegeven.
- Wanneer de eerder beïnvloede regio weer online is, worden alle Schrijf gegevens die niet zijn gerepliceerd toen de regio is mislukt, beschikbaar gesteld via de [feed conflicten](how-to-manage-conflicts.md#read-from-conflict-feed). Toepassingen kunnen de feed voor conflicten lezen, de conflicten oplossen op basis van de toepassingsspecifieke logica en de bijgewerkte gegevens naar de Azure Cosmos-container schrijven, indien van toepassing.
- Zodra de eerder beïnvloede schrijf regio herstelt, wordt deze automatisch beschikbaar als een lees regio. U kunt teruggaan naar de herstelde regio als de schrijf regio. U kunt de regio's wijzigen met behulp van [Power shell, Azure CLI of Azure Portal](how-to-manage-database-account.md#manual-failover). Er zijn **geen gegevens of beschik baarheids verlies** vóór, tijdens of nadat u de schrijf regio hebt overgeschakeld en uw toepassing Maxi maal beschikbaar is.

> [!IMPORTANT]
> Het wordt ten zeerste aangeraden om de Azure Cosmos-accounts te configureren die worden gebruikt voor productie werkbelastingen om **automatische failover mogelijk te maken**. Voor een hand matige failover is een verbinding tussen de secundaire en primaire schrijf regio vereist om een consistentie controle te volt ooien om ervoor te zorgen dat er geen gegevens verloren gaan tijdens de failover. Als de primaire regio niet beschikbaar is, kan deze consistentie controle niet worden voltooid en kan de hand matige failover mislukken, wat leidt tot verlies van schrijf Beschik baarheid.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Accounts met meerdere regio's met een regio voor één schrijf bewerking (Lees regio lezen)

- Tijdens een lees regio kan Cosmos-accounts die gebruikmaken van een consistentie niveau of sterke consistentie met drie of meer Lees regio's, Maxi maal beschikbaar blijven voor lees-en schrijf bewerkingen.
- De verbinding met het betrokken gebied wordt automatisch verbroken en wordt offline gemarkeerd. De [Azure Cosmos DB sdk's](sql-api-sdk-dotnet.md) omleiden Lees aanroepen naar de volgende beschik bare regio in de lijst voorkeurs regio.
- Als geen van de regio's in de lijst met voorkeursregio's beschikbaar is, vallen aanroepen automatisch terug naar de huidige schrijfregio.
- Er zijn geen wijzigingen vereist in de toepassings code voor het verwerken van de onderbreking van de Lees regio. Wanneer de betrokken Lees regio weer online is, wordt deze automatisch gesynchroniseerd met de huidige schrijf regio en is deze weer beschikbaar om Lees aanvragen te kunnen behandelen.
- Latere leesbewerkingen worden omgeleid naar de herstelde regio zonder dat er wijzigingen in uw toepassingscode nodig zijn. Tijdens zowel de failover als het opnieuw deel nemen aan een eerder mislukte regio, worden Lees consistentie garanties door Cosmos DB door lopen.

> [!IMPORTANT]
> Azure Cosmos-accounts die gebruikmaken van sterke consistentie met twee of minder Lees regio's, verliezen de schrijf beschikbaarheid tijdens een onderbreking van de Lees regio, maar bewaren de beschik baarheid van de resterende regio's.

- Zelfs in een zeldzame en vervelend-gebeurtenis wanneer de Azure-regio permanent is onherstelbare, is er geen gegevens verlies als uw Cosmos-account met meerdere regio's is geconfigureerd met *sterke* consistentie. In het geval van een permanente onherstelbare, een Cosmos-account met meerdere regio's dat is geconfigureerd met consistentie van afhankelijkheid, is het venster voor mogelijk gegevens verlies beperkt tot het verouderde venster (*k* of *T*) waarbij K = 100000 updates en T = 5 minuten. Voor sessie, consistent voor voegsel en uiteindelijke consistentie niveaus geldt een maximum van 15 minuten voor het venster van het potentiële gegevens verlies. Zie voor meer informatie over RTO-en RPO-doelen voor Azure Cosmos DB [consistentie niveaus en gegevens duurzaamheid](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Ondersteuning voor beschikbaarheids zone

Naast de tolerantie voor meerdere regio's kunt u nu **zone redundantie** inschakelen wanneer u een regio selecteert om te koppelen aan uw Azure Cosmos-data base.

Met de ondersteuning van de beschikbaarheids zone zorgt Azure Cosmos DB ervoor dat replica's in meerdere zones binnen een bepaalde regio worden geplaatst om hoge Beschik baarheid en tolerantie tijdens zonegebonden storingen te bieden. Er zijn geen wijzigingen in latentie en andere Sla's in deze configuratie. In het geval van een storing in één zone biedt zone redundantie volledige gegevens duurzaamheid met RPO = 0 en beschik baarheid met RTO = 0.

Zone redundantie is een *aanvullende mogelijkheid* voor de replicatie functie voor [meerdere masters](how-to-multi-master.md) . Alleen zone redundantie kan worden verzorgd om regionale tolerantie te garanderen. In het geval van regionale storingen of toegang met een lage latentie over de regio's, wordt het aanbevolen om naast zone redundantie meerdere schrijf regio's te hebben.

Bij het configureren van schrijf bewerkingen met meerdere regio's voor uw Azure Cosmos-account, kunt u zonder extra kosten voor zone redundantie kiezen. In andere gevallen raadpleegt u de onderstaande opmerking over de prijzen voor ondersteuning voor zone redundantie. U kunt zone redundantie inschakelen voor een bestaande regio van uw Azure Cosmos-account door de regio te verwijderen en opnieuw toe te voegen als u de zone redundantie hebt ingeschakeld.

Deze functie is beschikbaar in de volgende Azure-regio's:

- Verenigd Koninkrijk Zuid

- Azië - zuidoost

- VS - oost

- VS - oost 2

- VS - centraal

- Europa -west

- VS - west 2

- Australië - oost

- Japan - oost

- Europa - noord

> [!NOTE]
> Als u Beschikbaarheidszones inschakelt voor een Azure Cosmos-account in één regio, worden er kosten in rekening gebracht die gelijk zijn aan het toevoegen van een extra regio aan uw account. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) en de [kosten voor meerdere regio's in azure Cosmos DB](optimize-cost-regions.md) artikelen voor meer informatie over prijzen.

De volgende tabel bevat een overzicht van de mogelijkheden voor hoge Beschik baarheid van verschillende account configuraties:

|KPI  |Eén regio zonder Beschikbaarheidszones (niet-AZ)  |Eén regio met Beschikbaarheidszones (AZ)  |Meerdere regio's schrijven met Beschikbaarheidszones (AZ, 2 regio's) – meest aanbevolen instelling |
|---------|---------|---------|---------|
|SLA voor Beschik baarheid schrijven | 99,99% | 99,99% | 99,999% |
|SLA voor Beschik baarheid lezen  | 99,99% | 99,99% | 99,999% |
|Prijs | Facturerings tarief voor één regio | Facturerings tarief van de beschikbaarheids zone met één regio | Facturerings tarief voor meerdere regio's |
|Zone fouten – gegevens verlies | Gegevens verlies | Geen gegevens verlies | Geen gegevens verlies |
|Zone fouten-Beschik baarheid | Beschikbaarheids verlies | Geen beschikbaar verlies | Geen beschikbaar verlies |
|Lees latentie | Kruis regio | Kruis regio | Laag |
|Schrijf latentie | Kruis regio | Kruis regio | Laag |
|Regionale storing – gegevens verlies | Gegevens verlies |  Gegevens verlies | Gegevens verlies <br/><br/> Wanneer de consistentie van de afhankelijke veroudering met meerdere masters en meer dan één regio wordt gebruikt, wordt het gegevens verlies beperkt tot de gebonden veroudering die is geconfigureerd voor uw account <br /><br />U kunt gegevens verlies voor komen tijdens een regionale storing door sterke consistentie met meerdere regio's te configureren. Deze optie is ook van invloed op de beschik baarheid en prestaties. Het kan alleen worden geconfigureerd voor accounts die zijn geconfigureerd voor schrijf bewerkingen met één regio. |
|Regionale storingen-Beschik baarheid | Beschikbaarheids verlies | Beschikbaarheids verlies | Geen beschikbaar verlies |
|Doorvoer | Ingerichte door Voer van X RU/s | Ingerichte door Voer van X RU/s | 2X RU/s ingerichte door Voer <br/><br/> Deze configuratie modus vereist twee keer de hoeveelheid door Voer in vergelijking met een enkele regio met Beschikbaarheidszones omdat er twee regio's zijn. |

> [!NOTE]
> Als u ondersteuning voor beschikbaarheids zones wilt inschakelen voor een Azure Cosmos-account met meerdere regio's, moet voor het account multi-master schrijf bewerkingen zijn ingeschakeld.

U kunt zone redundantie inschakelen wanneer u een regio toevoegt aan nieuwe of bestaande Azure Cosmos-accounts. Als u zone redundantie wilt inschakelen voor uw Azure Cosmos-account, moet `isZoneRedundant` u de `true` markering instellen op voor een specifieke locatie. U kunt deze vlag instellen binnen de eigenschap locations. Het volgende Power shell-fragment maakt bijvoorbeeld zone redundantie mogelijk voor de regio ' Zuidoost-Azië ':

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

De volgende opdracht laat zien hoe u zone redundantie voor de regio's Oost en WestUS2 kunt inschakelen:

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

U kunt Beschikbaarheidszones inschakelen met behulp van Azure Portal bij het maken van een Azure Cosmos-account. Wanneer u een account maakt, moet u ervoor zorgen dat u de functie voor **geo-redundantie**, **meerdere regio's**en een regio kiest waar Beschikbaarheidszones worden ondersteund:

![Beschikbaarheidszones met behulp van Azure Portal inschakelen](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Maxi maal beschik bare toepassingen bouwen

- Configureer uw Cosmos-account zo dat er ten minste twee regio's zijn met meerdere schrijf regio's om te zorgen voor hoge schrijf-en lees beschikbaarheid. Deze configuratie biedt de hoogste Beschik baarheid, de laagste latentie en de beste schaal baarheid voor zowel lees-als schrijf bewerkingen die worden ondersteund door service overeenkomsten. Zie [uw Cosmos-account met meerdere schrijf regio's configureren](tutorial-global-distribution-sql-api.md)voor meer informatie.

- Voor Cosmos-accounts met meerdere regio's die zijn geconfigureerd met een enkele schrijf regio, [schakelt u automatische failover in met behulp van Azure CLI of Azure Portal](how-to-manage-database-account.md#automatic-failover). Nadat u automatische failover hebt ingeschakeld, Cosmos DB automatisch een failover van uw account uit, wanneer er een regionale nood geval is.  

- Zelfs als uw Azure Cosmos-account Maxi maal beschikbaar is, is uw toepassing mogelijk niet juist ontworpen om Maxi maal beschikbaar te blijven. Als u de end-to-end hoge Beschik baarheid van uw toepassing wilt testen, kunt u als onderdeel van uw toepassing tests of herstel na nood gevallen, de automatische failover voor het account tijdelijk uitschakelen, de [hand matige failover aanroepen met behulp van Power shell, Azure CLI of Azure Portal](how-to-manage-database-account.md#manual-failover)en vervolgens de failover van uw toepassing controleren. Zodra het proces is voltooid, kunt u een failover uitvoeren naar de primaire regio en de automatische failover voor het account herstellen.

- Binnen een wereld wijd gedistribueerde database omgeving is er een rechtstreekse relatie tussen het consistentie niveau en de duurzaamheid van de gegevens in de aanwezigheid van een regionale storing. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld na een storende gebeurtenis. De tijd die nodig is om een toepassing volledig te herstellen, wordt de beoogde herstel tijd (RTO) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates die de toepassing kan afnemen bij het herstellen na een storende gebeurtenis. Deze periode wordt het beoogde herstelpunt (RPO) genoemd. Zie [consistentie niveaus en gegevens duurzaamheid](consistency-levels-tradeoffs.md#rto) voor een overzicht van de RPO en RTO voor Azure Cosmos db.

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u de volgende artikelen lezen:

- [Beschik baarheid en prestaties voor diverse consistentie niveaus](consistency-levels-tradeoffs.md)
- [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
- [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)
- [Consistentie niveaus in Azure Cosmos DB](consistency-levels.md)
- [Uw Cosmos-account configureren met meerdere schrijf regio's](how-to-multi-master.md)
