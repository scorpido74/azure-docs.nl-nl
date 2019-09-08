---
title: Query Store in Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie query Store in Azure Database for PostgreSQL-één server beschreven.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: deab527d44713bffed1f430ec283592d0e4232ee
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764419"
---
# <a name="monitor-performance-with-the-query-store"></a>Prestaties bewaken met de query Store

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6, 10, 11

De functie query Store in Azure Database for PostgreSQL biedt een manier om query prestaties na verloop van tijd bij te houden. Query Store vereenvoudigt het oplossen van prestaties, omdat u snel de langste en meest tijdrovende query's kunt vinden. In de query Store wordt automatisch een geschiedenis van query's en runtime-statistieken vastgelegd en worden deze voor uw beoordeling bewaard. De gegevens worden op tijd Vensters gescheiden, zodat u de database gebruiks patronen kunt zien. Gegevens voor alle gebruikers, data bases en query's worden opgeslagen in een Data Base met de naam **azure_sys** in het Azure database for PostgreSQL-exemplaar.

> [!IMPORTANT]
> Wijzig de **azure_sys** -data base of de bijbehorende schema's niet. Dit zorgt ervoor dat query Store en gerelateerde prestatie functies niet goed werken.

## <a name="enabling-query-store"></a>Query Store inschakelen
Query Store is een opt-in-functie, waardoor deze niet standaard actief is op een server. De Store is globaal ingeschakeld of uitgeschakeld voor alle data bases op een bepaalde server en kan niet worden in-of uitgeschakeld per data base.

### <a name="enable-query-store-using-the-azure-portal"></a>Query Store inschakelen met behulp van de Azure Portal
1. Meld u aan bij de Azure Portal en selecteer uw Azure Database for PostgreSQL-server.
2. Selecteer **server parameters** in de sectie **instellingen** van het menu.
3. Zoek de `pg_qs.query_capture_mode` para meter.
4. Stel de waarde in `TOP` op en **Sla**deze op.

Wachtende statistieken in het query archief inschakelen: 
1. Zoek de `pgms_wait_sampling.query_capture_mode` para meter.
1. Stel de waarde in `ALL` op en **Sla**deze op.


U kunt deze para meters ook instellen met behulp van de Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Maxi maal 20 minuten toestaan dat de eerste batch met gegevens in de azure_sys-data base blijft.

## <a name="information-in-query-store"></a>Informatie in query Store
Het query archief heeft twee winkels:
- Een runtime-statistieken opslag voor het persistent maken van de statistieken voor query-uitvoering.
- Een wacht statistieken opslag voor permanente wacht statistieken.

Veelvoorkomende scenario's voor het gebruik van query Store zijn onder andere:
- Bepalen hoe vaak een query in een bepaald tijd venster is uitgevoerd
- De gemiddelde uitvoerings tijd van een query vergelijken in tijd Vensters om grote verschillen te bekijken
- Langst uitgevoerde query's in de afgelopen X uur identificeren
- Eerste N query's identificeren die op resources wachten
- Wat is de wacht aard van een bepaalde query?

Om ruimte gebruik te minimaliseren, worden de runtime uitvoerings statistieken in de runtime-statistieken opslag geaggregeerd over een vast, configureerbaar tijd venster. De informatie in deze archieven is zichtbaar door query's uit te geven op de query Store-weer gaven.

De volgende query retourneert informatie over query's in query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Of deze query voor wachtende statistieken:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Wacht query's zoeken
Wacht gebeurtenis typen combi neren verschillende wacht gebeurtenissen in buckets op vergelijk bare wijze. Het query archief bevat het type wacht gebeurtenis, specifieke wacht gebeurtenis naam en de query in kwestie. Als u deze wacht gegevens wilt correleren met de statistieken voor de runtime van query's, betekent dit dat u meer inzicht krijgt in wat bijdragen aan de prestatie kenmerken van de query.

Hier volgen enkele voor beelden van hoe u meer inzicht kunt krijgen in uw werk belasting met behulp van de wacht statistieken in query Store:

| **Observ** | **Actie** |
|---|---|
|Wacht tijden met hoge vergren deling | Controleer de query teksten voor de betrokken query's en Identificeer de doel entiteiten. Zoek in query Store naar andere query's die dezelfde entiteit wijzigen, die regel matig wordt uitgevoerd en/of een hoge duur hebben. Nadat u deze query's hebt geïdentificeerd, kunt u overwegen om de toepassings logica te wijzigen om gelijktijdig gebruik te kunnen verbeteren of een minder beperkend isolatie niveau te gebruiken.|
| Hoge buffer-IO-wacht tijden | Zoek de query's met een groot aantal fysieke Lees bewerkingen in query Store. Als ze overeenkomen met de query's met hoge i/o-wacht tijden, kunt u overwegen een index voor de onderliggende entiteit te introduceren, om te zoeken in plaats van scans. Hierdoor worden de i/o-overhead van de query's geminimaliseerd. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen voor deze server zijn die de query's optimaliseren.|
| Hoog geheugen wacht tijden | Zoek het hoogste geheugen gebruik query's in query Store. Deze query's vertragen waarschijnlijk verdere voortgang van de betrokken query's. Controleer de **prestatie aanbevelingen** voor uw server in de portal om te zien of er index aanbevelingen zijn waarmee deze query's kunnen worden geoptimaliseerd.|

## <a name="configuration-options"></a>Configuratie-opties
Wanneer query Store is ingeschakeld, worden gegevens opgeslagen in een periode van 15 minuten voor aggregatie van Maxi maal 500 DISTINCT-query's per venster. 

De volgende opties zijn beschikbaar voor het configureren van query Store-para meters.

| **Parameter** | **Beschrijving** | **Standaard** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | Hiermee stelt u in welke instructies worden bijgehouden. | geen | geen, boven, alle |
| pg_qs.max_query_text_length | Hiermee stelt u de maximale query lengte in die kan worden opgeslagen. Langere query's worden afgekapt. | 6000 | 100-10K |
| pg_qs.retention_period_in_days | Hiermee stelt u de Bewaar periode. | 7 | 1 - 30 |
| pg_qs.track_utility | Hiermee wordt ingesteld of hulp opdrachten worden bijgehouden | op | aan, uit |

De volgende opties zijn specifiek van toepassing op wacht statistieken.

| **Parameter** | **Beschrijving** | **Standaard** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Hiermee wordt ingesteld welke instructies worden bijgehouden voor wachtende statistieken. | geen | geen, alle|
| Pgms_wait_sampling.history_period | Stel de frequentie in milliseconden in waarop wacht gebeurtenissen worden bemonsterd. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** vervangt **pgms_wait_sampling. query_capture_mode**. Als pg_qs. query_capture_mode geen is, heeft de instelling pgms_wait_sampling. query_capture_mode geen effect.


Gebruik de [Azure Portal](howto-configure-server-parameters-using-portal.md) of [Azure cli](howto-configure-server-parameters-using-cli.md) om een andere waarde voor een para meter op te halen of in te stellen.

## <a name="views-and-functions"></a>Weer gaven en functies
Bekijk en beheer query Store met behulp van de volgende weer gaven en functies. Iedereen in de open bare rol PostgreSQL kan deze weer gaven gebruiken om de gegevens in query Store te bekijken. Deze weer gaven zijn alleen beschikbaar in de **azure_sys** -data base.

Query's worden genormaliseerd door de structuur te bekijken na het verwijderen van letterlijke waarden en constanten. Als twee query's identiek zijn, met uitzonde ring van letterlijke waarden, hebben ze dezelfde hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
In deze weer gave worden alle gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID en query-ID. 

