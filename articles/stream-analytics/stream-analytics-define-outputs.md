---
title: Meer informatie over de uitvoer van Azure Stream Analytics
description: In dit artikel worden opties voor gegevens uitvoer beschreven die beschikbaar zijn in Azure Stream Analytics, met inbegrip van Power BI voor analyse resultaten.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475228"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Meer informatie over de uitvoer van Azure Stream Analytics

In dit artikel worden de typen uitvoer beschreven die beschikbaar zijn voor een Azure Stream Analytics taak. Met uitvoer kunt u de resultaten van de Stream Analytics-taak opslaan en opslaan. Door de uitvoer gegevens te gebruiken, kunt u verdere Business Analytics en gegevens opslag van uw gegevens uitvoeren.

Wanneer u uw Stream Analytics query ontwerpt, raadpleegt u de naam van de uitvoer met behulp van de [component into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). U kunt één uitvoer per taak of meerdere uitvoer per streaming-taak gebruiken (als u deze nodig hebt) door meerdere componenten in de query op te geven.

Als u Stream Analytics taak uitvoer wilt maken, bewerken en testen, kunt u de [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)en [Visual Studio](stream-analytics-quick-create-vs.md)gebruiken.

Sommige typen uitvoer ondersteunen het [partitioneren van partities](#partitioning). De grootte van de [uitvoer batch](#output-batch-size) is afhankelijk van de door voer te optimaliseren.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage gen 1

Stream Analytics ondersteunt [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage is een grootschalige-opslag plaats voor het hele bedrijf voor big data analytische werk belastingen. U kunt Data Lake Storage gebruiken voor het opslaan van gegevens van elke grootte, type en opname snelheid voor operationele en experimentele analyses. Stream Analytics moet worden gemachtigd om toegang te krijgen tot Data Lake Storage.

Azure Data Lake Storage uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Data Lake Storage gen 1-uitvoer te configureren.   

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query om te leiden naar Data Lake Store. |
| Abonnement | Het abonnement met uw Azure Data Lake Storage-account. |
| Accountnaam | De naam van het Data Lake Store-account waarnaar u uw uitvoer wilt verzenden. Er wordt een vervolg keuzelijst weer gegeven met Data Lake Store accounts die beschikbaar zijn in uw abonnement. |
| Patroon voor voegsel van pad | Het bestandspad dat wordt gebruikt voor het schrijven van uw bestanden binnen het opgegeven Data Lake Store-account. U kunt een of meer exemplaren van de variabelen {date} en {time} opgeven:<br /><ul><li>Voor beeld 1: Map1/logboeken/{date}/{time}</li><li>Voor beeld 2: Map1/logboeken/{date}</li></ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd.<br /><br />Als het patroon van het bestandspad geen afsluitende slash (/) bevat, wordt het laatste patroon in het bestandspad behandeld als een voor voegsel van de bestands naam. <br /><br />In deze omstandigheden worden nieuwe bestanden gemaakt:<ul><li>Wijziging in uitvoer schema</li><li>Externe of interne herstart van een taak</li></ul> |
| Datum notatie | Optioneel. Als het datum token wordt gebruikt in het voorvoegsel pad, kunt u de datum notatie selecteren waarin uw bestanden zijn ingedeeld. Voor beeld: JJJJ/MM/DD |
|Tijd notatie | Optioneel. Als de time-token wordt gebruikt in het pad van het voor voegsel, geeft u de tijd notatie op waarin uw bestanden zijn geordend. Op dit moment is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund.|
| Encoding | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund.|
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidings tekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep.|
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden**selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn.  **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijd venster. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven.|
| Verificatiemodus | U kunt toegang tot uw Data Lake Storage-account verlenen met behulp van [beheerde identiteits](stream-analytics-managed-identities-adls.md) -of gebruikers token. Nadat u toegang hebt verleend, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de Data Lake Storage uitvoer voor deze taak te verwijderen of de Stream Analytics-taak te verwijderen. |

## <a name="sql-database"></a>SQL Database

U kunt [Azure SQL database](https://azure.microsoft.com/services/sql-database/) gebruiken als uitvoer voor gegevens die relationeel zijn of voor toepassingen die afhankelijk zijn van inhoud die wordt gehost in een relationele data base. Stream Analytics-taken worden geschreven naar een bestaande tabel in SQL Database. Het tabel schema moet exact overeenkomen met de velden en de bijbehorende typen in de uitvoer van uw taak. U kunt ook [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) als uitvoer opgeven via de optie SQL database uitvoer. Zie het artikel [Stream Analytics met Azure SQL database als uitvoer als](stream-analytics-sql-output-perf.md) u meer wilt weten over manieren om de schrijf doorvoer te verbeteren.

U kunt ook [Azure SQL database beheerde instantie](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) gebruiken als uitvoer. U moet een [openbaar eind punt configureren in Azure SQL database Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) en vervolgens hand matig de volgende instellingen configureren in azure stream Analytics. Een virtuele Azure-machine met SQL Server met een gekoppelde data base wordt ook ondersteund door de onderstaande instellingen hand matig te configureren.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een SQL Database uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze data base te sturen. |
| Database | De naam van de Data Base waarin u uw uitvoer wilt verzenden. |
| Servernaam | De naam van de SQL Database-Server. Voor Azure SQL Database beheerde instantie moet het poort 3342 worden opgegeven. Bijvoorbeeld *sampleserver. public. data base. Windows. net, 3342* |
| Gebruikersnaam | De gebruikers naam die schrijf toegang tot de data base heeft. Stream Analytics ondersteunt alleen SQL-verificatie. |
| Wachtwoord | Het wacht woord om verbinding te maken met de data base. |
| Tabel | De tabel naam waarin de uitvoer wordt geschreven. De tabel naam is hoofdletter gevoelig. Het schema van deze tabel moet exact overeenkomen met het aantal velden en de bijbehorende typen die uw taak uitvoer genereert. |
|Partitie schema overnemen| Een optie voor het overnemen van het partitie schema van uw vorige query stap, om een volledig parallelle topologie met meerdere schrijvers voor de tabel in te scha kelen. Zie [Azure stream Analytics uitvoer naar Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie.|
|Maximum aantal batches| De aanbevolen bovengrens voor het aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob-opslag en Azure Data Lake Gen2

Data Lake Storage Gen2 maakt Azure Storage de basis voor het bouwen van zakelijke gegevens meren op Azure. Data Lake Storage Gen2 is ontworpen met het starten van meerdere PETA bytes aan gegevens terwijl er honderden gigabits van de door voer worden gehouden, en waarmee u eenvoudig enorme hoeveel heden gegevens kunt beheren. Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een hiërarchische naam ruimte aan Blob Storage.

Azure Blob-opslag biedt een voordelige en schaal bare oplossing voor het opslaan van grote hoeveel heden ongestructureerde gegevens in de Cloud. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor een inleiding in Blob Storage en het gebruik ervan.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een BLOB of ADLS Gen2 uitvoer.

| Naam van eigenschap       | Beschrijving                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Uitvoeralias        | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Blob-opslag te sturen. |
| Storage-account     | De naam van het opslag account waarin u uw uitvoer wilt verzenden.               |
| Sleutel van het opslag account | De geheime sleutel die is gekoppeld aan het opslag account.                              |
| Opslag container   | Een logische groepering voor blobs die zijn opgeslagen in de Azure-Blob service. Wanneer u een BLOB uploadt naar de Blob service, moet u een container voor die BLOB opgeven. |
| Padpatroon | Optioneel. Het patroon van het bestandspad dat wordt gebruikt voor het schrijven van uw blobs binnen de opgegeven container. <br /><br /> In het pad patroon kunt u kiezen voor het gebruik van een of meer exemplaren van de variabelen datum en tijd om de frequentie op te geven waarmee blobs worden geschreven: <br /> {date}, {time} <br /><br />U kunt aangepaste BLOB-partitionering gebruiken om één aangepaste {Field}-naam van uw gebeurtenis gegevens op te geven voor het partitioneren van blobs. De naam van het veld is alfanumeriek en kan spaties, afbreek streepjes en onderstrepings tekens bevatten. De volgende beperkingen zijn van toepassing op aangepaste velden: <ul><li>Veld namen zijn niet hoofdletter gevoelig. De service kan bijvoorbeeld geen onderscheid maken tussen de kolom-ID en de kolom-id.</li><li>Geneste velden zijn niet toegestaan. Gebruik in plaats daarvan een alias in de taak query om het veld af te vlakken.</li><li>Expressies kunnen niet worden gebruikt als veld naam.</li></ul> <br />Met deze functie kunt u het gebruik van aangepaste configuraties voor datum/tijd-indeling in het pad. Aangepaste datum-en tijd notaties moeten een voor een worden opgegeven, omsloten door het sleutel\<woord {DateTime: specificatie>}. Toegestane invoer voor \<de specificatie> zijn jjjj, mm, M, dd, d, hh, H, mm, M, SS of s. Het sleutel woord {\<DateTime: aanduiding>} kan meermaals worden gebruikt in het pad naar aangepaste datum/tijd-configuraties. <br /><br />Voorbeelden: <ul><li>Voor beeld 1: cluster1/logboeken/{date}/{time}</li><li>Voor beeld 2: cluster1/logboeken/{date}</li><li>Voor beeld 3: cluster1/{client_id}/{date}/{time}</li><li>Voor beeld 4: cluster1/{DateTime: SS}/{myField} waarbij de query is: SELECT data. myField AS myField FROM input;</li><li>Voor beeld 5: cluster1/Year = {DateTime: jjjj}/month = {DateTime: MM}/Day = {DateTime: dd}</ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd.<br /><br />Bestands namen gebruiken de volgende Conventie: <br /><br />{Path-voorvoegsel patroon}/schemaHashcode_Guid_Number. extensie<br /><br />Voorbeeld uitvoer bestanden:<ul><li>MYOUTPUT/20170901/00/45434_gguid_1. CSV</li>  <li>MYOUTPUT/20170901/01/45434_gguid_1. CSV</li></ul> <br />Zie [Azure stream Analytics aangepaste BLOB-uitvoer partitionering](stream-analytics-custom-path-patterns-blob-storage-output.md)voor meer informatie over deze functie. |
| Datum notatie | Optioneel. Als het datum token wordt gebruikt in het voorvoegsel pad, kunt u de datum notatie selecteren waarin uw bestanden zijn ingedeeld. Voor beeld: JJJJ/MM/DD |
| Tijd notatie | Optioneel. Als de time-token wordt gebruikt in het pad van het voor voegsel, geeft u de tijd notatie op waarin uw bestanden zijn geordend. Op dit moment is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | Serialisatie-indeling voor uitvoer gegevens. JSON, CSV, AVRO en Parquet worden ondersteund. |
|Minimum aantal rijen (alleen Parquet)|Het aantal minimum rijen per batch. Voor Parquet maakt elke batch een nieuw bestand. De huidige standaard waarde is 2.000 rijen en het toegestane maximum is 10.000 rijen.|
|Maximum tijd (alleen Parquet)|De maximale wacht tijd per batch. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. De huidige standaard waarde is 1 minuut en het toegestane maximum is 2 uur. Als uw BLOB-uitvoer een patroon frequentie voor het pad heeft, kan de wacht tijd niet hoger zijn dan het tijds bereik van de partitie.|
| Encoding    | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund. |
| Scheidingsteken   | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidings tekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling      | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden**selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijd venster. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven. |

Wanneer u Blob Storage als uitvoer gebruikt, wordt er in de volgende gevallen een nieuw bestand in de BLOB gemaakt:

* Als het bestand het maximum aantal toegestane blokken (momenteel 50.000) overschrijdt. U kunt het Maxi maal toegestane aantal blokken bereiken zonder de Maxi maal toegestane Blob-grootte te bereiken. Als de uitvoer frequentie bijvoorbeeld hoog is, kunt u meer bytes per blok weer geven en is de bestands grootte groter. Als de uitvoer frequentie laag is, heeft elk blok minder gegevens en is de bestands grootte kleiner.
* Als er een schema wijziging in de uitvoer is, en de uitvoer indeling vereist een vast schema (CSV en AVRO).
* Als een taak opnieuw wordt gestart, hetzij extern door een gebruiker die deze stopt en start, of intern voor systeem onderhoud of fout herstel.
* Als de query volledig is gepartitioneerd en er een nieuw bestand wordt gemaakt voor elke uitvoer partitie.
* Als de gebruiker een bestand of container van het opslag account verwijdert.
* Als de uitvoer is gepartitioneerd met behulp van het pad voorvoegsel patroon en er wordt een nieuwe BLOB gebruikt wanneer de query naar het volgende uur wordt verplaatst.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld en er een nieuwe BLOB wordt gemaakt per partitie sleutel als deze niet bestaat.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld waarbij de kardinaliteit van de partitie sleutel groter is dan 8.000 en er een nieuwe BLOB wordt gemaakt per partitie sleutel.

## <a name="event-hubs"></a>Event Hubs

De [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/) -service is een zeer schaal bare Event ingestor voor publiceren en abonneren. Het kan miljoenen gebeurtenissen per seconde verzamelen. Een Event Hub als uitvoer wordt gebruikt wanneer de uitvoer van een Stream Analytics taak de invoer van een andere streaming-taak wordt. Zie de sectie [uitvoer Batch grootte](#output-batch-size) voor informatie over de maximale grootte van berichten en het optimaliseren van de Batch grootte.

U hebt een aantal para meters nodig voor het configureren van gegevens stromen van Event hubs als uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Event Hub te sturen. |
| Event hub-naamruimte | Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een Event Hub naam ruimte gemaakt. |
| Naam van Event Hub | De naam van de Event Hub uitvoer. |
| Naam van de Event hub-beleid | Het beleid voor gedeelde toegang, dat u kunt maken op het tabblad **configureren** van de Event hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Event hub-beleids sleutel | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Event Hub. |
| Partitie sleutel kolom | Optioneel. Een kolom die de partitie sleutel voor Event Hub uitvoer bevat. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding | Voor CSV en JSON is UTF-8 de enige coderings indeling die op dit moment wordt ondersteund. |
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden**selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten.  |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

U kunt [Power bi](https://powerbi.microsoft.com/) als uitvoer voor een stream Analytics taak gebruiken om een uitgebreide visualisatie ervaring met analyse resultaten te bieden. U kunt deze mogelijkheid gebruiken voor operationele Dash boards, het genereren van rapporten en het rapporteren van metrische gegevens.

Power BI uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Power BI-uitvoer te configureren.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Geef een beschrijvende naam op die wordt gebruikt in query's om de uitvoer van de query naar deze Power BI uitvoer te sturen. |
| Groeps werkruimte |Als u het delen van gegevens met andere Power BI gebruikers wilt inschakelen, kunt u groepen in uw Power BI account selecteren of **mijn werk ruimte** kiezen als u niet naar een groep wilt schrijven. Als u een bestaande groep wilt bijwerken, moet u de Power BI-verificatie vernieuwen. |
| Naam van de gegevensset |Geef een naam op voor de gegevensset die u voor de Power BI uitvoer wilt gebruiken. |
| Tabelnaam |Geef een tabel naam op onder de gegevensset van de Power BI uitvoer. Power BI uitvoer van Stream Analytics taken kan momenteel slechts één tabel bevatten in een gegevensset. |
| Verbinding machtigen | U moet autoriseren met Power BI om uw uitvoer instellingen te configureren. Zodra u deze uitvoer toegang hebt verleend aan uw Power BI-dash board, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de taak uitvoer te verwijderen of de Stream Analytics-taak te verwijderen. | 

Raadpleeg de zelf studie [Azure stream Analytics en Power bi](stream-analytics-power-bi-dashboard.md) voor een overzicht van het configureren van een Power bi uitvoer en een dash board.

> [!NOTE]
> Maak geen expliciete gegevensset en tabel in het dash board Power BI. De gegevensset en de tabel worden automatisch ingevuld wanneer de taak wordt gestart en de taak pomp uitvoer wordt gestart in Power BI. Als de taak query geen resultaten genereert, worden de gegevensset en tabel niet gemaakt. Als Power BI al een gegevensset en een tabel met dezelfde naam heeft als die in deze Stream Analytics taak, worden de bestaande gegevens overschreven.
>

### <a name="create-a-schema"></a>Een schema maken
Azure Stream Analytics maakt een Power BI gegevensset en tabel schema voor de gebruiker als deze nog niet bestaan. In alle andere gevallen wordt de tabel bijgewerkt met nieuwe waarden. Op dit moment kan slechts één tabel bestaan in een gegevensset. 

Power BI gebruikt het FIFO-Bewaar beleid (First-in, first-out). Gegevens worden verzameld in een tabel totdat er 200.000 rijen zijn gevonden.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converteer een gegevens type van Stream Analytics naar Power BI
Azure Stream Analytics het gegevens model dynamisch bij runtime bijgewerkt als het uitvoer schema wordt gewijzigd. Kolom naam wijzigingen, kolom type wijzigingen en het toevoegen of verwijderen van kolommen worden allemaal bijgehouden.

Deze tabel bevat informatie over de gegevens type conversies van [Stream Analytics gegevens typen](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) Power BI naar de [EDM-typen (Entity Data Model)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), als een Power bi-gegevensset en-tabel niet bestaan.

Van Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar (max) | Tekenreeks
datum/tijd | Datum/tijd
float | Double
Record matrix | Teken reeks type, constante waarde "IRecord" of "IArray"

### <a name="update-the-schema"></a>Het schema bijwerken
Stream Analytics leidt het schema van het gegevens model af op basis van de eerste set gebeurtenissen in de uitvoer. Later, indien nodig, wordt het schema van het gegevens model bijgewerkt om binnenkomende gebeurtenissen te kunnen verwerken die mogelijk niet in het oorspronkelijke schema passen.

Vermijd de `SELECT *` query om dynamische schema-updates voor rijen te voor komen. Naast mogelijke prestatie implicaties kan dit leiden tot een onzekerheid van de tijd die nodig is voor de resultaten. Selecteer de exacte velden die moeten worden weer gegeven op het Power BI dash board. Daarnaast moeten de gegevens waarden voldoen aan het gekozen gegevens type.


Vorige/huidige | Int64 | Tekenreeks | Datum/tijd | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Tekenreeks | Tekenreeks | Double
Double | Double | Tekenreeks | Tekenreeks | Double
Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks 
Datum/tijd | Tekenreeks | Tekenreeks |  Datum/tijd | Tekenreeks

## <a name="table-storage"></a>Table Storage

[Azure Table Storage](../storage/common/storage-introduction.md) biedt een Maxi maal beschik bare, zeer schaal bare opslag, zodat een toepassing automatisch kan worden geschaald om te voldoen aan de vraag van de gebruiker. Table Storage is de NoSQL sleutel/kenmerk opslag van micro soft, die u kunt gebruiken voor gestructureerde gegevens met minder beperkingen voor het schema. Azure-tabel opslag kan worden gebruikt om gegevens op te slaan voor persistentie en efficiënt ophalen.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een tabel uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze tabel opslag te sturen. |
| Storage-account |De naam van het opslag account waarin u uw uitvoer wilt verzenden. |
| Sleutel van het opslag account |De toegangs sleutel die aan het opslag account is gekoppeld. |
| Tabelnaam |De naam van de tabel. De tabel wordt gemaakt als deze nog niet bestaat. |
| Partitiesleutel |De naam van de uitvoer kolom die de partitie sleutel bevat. De partitie sleutel is een unieke id voor de partitie in een tabel die het eerste deel vormt van de primaire sleutel van een entiteit. Dit is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Rij-sleutel |De naam van de uitvoer kolom die de rij sleutel bevat. De rij is een unieke id voor een entiteit binnen een partitie. Het vormt het tweede deel van de primaire sleutel van een entiteit. De rij is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Batchgrootte |Het aantal records voor een batch bewerking. De standaard waarde (100) is voldoende voor de meeste taken. Zie de [tabel batch bewerking spec](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) voor meer informatie over het wijzigen van deze instelling. |

## <a name="service-bus-queues"></a>Service Bus-wachtrijen

[Service Bus wachtrijen](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) bieden een FIFO-bericht levering aan een of meer concurrerende consumenten. Normaal gesp roken worden berichten ontvangen en verwerkt door de ontvangers in de tijdelijke volg orde waarin ze aan de wachtrij zijn toegevoegd. Elk bericht wordt door slechts één bericht verbruiker ontvangen en verwerkt.

In [compatibiliteits niveau 1,2](stream-analytics-compatibility-level.md)maakt Azure stream Analytics gebruik van het [AMQP-berichten Protocol (Advanced Message queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) om te schrijven naar service bus-wacht rijen en-onderwerpen. Met AMQP kunt u platform onafhankelijke, hybride toepassingen bouwen met behulp van een open standaard protocol.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een wachtrij-uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Service Bus wachtrij te sturen. |
| Service Bus naam ruimte |Een container voor een set met bericht entiteiten. |
| Wachtrijnaam |De naam van de Service Bus wachtrij. |
| Naam van wachtrij beleid |Wanneer u een wachtrij maakt, kunt u ook een beleid voor gedeelde toegang maken op het tabblad **configureren** van de wachtrij. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Wachtrij beleids sleutel |De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Service Bus. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Voor CSV en JSON is UTF-8 de enige coderings indeling die op dit moment wordt ondersteund. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling |Alleen van toepassing op JSON-type. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden**selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolom systeem eigenschappen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [systeem eigenschappen voor service bus wachtrij en onderwerp-uitvoer](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Het aantal partities is [gebaseerd op de service bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.

## <a name="service-bus-topics"></a>Service Bus-onderwerpen
Service Bus wachtrijen bieden een een-op-een-communicatie methode van afzender naar ontvanger. [Service Bus onderwerpen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieden een een-op-veel communicatie vorm.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een Service Bus topic-uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar dit Service Bus onderwerp te sturen. |
| Service Bus naam ruimte |Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een Service Bus naam ruimte gemaakt. |
| Onderwerpnaam |Onderwerpen zijn bericht entiteiten, vergelijkbaar met Event hubs en wacht rijen. Ze zijn ontworpen voor het verzamelen van gebeurtenis stromen van apparaten en services. Wanneer een onderwerp wordt gemaakt, wordt er ook een specifieke naam gegeven. De berichten die naar een onderwerp worden verzonden, zijn pas beschikbaar als er een abonnement is gemaakt. Zorg er dus voor dat er een of meer abonnementen zijn onder het onderwerp. |
| Naam van onderwerps beleid |Wanneer u een Service Bus onderwerp maakt, kunt u ook beleid voor gedeelde toegang maken op het tabblad **configureren** van het onderwerp. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Sleutel van het onderwerpbeleid |De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Service Bus. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolom systeem eigenschappen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [systeem eigenschappen voor service bus wachtrij en onderwerp-uitvoer](#system-properties-for-service-bus-queue-and-topic-outputs) |

Het aantal partities is [gebaseerd op de service bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is een wereld wijd gedistribueerde database service die onbeperkte elastische schaal rond de wereld, uitgebreide query en automatische indexering via schema-neutraal gegevens modellen biedt. Zie het artikel [Stream Analytics met Azure Cosmos DB als uitvoer](stream-analytics-documentdb-output.md) voor meer informatie over Azure Cosmos DB container opties voor stream Analytics.

Azure Cosmos DB uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

> [!Note]
> Op dit moment ondersteunt Azure Stream Analytics alleen de verbinding met Azure Cosmos DB met behulp van de SQL-API.
> Andere Azure Cosmos DB Api's worden nog niet ondersteund. Als u Azure Stream Analytics naar de Azure Cosmos DB-accounts die zijn gemaakt met andere Api's, zijn de gegevens mogelijk niet op de juiste manier opgeslagen.

In de volgende tabel worden de eigenschappen beschreven voor het maken van een Azure Cosmos DB uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een alias om deze uitvoer in uw Stream Analytics-query te verwijzen. |
| Sink | Azure Cosmos DB. |
| Importoptie | Kies **Cosmos DB selecteren in uw abonnement** of **Cosmos DB instellingen hand matig opgeven**.
| Account-id | De naam of eind punt-URI van het Azure Cosmos DB-account. |
| Accountsleutel | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account. |
| Database | De naam van de Azure Cosmos DB-Data Base. |
| Containernaam | De container naam die moet worden gebruikt, die voor komt in Cosmos DB. Voorbeeld:  <br /><ul><li> _MyContainer_: er moet een container met de naam ' MyContainer ' bestaan.</li>|
| Document-ID |Optioneel. De naam van het veld in uitvoer gebeurtenissen dat wordt gebruikt om de primaire sleutel op te geven waarop invoeg-of update bewerkingen zijn gebaseerd.

## <a name="azure-functions"></a>Azure Functions
Azure Functions is een serverloze compute-service die u kunt gebruiken om code op aanvraag uit te voeren zonder expliciet een infra structuur in te richten of te beheren. Hiermee kunt u code implementeren die wordt geactiveerd door gebeurtenissen die optreden in azure of partner services. Dankzij deze mogelijkheid van Azure Functions om op Triggers te reageren, is het een natuurlijke uitvoer voor Azure Stream Analytics. Met deze uitvoer adapter kunnen gebruikers Stream Analytics verbinding maken met Azure Functions en een script of stukje code uitvoeren als reactie op verschillende gebeurtenissen.

Azure Functions uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

Azure Stream Analytics roept Azure Functions aan via HTTP-triggers. De Azure Functions uitvoer adapter is beschikbaar met de volgende Configureer bare eigenschappen:

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Function App |De naam van uw Azure Functions-app. |
| Functie |De naam van de functie in uw Azure Functions-app. |
| Sleutel |Als u een Azure-functie van een ander abonnement wilt gebruiken, kunt u dit doen door de sleutel voor toegang tot uw functie te geven. |
| Maximale Batch grootte |Een eigenschap waarmee u de maximale grootte kunt instellen voor elke uitvoer batch die wordt verzonden naar uw Azure-functie. De invoer eenheid bevindt zich in bytes. Deze waarde is standaard 262.144 bytes (256 KB). |
| Maximum aantal batches  |Een eigenschap waarmee u het maximum aantal gebeurtenissen in elke batch kunt opgeven dat naar Azure Functions wordt verzonden. De standaardwaarde is 100. |

Azure Stream Analytics verwacht HTTP-status 200 van de functie-app voor batches die zijn verwerkt.

Wanneer Azure Stream Analytics een uitzonde ring ontvangt van een 413 (' HTTP-aanvraag entiteit te groot ') van een Azure-functie, vermindert deze de grootte van de batches die worden verzonden naar Azure Functions. Gebruik in uw Azure-functie code Deze uitzonde ring om ervoor te zorgen dat Azure Stream Analytics geen grote aantal batches verzendt. Zorg er ook voor dat het maximum aantal batch-en grootte waarden die worden gebruikt in de functie consistent zijn met de waarden die zijn ingevoerd in de Stream Analytics Portal.

> [!NOTE]
> Tijdens een test verbinding stuurt Stream Analytics een lege batch naar Azure Functions om te testen als de verbinding tussen de twee werken. Zorg ervoor dat uw functions-app lege batch-aanvragen verwerkt om ervoor te zorgen dat de test verbinding verloopt.

In een situatie waarin er geen gebeurtenis overloop plaatsvindt in een tijd venster, wordt er ook geen uitvoer gegenereerd. Als gevolg hiervan wordt de functie **computeResult** niet aangeroepen. Dit gedrag is consistent met de ingebouwde statistische functies van het venster.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste meta gegevens eigenschappen voor uitvoer 

U kunt query kolommen als gebruikers eigenschappen aan uw uitgaande berichten toevoegen. Deze kolommen gaan niet naar de payload. De eigenschappen zijn aanwezig in de vorm van een woorden lijst in het uitvoer bericht. *Sleutel* is de kolom naam en- *waarde* is de kolom waarde in de woorden lijst eigenschappen. Alle Stream Analytics gegevens typen worden ondersteund, behalve record en matrix.  

Ondersteunde uitvoer: 
* Service Bus-wachtrij 
* Service Bus-onderwerp 
* Event Hub 

In het volgende voor beeld voegen we de twee `DeviceId` velden `DeviceStatus` en toe aan de meta gegevens. 
* Ophalen`select *, DeviceId, DeviceStatus from iotHubInput`
* Uitvoer configuratie:`DeviceId,DeviceStatus`

![Eigenschappen kolommen](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

De volgende scherm afbeelding ziet u de eigenschappen van uitvoer berichten die zijn geïnspecteerd in EventHub via [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Aangepaste eigenschappen van gebeurtenis](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Systeem eigenschappen voor de Service Bus wachtrij en onderwerp-uitvoer 
U kunt query kolommen als [systeem eigenschappen](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) koppelen aan uw uitgaande service bus-wachtrij of onderwerp-berichten. Deze kolommen worden niet in de payload gezet, maar de bijbehorende [systeem eigenschap](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage wordt gevuld met de query kolom waarden.
Deze systeem eigenschappen worden ondersteund `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Teken reeks waarden van deze kolommen worden geparseerd als overeenkomend type eigenschaps waarde van het systeem en eventuele fouten bij het parseren worden behandeld als gegevens fouten.
Dit veld wordt als een JSON-object indeling gegeven. Meer informatie over deze indeling is als volgt:
* Omgeven door accolades {}.
* Geschreven in sleutel/waarde-paren.
* Sleutels en waarden moeten teken reeksen zijn.
* Sleutel is de naam en waarde van de systeem eigenschap is de kolom naam van de query.
* Sleutels en waarden worden gescheiden door een dubbele punt.
* Elke sleutel/waarde-paar wordt gescheiden door een komma.

Hier ziet u hoe u deze eigenschap gebruikt –

* Ophalen`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolom systeem eigenschappen:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Hiermee stelt u `MessageId` de service bus-wachtrij berichten `column1`in met de waarden en PartitionKey wordt `column2`ingesteld met de waarden.

## <a name="partitioning"></a>Partitionering

De volgende tabel bevat een overzicht van de partitie-ondersteuning en het aantal uitvoer schrijvers voor elk uitvoer type:

| Uitvoer type | Ondersteuning voor partitionering | Partitiesleutel  | Aantal schrijvers van uitvoer |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Gebruik {date} en {time} tokens in het pad voor voegsel patroon. Kies de datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. HH wordt gebruikt voor de tijd notatie. | Volgt de invoer partitionering voor [volledige kan worden opgestart-query's](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ja, moet zijn ingeschakeld. | Op basis van de component PARTITION BY in de query. | Wanneer de optie partitioneren overnemen is ingeschakeld, volgt de invoer partitionering voor [volledige kan worden opgestart-query's](stream-analytics-scale-jobs.md). Zie [Azure stream Analytics output to Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie over het verbeteren van de prestaties van schrijf doorvoer tijdens het laden van gegevens in Azure SQL database. |
| Azure Blob Storage | Ja | Gebruik {date} en {time} tokens uit uw gebeurtenis velden in het pad patroon. Kies de datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. HH wordt gebruikt voor de tijd notatie. BLOB-uitvoer kan worden gepartitioneerd met één aangepast gebeurtenis kenmerk {FieldName} of {datetime\<: specificatie>}. | Volgt de invoer partitionering voor [volledige kan worden opgestart-query's](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ja | Ja | Is afhankelijk van de uitlijning van de partitie.<br /> Wanneer de partitie sleutel voor Event Hub uitvoer gelijk wordt uitgelijnd met de stap upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in Event Hub uitvoer. Elke schrijver maakt gebruik van de [EventHubSender-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) voor het verzenden van gebeurtenissen naar de specifieke partitie. <br /> Wanneer de partitie sleutel voor Event Hub uitvoer niet is afgestemd op de stap van de upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in die vorige stap. Elke schrijver maakt gebruik van de [SendBatchAsync-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** om gebeurtenissen te verzenden naar alle uitvoer partities. |
| Power BI | Nee | Geen | Niet van toepassing. |
| Azure Table Storage | Ja | Een uitvoer kolom.  | Volgt de invoer partitionering voor [volledig parallelle query's](stream-analytics-scale-jobs.md). |
| Azure Service Bus-onderwerp | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in het uitvoer onderwerp.  |
| Azure Service Bus-wachtrij | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in de uitvoer wachtrij. |
| Azure Cosmos DB | Ja | Op basis van de component PARTITION BY in de query. | Volgt de invoer partitionering voor [volledig parallelle query's](stream-analytics-scale-jobs.md). |
| Azure Functions | Ja | Op basis van de component PARTITION BY in de query. | Volgt de invoer partitionering voor [volledig parallelle query's](stream-analytics-scale-jobs.md). |

Het aantal schrijvers van uitvoer kan ook worden beheerd met `INTO <partition count>` behulp van de component (Zie [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) in uw query. Dit kan handig zijn bij het bereiken van een gewenste taak topologie. Als uw uitvoer adapter niet is gepartitioneerd, heeft het ontbreken van gegevens in één invoer partitie tot een vertraging tot de duur van de late aankomst. In dergelijke gevallen wordt de uitvoer samengevoegd met één schrijver, wat kan leiden tot knel punten in de pijp lijn. Zie [Azure stream Analytics overwegingen voor gebeurtenis orders](stream-analytics-out-of-order-and-late-events.md)voor meer informatie over het beleid voor late ontvangst.

## <a name="output-batch-size"></a>Grootte van uitvoer batch
Azure Stream Analytics gebruikt batches van het formaat variable om gebeurtenissen te verwerken en te schrijven naar uitvoer. Normaal gesp roken schrijft de Stream Analytics engine niet één bericht tegelijk en worden batches gebruikt voor efficiëntie. Wanneer het aantal binnenkomende en uitgaande gebeurtenissen hoog is, gebruikt Stream Analytics grotere batches. Wanneer het uitvoer bedrag laag is, worden kleinere batches gebruikt om latentie laag te blijven.

In de volgende tabel worden enkele van de overwegingen voor het uitvoeren van batch verwerking beschreven:

| Uitvoer type |    Maximale bericht grootte | Optimalisatie van Batch grootte |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zie [Data Lake Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Gebruik Maxi maal 4 MB per schrijf bewerking. |
| Azure SQL Database | Configureerbaar met behulp van maximum aantal batches. Maxi maal 10.000 minimale en 100 minimum rijen per bulk invoer standaard.<br />Zie [Azure SQL-limieten](../sql-database/sql-database-resource-limits.md). |  Elke batch wordt in eerste instantie bulksgewijs ingevoegd met een maximum aantal batches. Batch is gesplitst in tweeën (totdat het minimale aantal batches is) op basis van Herhaal bare fouten van SQL. |
| Azure Blob Storage | Zie [Azure Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | De maximale grootte van het BLOB-blok is 4 MB.<br />Het maximum aantal BLOB-Bock is 50.000. |
| Azure Event Hubs    | 256 KB of 1 MB per bericht. <br />Zie [Event hubs limieten](../event-hubs/event-hubs-quotas.md). |    Wanneer invoer/uitvoer-partitionering niet is uitgelijnd, wordt elke gebeurtenis afzonderlijk `EventData` verpakt in en verzonden in een batch van Maxi maal de maximale bericht grootte. Dit gebeurt ook als [Eigenschappen van aangepaste meta gegevens](#custom-metadata-properties-for-output) worden gebruikt. <br /><br />  Wanneer de invoer/uitvoer-partitionering is uitgelijnd, worden meerdere gebeurtenissen in één `EventData` exemplaar verpakt, tot de maximale bericht grootte en verzonden.    |
| Power BI | Zie [Power bi-rest API-limieten](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zie [Azure Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | De standaard waarde is 100 entiteiten per afzonderlijke trans actie. U kunt deze naar behoefte zo nodig naar een kleinere waarde configureren. |
| Azure Service Bus-wachtrij    | 256 KB per bericht voor de Standard-laag, 1 MB voor de Premium-laag.<br /> Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md). | Eén gebeurtenis per bericht gebruiken. |
| Azure Service Bus-onderwerp | 256 KB per bericht voor de Standard-laag, 1 MB voor de Premium-laag.<br /> Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md). | Eén gebeurtenis per bericht gebruiken. |
| Azure Cosmos DB    | Zie [Azure Cosmos DB limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batch grootte en schrijf frequentie worden dynamisch aangepast op basis van Azure Cosmos DB reacties. <br /> Er zijn geen vooraf vastgestelde beperkingen van Stream Analytics. |
| Azure Functions    | | De standaard Batch grootte is 262.144 bytes (256 KB). <br /> Het aantal standaard gebeurtenissen per batch is 100. <br /> De Batch grootte kan worden geconfigureerd en kan worden verg root of verkleind in de Stream Analytics [uitvoer opties](#azure-functions).

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> 
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
