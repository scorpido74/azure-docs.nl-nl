---
title: Query Store - Azure Database voor MySQL
description: Meer informatie over de functie Query Store in Azure Database voor MySQL om u te helpen de prestaties in de loop van de tijd bij te houden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537037"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Azure-database controleren op de prestaties van MySQL met Query Store

**Geldt voor:** Azure-database voor MySQL 5.7

De functie Query Store in Azure Database voor MySQL biedt een manier om queryprestaties in de loop van de tijd bij te houden. Query Store vereenvoudigt het oplossen van prestaties door u te helpen snel de langstlopende en meest resource-intensieve query's te vinden. Query Store legt automatisch een geschiedenis van query's en runtime statistieken vast en behoudt deze voor uw beoordeling. Het scheidt gegevens op tijdvensters, zodat u databasegebruikspatronen zien. Gegevens voor alle gebruikers, databases en query's worden opgeslagen in de **mysql-schemadatabase** in de Azure Database voor MySQL-instantie.

## <a name="common-scenarios-for-using-query-store"></a>Veelvoorkomende scenario's voor het gebruik van queryarchief

Queryarchief kan worden gebruikt in een aantal scenario's, waaronder het volgende:

- Opnieuw gevallen query's detecteren
- Het aantal keren dat een query in een bepaald tijdvenster is uitgevoerd
- De gemiddelde uitvoeringstijd van een query over tijdvensters vergelijken om grote delta's te zien

## <a name="enabling-query-store"></a>Queryarchief inschakelen

Query Store is een opt-in-functie, dus het is niet standaard actief op een server. Het queryarchief is wereldwijd ingeschakeld of uitgeschakeld voor alle databases op een bepaalde server en kan niet per database worden in- of uitgeschakeld.

### <a name="enable-query-store-using-the-azure-portal"></a>Querystore inschakelen met de Azure-portal

1. Meld u aan bij de Azure-portal en selecteer uw Azure-database voor MySQL-server.
1. Selecteer **Serverparameters** in de sectie **Instellingen** van het menu.
1. Zoek naar de parameter query_store_capture_mode.
1. Stel de waarde in op ALLES en **Opslaan**.

Als u wachtstatistieken in schakelt in uw Query Store:

1. Zoek naar de parameter query_store_wait_sampling_capture_mode.
1. Stel de waarde in op ALLES en **Opslaan**.

Laat maximaal 20 minuten toe dat de eerste batch gegevens in de mysql-database blijft bestaan.

## <a name="information-in-query-store"></a>Informatie in queryarchief

Query Store heeft twee winkels:

- Een runtime-statistieken slaan op voor het voortbestaan van de informatie over queryuitvoeringsstatistieken.
- Een wachten statistieken op te slaan voor aanhoudende wachten statistieken informatie.

Om het ruimtegebruik te minimaliseren, worden de runtime-uitvoeringsstatistieken in het runtime-statistiekenarchief samengevoegd over een vast, configureerbaar tijdvenster. De informatie in deze winkels is zichtbaar door de querystoreweergaven op te vragen.

Met de volgende query wordt informatie over query's geretourneerd in het Queryarchief:

```sql
SELECT * FROM mysql.query_store;
```

Of deze query voor wachtstatistieken:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Wachtquery's zoeken

> [!NOTE]
> Wachtstatistieken mogen niet worden ingeschakeld tijdens piekwerkuren of voor onbepaalde tijd worden ingeschakeld voor gevoelige workloads. <br>Voor workloads die met een hoog CPU-gebruik of op servers zijn geconfigureerd met lagere vCores, moet u voorzichtig zijn bij het inschakelen van wachtstatistieken. Het moet niet voor onbepaalde tijd worden ingeschakeld. 

Wachtgebeurtenistypen combineren verschillende wachtgebeurtenissen in buckets door gelijkenis. Query store biedt het type wachtgebeurtenis, de specifieke naam van de wachtgebeurtenis en de query in kwestie. Als u deze wachtinformatie correleren met de queryruntime-statistieken, u een beter inzicht krijgen in wat bijdraagt aan de prestatiekenmerken van query's.

Hier volgen enkele voorbeelden van hoe u meer inzicht krijgen in uw werkbelasting met behulp van de wachtstatistieken in de Query Store:

| **Observatie** | **Actie** |
|---|---|
|High Lock wacht | Controleer de queryteksten voor de betreffende query's en identificeer de doelentiteiten. Zoek in query's naar andere query's die dezelfde entiteit wijzigen, die vaak wordt uitgevoerd en/of een hoge duur hebben. Nadat u deze query's hebt geïdentificeerd, u overwegen de toepassingslogica te wijzigen om de gelijktijdigheid te verbeteren of een minder restrictief isolatieniveau te gebruiken. |
|Hoge Buffer IO wacht | Zoek de query's met een hoog aantal fysieke reads in de Query Store. Als ze overeenkomen met de query's met hoge IO wacht, overwegen de invoering van een index op de onderliggende entiteit, te doen zoekt in plaats van scans. Dit zou de IO overhead van de query's minimaliseren. Controleer de **prestatieaanbevelingen** voor uw server in de portal om te zien of er indexaanbevelingen voor deze server zijn die de query's optimaliseren. |
|Hoge geheugen wacht | Zoek de belangrijkste geheugenverbruikende query's in het Queryarchief. Deze query's vertragen waarschijnlijk de verdere voortgang van de betreffende query's. Controleer de **prestatieaanbevelingen** voor uw server in de portal om te zien of er indexaanbevelingen zijn die deze query's optimaliseren.|

## <a name="configuration-options"></a>Configuratie-opties

Wanneer Query Store is ingeschakeld, worden gegevens opgeslagen in aggregatievensters van 15 minuten, tot 500 verschillende query's per venster.

De volgende opties zijn beschikbaar voor het configureren van querywinkelparameters.

| **Parameter** | **Beschrijving** | **Standaard** | **Bereik** |
|---|---|---|---|
| query_store_capture_mode | Schakel de functie AAN/UIT voor queryopslag in op basis van de waarde. Opmerking: Als performance_schema is uitgeschakeld, schakelt u query_store_capture_mode in performance_schema en een subset van prestatieschema-instrumenten die nodig zijn voor deze functie in. | ALLE | NONE |
| query_store_capture_interval | Het opnameinterval voor queryopslag in enkele minuten. Hiermee u het interval opgeven waarin de querystatistieken worden samengevoegd | 15 | 5 - 60 |
| query_store_capture_utility_queries | In- of uitschakelen om alle hulpprogrammaquery's vast te leggen die in het systeem worden uitgevoerd. | NO | JA, NEE. |
| query_store_retention_period_in_days | Tijdvenster in dagen om de gegevens in het queryarchief te bewaren. | 7 | 1 - 30 |

De volgende opties zijn specifiek van toepassing op wachtstatistieken.

| **Parameter** | **Beschrijving** | **Standaard** | **Bereik** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Hiermee u de wachtstatistieken in- en uitschakelen. | Geen | NONE |
| query_store_wait_sampling_frequency | Wijzigt de frequentie van de wait-sampling in seconden. 5 tot 300 seconden. | 30 | 5-300 |

> [!NOTE]
> Momenteel **query_store_capture_mode** vervangt deze configuratie, wat betekent dat zowel **query_store_capture_mode** en **query_store_wait_sampling_capture_mode** moeten worden ingeschakeld om alle voor wachtstatistieken te werken. Als **query_store_capture_mode** is uitgeschakeld, worden wachtstatistieken ook uitgeschakeld, omdat wachtstatistieken de performance_schema ingeschakeld en de query_text die zijn vastgelegd door queryopslag.

Gebruik de [Azure-portal](howto-server-parameters.md) of [Azure CLI](howto-configure-server-parameters-using-cli.md) om een andere waarde voor een parameter te krijgen of in te stellen.

## <a name="views-and-functions"></a>Weergaven en functies

Querystore weergeven en beheren met de volgende weergaven en functies. Iedereen in de [openbare rol met geselecteerde bevoegdheden](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) kan deze weergaven gebruiken om de gegevens in het Queryarchief weer te geven. Deze weergaven zijn alleen beschikbaar in de **mysql-database.**

Query's worden genormaliseerd door te kijken naar hun structuur na het verwijderen van liters en constanten. Als twee query's identiek zijn, behalve voor letterlijke waarden, hebben ze dezelfde hash.

### <a name="mysqlquery_store"></a>mysql.query_store

In deze weergave worden alle gegevens geretourneerd in het Queryarchief. Er is één rij voor elke afzonderlijke database-id, gebruikersnaam en query-id.