|**Name**   |**Type** | **Referentie**  | **Beschrijving**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID uit de runtime_stats_entries-tabel|
|user_id    |oid    |pg_authid.oid  |OID van de gebruiker die de instructie heeft uitgevoerd|
|db_id  |oid    |pg_database.oid    |De OID van de Data Base waarin de instructie is uitgevoerd|
|query_id   |bigint  || Interne hash-code, berekend op basis van de ontledings structuur van de instructie|
|query_sql_text |Varchar (10000)  || De tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samen geclusterd. deze tekst is de tekst voor de eerste van de query's in het cluster.|
|plan_id    |bigint |   |De ID van het abonnement dat is gekoppeld aan deze query en die nog niet beschikbaar is|
|start_time |timestamp  ||  Query's worden geaggregeerd op tijd-buckets: de tijds panne van een Bucket is standaard 15 minuten. Dit is de begin tijd die overeenkomt met het tijds interval voor deze vermelding.|
|end_time   |timestamp  ||  De eind tijd die overeenkomt met het tijds interval voor deze vermelding.|
|oproepen  |bigint  || Aantal keren dat de query is uitgevoerd|
|total_time |dubbele precisie   ||  Totale uitvoerings tijd van de query, in milliseconden|
|min_time   |dubbele precisie   ||  Minimale uitvoerings tijd van de query, in milliseconden|
|max_time   |dubbele precisie   ||  Maximale uitvoerings tijd van de query, in milliseconden|
|mean_time  |dubbele precisie   ||  Gemiddelde uitvoerings tijd van query's, in milliseconden|
|stddev_time|   dubbele precisie    ||  Standaard afwijking van de uitvoerings tijd van de query, in milliseconden |
|rijen   |bigint ||  Totaal aantal rijen dat is opgehaald of beïnvloed door de instructie|
|shared_blks_hit|   bigint  ||  Totaal aantal gedeelde blok cache treffers per instructie|
|shared_blks_read|  bigint  ||  Totaal aantal gedeelde blokken dat is gelezen door de instructie|
|shared_blks_dirtied|   bigint   || Totaal aantal gedeelde blokken dirtied door de instructie |
|shared_blks_written|   bigint  ||  Totaal aantal gedeelde blokken dat is geschreven door de instructie|
|local_blks_hit|    bigint ||   Totaal aantal lokale blok cache treffers per instructie|
|local_blks_read|   bigint   || Totaal aantal lokale blokken dat door de instructie is gelezen|
|local_blks_dirtied|    bigint  ||  Totaal aantal lokale blokken dirtied door de instructie|
|local_blks_written|    bigint  ||  Totaal aantal lokale blokken dat is geschreven door de instructie|
|temp_blks_read |bigint  || Totaal aantal tijdelijke blokken dat is gelezen door de instructie|
|temp_blks_written| bigint   || Totaal aantal tijdelijke blokken dat is geschreven door de instructie|
|blk_read_time  |dubbele precisie    || De totale tijd die de instructie heeft besteed aan het lezen van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
|blk_write_time |dubbele precisie    || De totale tijd die de instructie heeft besteed aan het schrijven van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Deze weer gave retourneert query tekst gegevens in query Store. Er is één rij voor elke afzonderlijke query_text.

|**Name**|  **Type**|   **Beschrijving**|
|---|---|---|
|query_text_id  |bigint     |ID voor de query_texts-tabel|
|query_sql_text |Varchar (10000)     |De tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samen geclusterd. deze tekst is de tekst voor de eerste van de query's in het cluster.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Met deze weer gave worden wachtende gebeurtenis gegevens in query Store geretourneerd. Er is één rij voor elke afzonderlijke data base-ID, gebruikers-ID, query-ID en gebeurtenis.

|**Name**|  **Type**|   **Referentie**| **Beschrijving**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |OID van de gebruiker die de instructie heeft uitgevoerd|
|db_id  |oid    |pg_database.oid    |De OID van de Data Base waarin de instructie is uitgevoerd|
|query_id   |bigint     ||Interne hash-code, berekend op basis van de ontledings structuur van de instructie|
|event_type |text       ||Het type gebeurtenis waarvoor de back-end wacht|
|gebeurtenis  |text       ||De wacht gebeurtenis naam als de back-end momenteel wacht|
|oproepen  |Integer        ||Nummer van dezelfde gebeurtenis vastgelegd|


### <a name="functions"></a>Functies
Query_store.qs_reset() returns void

`qs_reset` Hiermee worden alle statistische gegevens verwijderd die tot nu toe zijn verzameld door de query Store. Deze functie kan alleen worden uitgevoerd door de rol Server beheerder.

Query_store.staging_data_reset() returns void

`staging_data_reset` Hiermee worden alle statistieken die in het geheugen zijn verzameld, verwijderd door de query Store (dat wil zeggen, de gegevens in het geheugen die nog niet zijn leeg gemaakt voor de data base). Deze functie kan alleen worden uitgevoerd door de rol Server beheerder.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
- Als een PostgreSQL-server de para meter default_transaction_read_only heeft in, kan de query Store geen gegevens vastleggen.
- De functionaliteit van het query archief kan worden onderbroken als er lange Unicode-query's worden aangetroffen (> = 6000 bytes).
- [Replica's lezen](concepts-read-replicas.md) query's opslaan gegevens van de hoofd server repliceren. Dit betekent dat het query archief van een lees replica geen statistieken biedt over query's die worden uitgevoerd op de Lees replica.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [scenario's waarin query Store bijzonder nuttig kan zijn](concepts-query-store-scenarios.md).
- Meer informatie over [Best practices voor het gebruik van query Store](concepts-query-store-best-practices.md).
