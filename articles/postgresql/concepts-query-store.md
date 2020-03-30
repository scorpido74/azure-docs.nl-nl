---
title: Query Store - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt de functie Query Store in Azure Database voor PostgreSQL - Single Server beschreven.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768262"
---
# <a name="monitor-performance-with-the-query-store"></a>Prestaties bewaken met de Query Store

**Geldt voor:** Azure Database voor PostgreSQL - Single Server-versies 9.6, 10, 11

De functie Query Store in Azure Database voor PostgreSQL biedt een manier om queryprestaties in de loop van de tijd bij te houden. Query Store vereenvoudigt het oplossen van prestaties door u te helpen snel de langstlopende en meest resource-intensieve query's te vinden. Query Store legt automatisch een geschiedenis van query's en runtime statistieken vast en behoudt deze voor uw beoordeling. Het scheidt gegevens op tijdvensters, zodat u databasegebruikspatronen zien. Gegevens voor alle gebruikers, databases en query's worden opgeslagen in een database met de naam **azure_sys** in de Instantie Azure Database voor PostgreSQL.

> [!IMPORTANT]
> Wijzig de **azure_sys** database of de schema's ervan niet. Als u dit doet, wordt voorkomen dat querywinkel- en gerelateerde prestatiefuncties correct functioneren.

## <a name="enabling-query-store"></a>Queryarchief inschakelen
Query Store is een opt-in-functie, dus het is niet standaard actief op een server. De winkel is wereldwijd ingeschakeld of uitgeschakeld voor alle databases op een bepaalde server en kan niet per database worden in- of uitgeschakeld.

### <a name="enable-query-store-using-the-azure-portal"></a>Querystore inschakelen met de Azure-portal
1. Meld u aan bij de Azure-portal en selecteer uw Azure-database voor PostgreSQL-server.
2. Selecteer **Serverparameters** in de sectie **Instellingen** van het menu.
3. Zoek naar `pg_qs.query_capture_mode` de parameter.
4. De waarde `TOP` instellen op en **Opslaan**.

Als u wachtstatistieken in schakelt in uw Query Store: 
1. Zoek naar `pgms_wait_sampling.query_capture_mode` de parameter.
1. De waarde `ALL` instellen op en **Opslaan**.


U deze parameters ook instellen met azure cli.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Wacht 20 minuten tot de eerste batch met gegevens is vastgelegd in database azure_sys.

## <a name="information-in-query-store"></a>Informatie in queryarchief
Query Store heeft twee winkels:
- Een runtime stats slaan voor het voortbestaan van de query uitvoering statistieken informatie.
- Een wachten statistieken op te slaan voor aanhoudende wachten statistieken informatie.

Veelvoorkomende scenario's voor het gebruik van queryarchief zijn:
- Het aantal keren dat een query in een bepaald tijdvenster is uitgevoerd
- De gemiddelde uitvoeringstijd van een query over tijdvensters vergelijken om grote delta's te zien
- Het identificeren van langstlopende query's in de afgelopen X-uren
- Top N-query's identificeren die op resources wachten
- De aard van de wachttijd op een bepaalde query begrijpen

Om het ruimtegebruik te minimaliseren, worden de runtime-uitvoeringsstatistieken in het runtime-statistiekenarchief samengevoegd over een vast, configureerbaar tijdvenster. De informatie in deze winkels is zichtbaar door de querystoreweergaven op te vragen.

## <a name="access-query-store-information"></a>Informatie over Access Query Store

Query Store-gegevens worden opgeslagen in de azure_sys database op uw Postgres-server. 

Met de volgende query wordt informatie over query's geretourneerd in het Queryarchief:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Of deze query voor wachtstatistieken:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