| **Naam** | **Gegevenstype** | **IS_NULLABLE** | **Beschrijving** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Naam van het schema |
| `query_id`| bigint(20) | NO| Unieke id gegenereerd voor de specifieke query, als dezelfde query wordt uitgevoerd in verschillende schema's, zal een nieuwe ID worden gegenereerd |
| `timestamp_id` | tijdstempel| NO| Tijdstempel waarin de query wordt uitgevoerd. Dit is gebaseerd op de query_store_interval configuratie|
| `query_digest_text`| Longtext| NO| De genormaliseerde querytekst na het verwijderen van alle literalen|
| `query_sample_text` | Longtext| NO| Eerste weergave van de werkelijke query met literalen|
| `query_digest_truncated` | bit| JA| Of de querytekst is afgekapt. Waarde is Ja als de query langer is dan 1 KB|
| `execution_count` | bigint(20)| NO| Het aantal keren dat de query is uitgevoerd voor deze tijdstempel-id / tijdens de geconfigureerde intervalperiode|
| `warning_count` | bigint(20)| NO| Aantal waarschuwingen die deze query genereert tijdens de interne|
| `error_count` | bigint(20)| NO| Aantal fouten dat deze query tijdens het interval heeft gegenereerd|
| `sum_timer_wait` | double| JA| Totale uitvoeringstijd van deze query tijdens het interval|
| `avg_timer_wait` | double| JA| Gemiddelde uitvoeringstijd voor deze query tijdens het interval|
| `min_timer_wait` | double| JA| Minimale uitvoeringstijd voor deze query|
| `max_timer_wait` | double| JA| Maximale uitvoeringstijd|
| `sum_lock_time` | bigint(20)| NO| Totale hoeveelheid tijd die is besteed aan alle vergrendelingen voor deze queryuitvoering tijdens dit tijdvenster|
| `sum_rows_affected` | bigint(20)| NO| Aantal getroffen rijen|
| `sum_rows_sent` | bigint(20)| NO| Aantal rijen dat naar client wordt verzonden|
| `sum_rows_examined` | bigint(20)| NO| Aantal onderzochte rijen|
| `sum_select_full_join` | bigint(20)| NO| Aantal volledige joins|
| `sum_select_scan` | bigint(20)| NO| Aantal geselecteerde scans |
| `sum_sort_rows` | bigint(20)| NO| Aantal gesorteerde rijen|
| `sum_no_index_used` | bigint(20)| NO| Aantal keren dat de query geen indexen heeft gebruikt|
| `sum_no_good_index_used` | bigint(20)| NO| Aantal keren dat de queryuitvoeringsengine geen goede indexen heeft gebruikt|
| `sum_created_tmp_tables` | bigint(20)| NO| Totaal aantal tijdelijke tabellen gemaakt|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Totaal aantal tijdelijke tabellen dat in schijf is gemaakt (genereert I/O)|
| `first_seen` | tijdstempel| NO| De eerste gebeurtenis (UTC) van de query tijdens het aggregatievenster|
| `last_seen` | tijdstempel| NO| De laatste gebeurtenis (UTC) van de query tijdens dit aggregatievenster|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

In deze weergave worden wachtgebeurtenissen geretourneerd in het Queryarchief. Er is één rij voor elke afzonderlijke database-id, gebruikersnaam, query-id en gebeurtenis.

| **Naam**| **Gegevenstype** | **IS_NULLABLE** | **Beschrijving** |
|---|---|---|---|
| `interval_start` | tijdstempel | NO| Begin van het interval (15 minuten stappen)|
| `interval_end` | tijdstempel | NO| Einde van het interval (15 minuten toename)|
| `query_id` | bigint(20) | NO| Gegenereerde unieke id op de genormaliseerde query (uit queryopslag)|
| `query_digest_id` | varchar(32) | NO| De genormaliseerde querytekst na het verwijderen van alle literalen (uit het queryarchief) |
| `query_digest_text` | Longtext | NO| Eerste weergave van de werkelijke query met literalen (uit queryopslag) |
| `event_type` | varchar(32) | NO| Categorie van het wachtevenement |
| `event_name` | varchar(128) | NO| Naam van de wachtgebeurtenis |
| `count_star` | bigint(20) | NO| Aantal wachtgebeurtenissen dat tijdens het interval voor de query is bemonsterd |
| `sum_timer_wait_ms` | double | NO| Totale wachttijd (in milliseconden) van deze query tijdens het interval |

### <a name="functions"></a>Functions

| **Naam**| **Beschrijving** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Alle gegevens van het queryarchief voor de opgegeven tijdstempel verwijderen |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Zuivert alle gebeurtenisgegevens wachten vóór de gegeven tijdstempel |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Zuiveringen aanbevelingen waarvan de vervaldatum is vóór de gegeven tijdstempel |

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

- Als de parameter `default_transaction_read_only` van een MySQL-server is ingeschakeld, kan Query Store geen gegevens vastleggen.
- De functionaliteit van de Query Store kan worden\>onderbroken als er lange Unicode-query's ( = 6000 bytes) worden ontvangen.
- De bewaartermijn voor wachtstatistieken is 24 uur.
- Wachtstatistieken gebruiken een voorbeeld om een fractie van gebeurtenissen vast te leggen. De frequentie kan worden `query_store_wait_sampling_frequency`gewijzigd met behulp van de parameter .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [inzichten in queryprestaties](concepts-query-performance-insight.md)
