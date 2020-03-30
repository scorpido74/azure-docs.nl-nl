---
title: Phoenix-prestaties in Azure HDInsight
description: Aanbevolen procedures om de prestaties van Apache Phoenix voor Azure HDInsight-clusters te optimaliseren
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552641"
---
# <a name="apache-phoenix-performance-best-practices"></a>Best practices voor Apache Phoenix-prestaties

Het belangrijkste aspect van [apache Phoenix](https://phoenix.apache.org/) prestaties is het optimaliseren van de onderliggende [Apache HBase](https://hbase.apache.org/). Phoenix maakt een relationeel gegevensmodel bovenop HBase dat SQL-query's omzet in HBase-bewerkingen, zoals scans. Het ontwerp van uw tabelschema, de selectie en volgorde van de velden in uw primaire sleutel en het gebruik van indexen hebben allemaal invloed op de prestaties van Phoenix.

## <a name="table-schema-design"></a>Ontwerp van tabelschema

Wanneer u een tabel maakt in Phoenix, wordt die tabel opgeslagen in een HBase-tabel. De Tabel HBase bevat groepen kolommen (kolomfamilies) die samen worden geopend. Een rij in de tabel Phoenix is een rij in de tabel HBase, waar elke rij bestaat uit versies van cellen die zijn gekoppeld aan een of meer kolommen. Logischerwijs is een enkele HBase-rij een verzameling sleutelwaardeparen, die elk dezelfde rijsleutelwaarde hebben. Dat wil zeggen dat elk sleutelpaar een rijsleutelkenmerk heeft en de waarde van dat kenmerk van de rijsleutel hetzelfde is voor een bepaalde rij.

Het schemaontwerp van een Phoenix-tabel bevat het primaire sleutelontwerp, het ontwerp van de kolomfamilie, het afzonderlijke kolomontwerp en de manier waarop de gegevens worden verdeeld.

### <a name="primary-key-design"></a>Primair sleutelontwerp

De primaire sleutel die is gedefinieerd op een tabel in Phoenix bepaalt hoe gegevens worden opgeslagen in de rijsleutel van de onderliggende HBase-tabel. In HBase is de enige manier om toegang te krijgen tot een bepaalde rij met de rijsleutel. Bovendien worden gegevens die zijn opgeslagen in een HBase-tabel gesorteerd op de rijsleutel. Phoenix bouwt de waarde van de rijsleutel door de waarden van elk van de kolommen in de rij samen te stellen in de volgorde waarin ze zijn gedefinieerd in de primaire sleutel.

Een tabel voor contactpersonen heeft bijvoorbeeld de voornaam, achternaam, telefoonnummer en adres, allemaal in dezelfde kolomfamilie. U een primaire sleutel definiëren op basis van een toenemend volgnummer:

|rijsleutel|       adres|   telefoon| voornaam| achternaam|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji Raji|

Als u echter vaak op lastName vraagt, presteert deze primaire sleutel mogelijk niet goed, omdat voor elke query een volledige tabelscan nodig is om de waarde van elke lastName te lezen. In plaats daarvan u een primaire sleutel definiëren op de kolommen achternaam, firstName en sofinummer. Deze laatste kolom is om twee bewoners te disambiguate op hetzelfde adres met dezelfde naam, zoals een vader en zoon.

|rijsleutel|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji Raji| 222 |

Met deze nieuwe primaire sleutel zouden de rijsleutels die door Phoenix worden gegenereerd:

|rijsleutel|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvijn-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji Raji| 222 |

In de eerste rij hierboven worden de gegevens voor de rijsleutel weergegeven zoals weergegeven:

|rijsleutel|       sleutel|   waarde|
|------|--------------------|---|
|  Dole-John-111|adres |1111 San Gabriel Dr.|  
|  Dole-John-111|telefoon |1-425-000-0002|  
|  Dole-John-111|voornaam |John|  
|  Dole-John-111|achternaam |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Deze rijsleutel slaat nu een dubbele kopie van de gegevens op. Houd rekening met de grootte en het aantal kolommen dat u in uw primaire sleutel opneemt, omdat deze waarde bij elke cel in de onderliggende HBase-tabel is opgenomen.

Als de primaire sleutel waarden heeft die monotonisch toenemen, moet u de tabel met *zoutemmers* maken om te voorkomen dat schrijfhotspots worden gemaakt - zie [Partitiegegevens](#partition-data).

### <a name="column-family-design"></a>Het familieontwerp van de kolom

Als sommige kolommen vaker worden geopend dan andere, moet u meerdere kolomfamilies maken om de vaak geopende kolommen te scheiden van zelden geopende kolommen.

Als bepaalde kolommen meestal samen worden geopend, plaatst u deze kolommen ook in dezelfde kolomfamilie.

### <a name="column-design"></a>Kolomontwerp

* Bewaar VARCHAR-kolommen onder ongeveer 1 MB vanwege de I/O-kosten van grote kolommen. Bij het verwerken van query's materialiseert HBase cellen volledig voordat ze naar de client worden verzonden, en de client ontvangt ze volledig voordat deze worden overgedragen aan de toepassingscode.
* Sla kolomwaarden op met een compact formaat zoals protobuf, Avro, msgpack of BSON. JSON wordt niet aanbevolen, omdat het groter is.
* Overweeg gegevens te comprimeren voordat ze worden opgeslagen om de latentie- en I/O-kosten te verlagen.

### <a name="partition-data"></a>Partitiegegevens

Met Phoenix u het aantal regio's bepalen waar uw gegevens worden gedistribueerd, wat de lees-/schrijfprestaties aanzienlijk kan verhogen. Wanneer u een Phoenix-tabel maakt, u uw gegevens zouten of vooraf splitsen.

Als u een tabel tijdens het maken wilt zoutmaken, geeft u het aantal zoutemmers op:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Deze zouten splitst de tabel langs de waarden van primaire toetsen en kiest de waarden automatisch. 

Als u wilt bepalen waar de tabelsplitsingen plaatsvinden, u de tabel vooraf splitsen door de bereikwaarden op te geven waarlangs de splitsing plaatsvindt. Als u bijvoorbeeld een tabel wilt maken die wordt gesplitst langs drie regio's:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Indexontwerp

Een Phoenix-index is een HBase-tabel die een kopie van sommige of alle gegevens uit de geïndexeerde tabel opslaat. Een index verbetert de prestaties voor specifieke typen query's.

Wanneer u meerdere indexen hebt gedefinieerd en vervolgens een tabel opvraagt, selecteert Phoenix automatisch de beste index voor de query. De primaire index wordt automatisch gemaakt op basis van de primaire toetsen die u selecteert.

Voor verwachte query's u ook secundaire indexen maken door hun kolommen op te geven.

Bij het ontwerpen van uw indexen:

* Maak alleen de indexen die u nodig hebt.
* Beperk het aantal indexen op veelgebruikte tabellen. Updates van een tabel vertalen zich in schrijft naar zowel de hoofdtabel als de indextabellen.

## <a name="create-secondary-indexes"></a>Secundaire indexen maken

Secundaire indexen kunnen de leesprestaties verbeteren door wat een volledige tabelscan zou zijn om te zetten in een puntlookup, ten koste van opslagruimte en schrijfsnelheid. Secundaire indexen kunnen worden toegevoegd of verwijderd na het maken van de tabel en vereisen geen wijzigingen in bestaande query's - query's worden gewoon sneller uitgevoerd. Afhankelijk van uw behoeften u overwegen gedekte indexen, functionele indexen of beide te maken.

### <a name="use-covered-indexes"></a>Gedekte indexen gebruiken

Gedekte indexen zijn indexen die gegevens uit de rij bevatten naast de waarden die worden geïndexeerd. Na het vinden van de gewenste indexvermelding, is er geen noodzaak om toegang te krijgen tot de primaire tabel.

In de tabel Met voorbeeld contact u bijvoorbeeld een secundaire index maken op alleen de kolom socialSecurityNum. Deze secundaire index zou query's versnellen die filteren op socialSecurityNum-waarden, maar voor het ophalen van andere veldwaarden moet een andere worden gelezen ten opzichte van de hoofdtabel.

|rijsleutel|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvijn-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji Raji| 222 |

Als u echter meestal de firstName en lastName wilt opzoeken, gezien het socialSecurityNum, u een gedekte index maken die de firstName en lastName als werkelijke gegevens in de indextabel bevat:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Met deze gedekte index kan de volgende query alle gegevens verkrijgen door uit de tabel met de secundaire index te lezen:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Functionele indexen gebruiken

Met functionele indexen u een index maken op basis van een willekeurige expressie waarvan u verwacht dat deze in query's wordt gebruikt. Zodra u een functionele index hebt en een query die expressie gebruikt, kan de index worden gebruikt om de resultaten op te halen in plaats van de gegevenstabel.

U bijvoorbeeld een index maken waarmee u hoofdlettergevoelige zoekopdrachten uitvoeren op de gecombineerde voor- en achternaam van een persoon:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Queryontwerp

De belangrijkste overwegingen in queryontwerp zijn:

* Begrijp het queryplan en verifieer het verwachte gedrag.
* Sluit je efficiënt aan.

### <a name="understand-the-query-plan"></a>Het queryplan begrijpen

Gebruik [IN SQLLine](http://sqlline.sourceforge.net/)EXPLAIN, gevolgd door uw SQL-query, om het plan van bewerkingen weer te geven dat Phoenix zal uitvoeren. Controleer of het plan:

* Gebruikt indien nodig uw primaire sleutel.
* Gebruikt geschikte secundaire indexen in plaats van de gegevenstabel.
* Gebruikt waar mogelijk RANGE SCAN of SKIP SCAN, in plaats van TABLE SCAN.

#### <a name="plan-examples"></a>Voorbeelden plannen

Stel bijvoorbeeld dat u een tabel hebt met de naam VLUCHTEN die vluchtvertragingsinformatie opslaat.

Als u alle vluchten wilt `19805`selecteren met een airlineid van , waarbij airlineid een veld is dat niet in de primaire sleutel of in een index staat:

    select * from "FLIGHTS" where airlineid = '19805';

Voer de opdracht uitleggen als volgt uit:

    explain select * from "FLIGHTS" where airlineid = '19805';

Het queryplan ziet er als volgt uit:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Let in dit plan op de zinsnede FULL SCAN OVER FLIGHTS. Deze zin geeft aan dat de uitvoering een tabelscan doet over alle rijen in de tabel, in plaats van de efficiëntere optie SCAN OF SKIP te gebruiken.

Stel dat u op 2 januari 2014 vluchten wilt `AA` aanvragen voor de luchtvaartmaatschappij waar het vluchtcijfer groter was dan 1. Laten we aannemen dat de kolommen jaar, maand, dagvanmaand, luchtvaartmaatschappij en vluchtnum bestaan in de voorbeeldtabel en allemaal deel uitmaken van de samengestelde primaire sleutel. De query ziet er als volgt uit:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Laten we het plan voor deze query bekijken met:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Het resulterende plan is:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

De waarden in vierkante haakjes zijn het waardenbereik voor de primaire toetsen. In dit geval zijn de bereikwaarden vastgesteld met jaar 2014, maand 1 en dag van maand 2, maar staan waarden toe voor flightnum vanaf 2 en omhoog (`*`). Dit queryplan bevestigt dat de primaire sleutel wordt gebruikt zoals verwacht.

Maak vervolgens een index op `carrier2_idx` de tabel VLUCHTEN met de naam die alleen in het veld van de luchtvaartmaatschappij staat. Deze index bevat ook flightdate, tailnum, origin, en flightnum als gedekte kolommen waarvan de gegevens ook in de index zijn opgeslagen.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Stel dat u de luchtvaartmaatschappij mee wilt nemen met de vluchtdatum en de staart, zoals in de volgende query:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

U zou moeten zien dat deze index wordt gebruikt:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Zie de sectie Plannen uitleggen in de [Apache Phoenix Tuning Guide](https://phoenix.apache.org/tuning_guide.html)voor een volledige lijst met items die kunnen worden weergegeven in de resultaten van het plan uitleggen.

### <a name="join-efficiently"></a>Sluit efficiënt aan

Over het algemeen wilt u joins vermijden, tenzij één kant klein is, vooral bij frequente query's.

Indien nodig u grote `/*+ USE_SORT_MERGE_JOIN */` joins doen met de hint, maar een grote join is een dure bewerking over enorme aantallen rijen. Als de totale grootte van alle rechtszijdetabellen het beschikbare `/*+ NO_STAR_JOIN */` geheugen zou overschrijden, gebruikt u de hint.

## <a name="scenarios"></a>Scenario's

De volgende richtlijnen beschrijven enkele veelvoorkomende patronen.

### <a name="read-heavy-workloads"></a>Lees-zware workloads

Voor leeszware use cases moet u controleren of u indexen gebruikt. Als u overhead voor leestijd wilt besparen, u bovendien overwegen gedekte indexen te maken.

### <a name="write-heavy-workloads"></a>Schrijfzware workloads

Voor schrijfzware workloads waarbij de primaire sleutel monotonisch toeneemt, maakt u zoutemmers om schrijfhotspots te voorkomen, ten koste van de algehele leesdoorvoer vanwege de extra scans die nodig zijn. Als u UPSERT gebruikt om een groot aantal records te schrijven, schakelt u autoCommit uit en batcht u de records.

### <a name="bulk-deletes"></a>Bulk verwijdert

Schakel bij het verwijderen van een grote gegevensset autoCommit in voordat u de delete-query uitgeeft, zodat de client de rijsleutels voor alle verwijderde rijen niet hoeft te onthouden. AutoCommit voorkomt dat de client de rijen buffert die door delete zijn beïnvloed, zodat Phoenix ze rechtstreeks op de regioservers kan verwijderen zonder de kosten om ze terug te sturen naar de client.

### <a name="immutable-and-append-only"></a>Onveranderlijk en alleen toevoegen

Als uw scenario de voorkeur geeft aan schrijfsnelheid boven gegevensintegriteit, u overwegen het logboek vooraf te uitschakelen bij het maken van uw tabellen:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Zie [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options)voor meer informatie over deze en andere opties.

## <a name="next-steps"></a>Volgende stappen

* [Apache Phoenix Tuning Gids](https://phoenix.apache.org/tuning_guide.html)
* [Secundaire indexen](https://phoenix.apache.org/secondary_indexing.html)