U queryopslaggegevens ook verzenden naar [Azure Monitor-logboeken](../azure-monitor/log-query/log-query-overview.md) voor analyses en waarschuwingen, gebeurtenishubs voor streaming en Azure Storage voor archivering. De logboekcategorieën die u wilt configureren zijn **QueryStoreRuntimeStatistics** en **QueryStoreWaitStatistics**. Ga voor meer informatie over de installatie naar het artikel [diagnostische instellingen van Azure Monitor.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="finding-wait-queries"></a>Wachtquery's zoeken
Wachtgebeurtenistypen combineren verschillende wachtgebeurtenissen in buckets door gelijkenis. Query store biedt het type wachtgebeurtenis, de specifieke naam van de wachtgebeurtenis en de query in kwestie. Als u deze wachtinformatie correleren met de queryruntime-statistieken, u een beter inzicht krijgen in wat bijdraagt aan de prestatiekenmerken van query's.

Hier volgen enkele voorbeelden van hoe u meer inzicht krijgen in uw werkbelasting met behulp van de wachtstatistieken in de Query Store:

| **Observatie** | **Actie** |
|---|---|
|High Lock wacht | Controleer de queryteksten voor de betreffende query's en identificeer de doelentiteiten. Zoek in query's naar andere query's die dezelfde entiteit wijzigen, die vaak wordt uitgevoerd en/of een hoge duur hebben. Nadat u deze query's hebt geïdentificeerd, u overwegen de toepassingslogica te wijzigen om de gelijktijdigheid te verbeteren of een minder restrictief isolatieniveau te gebruiken.|
| Hoge Buffer IO wacht | Zoek de query's met een hoog aantal fysieke reads in de Query Store. Als ze overeenkomen met de query's met hoge IO wacht, overwegen de invoering van een index op de onderliggende entiteit, om te doen zoekt in plaats van scans. Dit zou de IO overhead van de query's minimaliseren. Controleer de **prestatieaanbevelingen** voor uw server in de portal om te zien of er indexaanbevelingen voor deze server zijn die de query's optimaliseren.|
| Hoge geheugen wacht | Zoek de belangrijkste geheugenverbruikende query's in het Queryarchief. Deze query's vertragen waarschijnlijk de verdere voortgang van de betreffende query's. Controleer de **prestatieaanbevelingen** voor uw server in de portal om te zien of er indexaanbevelingen zijn die deze query's optimaliseren.|

## <a name="configuration-options"></a>Configuratie-opties
Wanneer Query Store is ingeschakeld, worden gegevens opgeslagen in aggregatievensters van 15 minuten, tot 500 verschillende query's per venster. 

De volgende opties zijn beschikbaar voor het configureren van querywinkelparameters.

| **Parameter** | **Beschrijving** | **Standaard** | **Bereik**|
|---|---|---|---|
| pg_qs.query_capture_mode | Hiermee stelt u in welke instructies worden bijgehouden. | geen | none, top, alle |
| pg_qs.max_query_text_length | Hiermee stelt u de maximale querylengte in die kan worden opgeslagen. Langere query's worden afgekapt. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Hiermee stelt u de bewaartermijn in. | 7 | 1 - 30 |
| pg_qs.track_utility | Hiermee bepaalt u of hulpprogrammaopdrachten worden bijgehouden | op | aan, uit |

De volgende opties zijn specifiek van toepassing op wachtstatistieken.

| **Parameter** | **Beschrijving** | **Standaard** | **Bereik**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Hiermee worden ingesteld welke instructies worden bijgehouden op wachtstatistieken. | geen | none|
| Pgms_wait_sampling.history_period | Stel de frequentie in, in milliseconden, waarop wachtgebeurtenissen worden bemonsterd. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs,query_capture_mode** vervangt **pgms_wait_sampling,query_capture_mode**. Als pg_qs.query_capture_mode GEEN is, heeft de instelling pgms_wait_sampling.query_capture_mode geen effect.


Gebruik de [Azure-portal](howto-configure-server-parameters-using-portal.md) of [Azure CLI](howto-configure-server-parameters-using-cli.md) om een andere waarde voor een parameter te krijgen of in te stellen.

## <a name="views-and-functions"></a>Weergaven en functies
Querystore weergeven en beheren met de volgende weergaven en functies. Iedereen in de openbare postgreSQL-rol kan deze weergaven gebruiken om de gegevens in de Query Store te bekijken. Deze weergaven zijn alleen beschikbaar in de **database azure_sys.**

Query's worden genormaliseerd door te kijken naar hun structuur na het verwijderen van liters en constanten. Als twee query's identiek zijn, behalve voor letterlijke waarden, hebben ze dezelfde hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
In deze weergave worden alle gegevens geretourneerd in het Queryarchief. Er is één rij voor elke afzonderlijke database-id, gebruikersnaam en query-id. 

|**Naam**   |**Type** | **Verwijzingen**  | **Beschrijving**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID van de tabel runtime_stats_entries|
|user_id    |Oid    |pg_authid.oid  |OID van gebruiker die de verklaring uitvoerde|
|db_id  |Oid    |pg_database.oid    |OID van database waarin de verklaring werd uitgevoerd|
|query_id   |bigint  || Interne hashcode, berekend op basis van de parse-structuur van de instructie|
|query_sql_text |Varchar(10000)  || Tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samengevoegd; deze tekst is de tekst voor de eerste query's in het cluster.|
|plan_id    |bigint |   |ID van het plan dat overeenkomt met deze query, nog niet beschikbaar|
|start_time |tijdstempel  ||  Query's worden geaggregeerd door tijdbuckets - de tijdspanne van een bucket is standaard 15 minuten. Dit is de begintijd die overeenkomt met de tijdbucket voor dit item.|
|end_time   |tijdstempel  ||  Eindtijd die overeenkomt met de tijdbucket voor dit item.|
|Oproepen  |bigint  || Aantal keren dat de query is uitgevoerd|
|total_time |dubbele precisie   ||  Totale uitvoeringstijd van query's, in milliseconden|
|min_time   |dubbele precisie   ||  Minimale uitvoeringstijd van query's, in milliseconden|
|max_time   |dubbele precisie   ||  Maximale uitvoeringstijd van query's, in milliseconden|
|mean_time  |dubbele precisie   ||  Gemiddelde uitvoeringstijd van query's, in milliseconden|
|stddev_time|   dubbele precisie    ||  Standaarddeviatie van de queryuitvoeringstijd, in milliseconden |
|Rijen   |bigint ||  Totaal aantal rijen dat door de instructie is opgehaald of beïnvloed|
|shared_blks_hit|   bigint  ||  Totaal aantal treffers in gedeelde blokcache door de instructie|
|shared_blks_read|  bigint  ||  Totaal aantal gedeelde blokken dat door de instructie wordt gelezen|
|shared_blks_dirtied|   bigint   || Totaal aantal gedeelde blokken dat door de verklaring wordt vervuild |
|shared_blks_written|   bigint  ||  Totaal aantal gedeelde blokken geschreven door de verklaring|
|local_blks_hit|    bigint ||   Totaal aantal lokale blokcachehits door de instructie|
|local_blks_read|   bigint   || Totaal aantal lokale blokken dat door de instructie wordt gelezen|
|local_blks_dirtied|    bigint  ||  Totaal aantal lokale blokken vuil door de verklaring|
|local_blks_written|    bigint  ||  Totaal aantal lokale blokken geschreven door de verklaring|
|temp_blks_read |bigint  || Totaal aantal tijdelijke blokken dat door de instructie wordt gelezen|
|temp_blks_written| bigint   || Totaal aantal tijdelijke blokken geschreven door de verklaring|
|blk_read_time  |dubbele precisie    || Totale tijd dat de instructie leesblokken heeft besteed in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
|blk_write_time |dubbele precisie    || Totale tijd dat de instructie heeft besteed aan het schrijven van blokken, in milliseconden (als track_io_timing is ingeschakeld, anders nul)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
In deze weergave worden querytekstgegevens geretourneerd in het Queryarchief. Er is één rij voor elk afzonderlijk query_text.

|**Naam**|  **Type**|   **Beschrijving**|
|---|---|---|
|query_text_id  |bigint     |ID voor de query_texts tabel|
|query_sql_text |Varchar(10000)     |Tekst van een representatieve verklaring. Verschillende query's met dezelfde structuur worden samengevoegd; deze tekst is de tekst voor de eerste query's in het cluster.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
In deze weergave worden wachtgebeurtenissen geretourneerd in het Queryarchief. Er is één rij voor elke afzonderlijke database-id, gebruikersnaam, query-id en gebeurtenis.

|**Naam**|  **Type**|   **Verwijzingen**| **Beschrijving**|
|---|---|---|---|
|user_id    |Oid    |pg_authid.oid  |OID van gebruiker die de verklaring uitvoerde|
|db_id  |Oid    |pg_database.oid    |OID van database waarin de verklaring werd uitgevoerd|
|query_id   |bigint     ||Interne hashcode, berekend op basis van de parse-structuur van de instructie|
|event_type |tekst       ||Het type gebeurtenis waarvoor de backend wacht|
|Gebeurtenis  |tekst       ||De naam van de wachtgebeurtenis als de backend momenteel wacht|
|Oproepen  |Geheel getal        ||Aantal van dezelfde gebeurtenis dat is vastgelegd|


### <a name="functions"></a>Functions
Query_store.qs_reset() retourneert ongeldig

`qs_reset` hiermee worden alle statistieken die tot nu toe door de Query Store zijn verzameld, verwijderd. Deze functie kan alleen worden uitgevoerd door de serverbeheerderrol.

Query_store.staging_data_reset() retourneert ongeldig

`staging_data_reset` hiermee worden alle statistieken die in het geheugen zijn verzameld door de Query Store (dat wil zeggen de gegevens in het geheugen die nog niet naar de database zijn gespoeld) verwijderd. Deze functie kan alleen worden uitgevoerd door de serverbeheerderrol.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen
- Als een PostgreSQL-server de parameter default_transaction_read_only heeft, kan Query Store geen gegevens vastleggen.
- De functionaliteit van de Query Store kan worden onderbroken als er lange Unicode-query's (>= 6000 bytes) worden aangelopen.
- [Lees replica's](concepts-read-replicas.md) repliceren Query Store-gegevens van de hoofdserver. Dit betekent dat de querywinkel van een gelezen replica geen statistieken biedt over query's die op de gelezen replica worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [scenario's waarin queryarchief bijzonder nuttig kan zijn.](concepts-query-store-scenarios.md)
- Meer informatie over [aanbevolen procedures voor het gebruik van de Query Store](concepts-query-store-best-practices.md).
