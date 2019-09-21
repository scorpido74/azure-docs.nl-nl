---
title: Inzicht in de uitvoer van Azure Stream Analytics
description: Dit artikel beschrijft de opties voor uitvoer gegevens beschikbaar zijn in Azure Stream Analytics, met inbegrip van Power BI voor analyseresultaten.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 386dc737bb45eec031aaa1a0c55f4478b8302c54
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173579"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Inzicht in de uitvoer van Azure Stream Analytics

In dit artikel worden de typen uitvoer beschreven die beschikbaar zijn voor een Azure Stream Analytics taak. Uitvoer kunnen u opslaan en sla de resultaten van de Stream Analytics-taak. Door de uitvoer gegevens te gebruiken, kunt u verdere Business Analytics en gegevens opslag van uw gegevens uitvoeren.

Wanneer u uw Stream Analytics query ontwerpt, raadpleegt u de naam van de uitvoer met behulp van de [component into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). U kunt één uitvoer per taak of meerdere uitvoer per streaming-taak gebruiken (als u deze nodig hebt) door meerdere componenten in de query op te geven.

Als u Stream Analytics taak uitvoer wilt maken, bewerken en testen, kunt u de [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)en [Visual Studio](stream-analytics-quick-create-vs.md)gebruiken.

Sommige typen uitvoer ondersteunen het [partitioneren van partities](#partitioning). De grootte van de [uitvoer batch](#output-batch-size) is afhankelijk van de door voer te optimaliseren.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics ondersteunt [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage is een grootschalige-opslag plaats voor het hele bedrijf voor big data analytische werk belastingen. U kunt Data Lake Storage gebruiken voor het opslaan van gegevens van elke grootte, type en opname snelheid voor operationele en experimentele analyses. Stream Analytics moet worden gemachtigd om toegang te krijgen tot Data Lake Storage.

Azure Data Lake Storage uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Data Lake Storage gen 1-uitvoer te configureren.   

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query om te leiden naar Data Lake Store. |
| Subscription | Het abonnement met uw Azure Data Lake Storage-account. |
| Accountnaam | De naam van het Data Lake Store-account waarnaar u uw uitvoer wilt verzenden. Er wordt een vervolg keuzelijst weer gegeven met Data Lake Store accounts die beschikbaar zijn in uw abonnement. |
| Voorvoegsel van het padpatroon | Het bestandspad dat wordt gebruikt voor het schrijven van uw bestanden binnen het opgegeven Data Lake Store-account. U kunt een of meer exemplaren van de variabelen {date} en {time} opgeven:<br /><ul><li>Voorbeeld 1: Map1/logboeken / {date} / {time}</li><li>Voorbeeld 2: Map1/logboeken / {date}</li></ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd.<br /><br />Als het patroon van het bestandspad geen afsluitende slash (/) bevat, wordt het laatste patroon in het bestandspad behandeld als een voor voegsel van de bestands naam. <br /><br />Nieuwe bestanden worden gemaakt in de volgende situaties:<ul><li>In de uitvoerschema wijzigen</li><li>Externe of interne herstart van een taak</li></ul> |
| Datumnotatie | Optioneel. Als de datumtoken van de in het pad van het voorvoegsel wordt gebruikt, kunt u de datumnotatie waarin de bestanden zijn ingedeeld. Voorbeeld: JJJJ/MM/DD |
|Tijdnotatie | Optioneel. Als de token van de tijd in het pad van het voorvoegsel wordt gebruikt, geeft u de indeling waarin de bestanden zijn ingedeeld. De enige ondersteunde waarde is momenteel HH. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON-, CSV- en Avro worden ondersteund.|
| Encoding | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. Alleen de coderingsindeling UTF-8 wordt momenteel ondersteund.|
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidingstekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn met door komma's, door puntkomma's, spatie, tab en verticale balk.|
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt gesloten wanneer de taak stopt of Stream Analytics is verplaatst op naar de volgende periode. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven.|
| Verificatiemodus | U kunt toegang tot uw Data Lake Storage-account verlenen met behulp van [beheerde identiteits](stream-analytics-managed-identities-adls.md) -of gebruikers token. Nadat u toegang hebt verleend, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de Data Lake Storage uitvoer voor deze taak te verwijderen of de Stream Analytics-taak te verwijderen. |

## <a name="sql-database"></a>SQL Database

U kunt [Azure SQL database](https://azure.microsoft.com/services/sql-database/) gebruiken als uitvoer voor gegevens die relationeel zijn of voor toepassingen die afhankelijk zijn van inhoud die wordt gehost in een relationele data base. Stream Analytics-taken worden geschreven naar een bestaande tabel in SQL Database. Het tabel schema moet exact overeenkomen met de velden en de bijbehorende typen in de uitvoer van uw taak. U kunt ook [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) als uitvoer opgeven via de optie SQL database uitvoer. Zie het artikel [Stream Analytics met Azure SQL database als uitvoer als](stream-analytics-sql-output-perf.md) u meer wilt weten over manieren om de schrijf doorvoer te verbeteren.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een SQL Database uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de query-uitvoer met deze database te regelen. |
| Database | De naam van de Data Base waarin u uw uitvoer wilt verzenden. |
| Servernaam | De naam van de SQL-Database-server. |
| Gebruikersnaam | De gebruikers naam die schrijf toegang tot de data base heeft. Stream Analytics ondersteunt alleen SQL-verificatie. |
| Wachtwoord | Het wachtwoord voor verbinding met de database. |
| Tabel | De naam van de tabel waar de uitvoer wordt geschreven. De tabel naam is hoofdletter gevoelig. Het schema van deze tabel moet exact overeenkomen met het aantal velden en de bijbehorende typen die uw taak uitvoer genereert. |
|Partitie schema overnemen| Een optie voor het overnemen van het partitie schema van uw vorige query stap, om een volledig parallelle topologie met meerdere schrijvers voor de tabel in te scha kelen. Zie [Azure stream Analytics uitvoer naar Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie.|
|Maximum aantal batches| De aanbevolen bovengrens voor het aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.|

> [!NOTE]
> De Azure SQL Database aanbieding wordt ondersteund voor een taak uitvoer in Stream Analytics, maar een virtuele machine van Azure met SQL Server met een gekoppelde data base of een SQL Azure beheerd exemplaar wordt nog niet ondersteund. Dit is mogelijk gewijzigd in toekomstige releases.

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob-opslag en Azure Data Lake Gen2

Uitgaand naar Azure Data Lake Gen2 wordt aangeboden als een preview-functie in de beperkte regio's over de hele wereld. U kunt toegang tot het voor beeld aanvragen door aanvullende details op ons [aanvraag formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)op te geven.

Azure Blob-opslag biedt een voordelige en schaal bare oplossing voor het opslaan van grote hoeveel heden ongestructureerde gegevens in de Cloud. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor een inleiding in Blob Storage en het gebruik ervan.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een BLOB-uitvoer.

| Naam van eigenschap       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Uitvoeralias        | Een beschrijvende naam die wordt gebruikt in query's om de query-uitvoer naar deze blobopslag te regelen. |
| Storage-account     | De naam van het opslag account waarin u uw uitvoer wilt verzenden.               |
| Opslagaccountsleutel | De geheime sleutel die is gekoppeld aan de storage-account.                              |
| Opslagcontainer   | Een logische groepering voor blobs die zijn opgeslagen in de Azure-Blob service. Wanneer u een blob geüpload naar de Blob-service, moet u een container voor die blob opgeven. |
| Padpatroon | Optioneel. Het patroon van het bestandspad dat wordt gebruikt voor het schrijven van uw blobs binnen de opgegeven container. <br /><br /> In het pad patroon kunt u kiezen voor het gebruik van een of meer exemplaren van de variabelen datum en tijd om de frequentie op te geven waarmee blobs worden geschreven: <br /> {date}, {time} <br /><br />U kunt aangepaste BLOB-partitionering gebruiken om één aangepaste {Field}-naam van uw gebeurtenis gegevens op te geven voor het partitioneren van blobs. Naam van het veld is alfanumerieke en kan spaties, afbreekstreepjes en onderstrepingstekens bevatten. De volgende beperkingen met betrekking tot aangepaste velden zijn beschikbaar: <ul><li>Veld namen zijn niet hoofdletter gevoelig. De service kan bijvoorbeeld geen onderscheid maken tussen de kolom-ID en de kolom-id.</li><li>Geneste velden zijn niet toegestaan. Gebruik in plaats daarvan een alias in de taak query om het veld af te vlakken.</li><li>Expressies kunnen niet worden gebruikt als veld naam.</li></ul> <br />Met deze functie kunt u het gebruik van aangepaste configuraties voor datum/tijd-indeling in het pad. Aangepaste datum en tijd indelingen moeten worden opgegeven op een tijdstip, ingesloten door de {datum/tijd:\<aanduiding >} trefwoord. Toegestane invoer voor \<de specificatie > zijn jjjj, mm, M, dd, d, hh, H, mm, M, SS of s. Het sleutel woord {\<DateTime: aanduiding >} kan meermaals worden gebruikt in het pad naar aangepaste datum/tijd-configuraties. <br /><br />Voorbeelden: <ul><li>Voorbeeld 1: cluster1/logboeken / {date} / {time}</li><li>Voorbeeld 2: cluster1/logboeken / {date}</li><li>Voor beeld 3: cluster1/{client_id}/{date}/{time}</li><li>Voor beeld 4: cluster1/{DateTime: SS}/{myField} waarbij de query is: Selecteer Data. myField als myField van invoer.</li><li>Voor beeld 5: cluster1/Year = {DateTime: jjjj}/month = {DateTime: MM}/Day = {DateTime: dd}</ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd.<br /><br />Bestands namen gebruiken de volgende Conventie: <br /><br />{Pad voorvoegsel Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Voorbeeld van de uitvoerbestanden:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Zie [Azure stream Analytics aangepaste BLOB-uitvoer partitionering](stream-analytics-custom-path-patterns-blob-storage-output.md)voor meer informatie over deze functie. |
| Datumnotatie | Optioneel. Als de datumtoken van de in het pad van het voorvoegsel wordt gebruikt, kunt u de datumnotatie waarin de bestanden zijn ingedeeld. Voorbeeld: JJJJ/MM/DD |
| Tijdnotatie | Optioneel. Als de token van de tijd in het pad van het voorvoegsel wordt gebruikt, geeft u de indeling waarin de bestanden zijn ingedeeld. De enige ondersteunde waarde is momenteel HH. |
| Serialisatie-indeling voor gebeurtenissen | Serialisatie-indeling voor de uitvoergegevens. JSON, CSV, AVRO en Parquet worden ondersteund. |
|Minimum aantal rijen (alleen Parquet)|Het aantal minimum rijen per batch. Voor Parquet maakt elke batch een nieuw bestand. De huidige standaard waarde is 2.000 rijen en het toegestane maximum is 10.000 rijen.|
|Maximum tijd (alleen Parquet)|De maximale wacht tijd per batch. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. De huidige standaard waarde is 1 minuut en het toegestane maximum is 2 uur. Als uw BLOB-uitvoer een patroon frequentie voor het pad heeft, kan de wacht tijd niet hoger zijn dan het tijds bereik van de partitie.|
| Encoding    | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. Alleen de coderingsindeling UTF-8 wordt momenteel ondersteund. |
| Scheidingsteken   | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidingstekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn met door komma's, door puntkomma's, spatie, tab en verticale balk. |
| Indeling      | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt gesloten wanneer de taak stopt of Stream Analytics is verplaatst op naar de volgende periode. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven. |

Wanneer u Blob Storage als uitvoer gebruikt, wordt er in de volgende gevallen een nieuw bestand in de BLOB gemaakt:

* Als het bestand is groter dan het maximum aantal toegestane blokken (momenteel 50.000). U kunt het Maxi maal toegestane aantal blokken bereiken zonder de Maxi maal toegestane Blob-grootte te bereiken. Als de uitvoerpakketten hoog is, ziet u meer bytes per blok en het bestand is groter. Als de uitvoerpakketten laag is, elk blok is minder gegevens en de grootte van het kleiner is.
* Als er een schema wijziging in de uitvoer is, en de uitvoer indeling vereist een vast schema (CSV en AVRO).
* Als een taak opnieuw wordt opgestart, extern door een gebruiker het stoppen en starten of intern voor onderhoud of fout bij het herstel van het systeem.
* Als de query volledig is gepartitioneerd en er een nieuw bestand wordt gemaakt voor elke uitvoer partitie.
* Als de gebruiker een bestand of container van het opslag account verwijdert.
* Als de uitvoer is gepartitioneerd met behulp van het pad voorvoegsel patroon en er wordt een nieuwe BLOB gebruikt wanneer de query naar het volgende uur wordt verplaatst.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld en er een nieuwe BLOB wordt gemaakt per partitie sleutel als deze niet bestaat.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld waarbij de kardinaliteit van de partitie sleutel groter is dan 8.000 en er een nieuwe BLOB wordt gemaakt per partitie sleutel.

## <a name="event-hubs"></a>Event Hubs

De [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) -service is een zeer schaalbare publish-subscribe ingestor voor het event. Het kan miljoenen gebeurtenissen per seconde verzamelen. Een Event Hub als uitvoer wordt gebruikt wanneer de uitvoer van een Stream Analytics taak de invoer van een andere streaming-taak wordt.

U hebt een aantal para meters nodig voor het configureren van gegevens stromen van Event hubs als uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Event Hub te sturen. |
| Event Hub-naamruimte | Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een Event Hub naam ruimte gemaakt. |
| Naam van Event Hub | De naam van de Event Hub uitvoer. |
| De naam van een Event hub-beleid | Het beleid voor gedeelde toegang, dat u kunt maken op het tabblad **configureren** van de Event hub. Elk gedeeld toegangsbeleid heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Beleidssleutel voor Event hub | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Event Hub. |
| Partitiesleutelkolom | Optioneel. Een kolom die de partitie sleutel voor Event Hub uitvoer bevat. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON-, CSV- en Avro worden ondersteund. |
| Encoding | Voor CSV en JSON is UTF-8 de enige ondersteunde coderingsindeling op dit moment. |
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidingstekens om gegevens te serialiseren in csv-indeling. Ondersteunde waarden zijn met door komma's, door puntkomma's, spatie, tab en verticale balk. |
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt gesloten wanneer de taak stopt of Stream Analytics is verplaatst op naar de volgende periode. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven. |
| Eigenschappenkolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

U kunt [Power bi](https://powerbi.microsoft.com/) als uitvoer voor een stream Analytics taak gebruiken om een uitgebreide visualisatie ervaring met analyse resultaten te bieden. U kunt deze mogelijkheid gebruiken voor operationele Dash boards, het genereren van rapporten en het rapporteren van metrische gegevens.

Power BI uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Power BI-uitvoer te configureren.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Geef een beschrijvende naam op die wordt gebruikt in query's om de uitvoer van de query naar deze Power BI uitvoer te sturen. |
| Groepswerkruimte |Als u het delen van gegevens met andere Power BI gebruikers wilt inschakelen, kunt u groepen in uw Power BI account selecteren of **mijn werk ruimte** kiezen als u niet naar een groep wilt schrijven. Bijwerken van een bestaande groep is vereist voor het vernieuwen van de Power BI-verificatie. |
| Naam van de gegevensset |Geef een naam op voor de gegevensset die u voor de Power BI uitvoer wilt gebruiken. |
| Tabelnaam |Geef een tabelnaam onder de gegevensset van de Power BI-uitvoer. Power BI uitvoer van Stream Analytics taken kan momenteel slechts één tabel bevatten in een gegevensset. |
| Verbinding autoriseren | U moet autoriseren met Power BI om uw uitvoer instellingen te configureren. Zodra u deze uitvoer toegang hebt verleend aan uw Power BI-dash board, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de taak uitvoer te verwijderen of de Stream Analytics-taak te verwijderen. | 

Raadpleeg de zelf studie [Azure stream Analytics en Power bi](stream-analytics-power-bi-dashboard.md) voor een overzicht van het configureren van een Power bi uitvoer en een dash board.

> [!NOTE]
> Maak geen expliciete gegevensset en tabel in het dash board Power BI. De gegevensset en de tabel worden automatisch ingevuld wanneer de taak wordt gestart en de taak pomp uitvoer wordt gestart in Power BI. Als de taak query geen resultaten genereert, worden de gegevensset en tabel niet gemaakt. Als Power BI al een gegevensset en een tabel met dezelfde naam heeft als die in deze Stream Analytics taak, worden de bestaande gegevens overschreven.
>

### <a name="create-a-schema"></a>Maak een schema
Azure Stream Analytics maakt een Power BI gegevensset en tabel schema voor de gebruiker als deze nog niet bestaan. In alle andere gevallen moet wordt de tabel bijgewerkt met nieuwe waarden. Op dit moment kan slechts één tabel bestaan in een gegevensset. 

Power BI gebruikt het FIFO-Bewaar beleid (First-in, first-out). Gegevens worden verzameld in een tabel totdat er 200.000 rijen zijn gevonden.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converteer een gegevens type van Stream Analytics naar Power BI
Azure Stream Analytics bijgewerkt het gegevensmodel in runtime dynamisch als het uitvoerschema wordt gewijzigd. Alle worden wijzigingen in de kolom naam, de wijzigingen van het type kolom, en het toevoegen of verwijderen van kolommen bijgehouden.

Deze tabel bevat informatie over de gegevens type conversies van [Stream Analytics gegevens typen](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) Power BI naar de [EDM-typen (Entity Data Model)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), als een Power bi-gegevensset en-tabel niet bestaan.

Van Stream Analytics | Met Power BI
-----|-----
bigint | Int64
nvarchar(max) | Reeks
datum/tijd | Datum en tijd
float | Double-waarde
Matrix van de record | Teken reeks type, constante waarde "IRecord" of "IArray"

### <a name="update-the-schema"></a>Het schema bijwerken
Stream Analytics bepaalt welk data model-schema op basis van de eerste set met gebeurtenissen in de uitvoer. Later, indien nodig, wordt het schema van het gegevens model bijgewerkt om binnenkomende gebeurtenissen te kunnen verwerken die mogelijk niet in het oorspronkelijke schema passen.

Vermijd de `SELECT *` query om dynamische schema-updates voor rijen te voor komen. Naast mogelijke prestatie implicaties kan dit leiden tot een onzekerheid van de tijd die nodig is voor de resultaten. Selecteer de exacte velden die moeten worden weer gegeven op het Power BI dash board. Bovendien moeten de gegevenswaarden compatibel zijn met het gekozen type zijn.


Vorige/huidige | Int64 | Reeks | Datum en tijd | Double-waarde
-----------------|-------|--------|----------|-------
Int64 | Int64 | Reeks | Reeks | Double-waarde
Double-waarde | Double-waarde | Reeks | Reeks | Double-waarde
Reeks | String | String | String | Reeks 
Datum en tijd | Reeks | Reeks |  Datum en tijd | Tekenreeks

## <a name="table-storage"></a>Tabelopslag

[Azure Table Storage](../storage/common/storage-introduction.md) biedt een Maxi maal beschik bare, zeer schaal bare opslag, zodat een toepassing automatisch kan worden geschaald om te voldoen aan de vraag van de gebruiker. Table Storage is de NoSQL sleutel/kenmerk opslag van micro soft, die u kunt gebruiken voor gestructureerde gegevens met minder beperkingen voor het schema. Azure Table storage kan worden gebruikt voor het opslaan van gegevens voor de persistentie en efficiënte ophalen.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een tabel uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de query-uitvoer naar deze tabelopslag te regelen. |
| Storage-account |De naam van het opslag account waarin u uw uitvoer wilt verzenden. |
| Opslagaccountsleutel |De toegangssleutel die is gekoppeld aan de storage-account. |
| Tabelnaam |De naam van de tabel. De tabel wordt gemaakt als deze nog niet bestaat. |
| Partitiesleutel |De naam van de uitvoer kolom die de partitie sleutel bevat. De partitie sleutel is een unieke id voor de partitie in een tabel die het eerste deel vormt van de primaire sleutel van een entiteit. Dit is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Rijsleutel |De naam van de uitvoer kolom die de rij sleutel bevat. De rij is een unieke id voor een entiteit binnen een partitie. Deze bestaat uit het tweede gedeelte van de primaire sleutel van een entiteit. De rij is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Batchgrootte |Het aantal records voor een batchbewerking. De standaardwaarde (100) is voldoende voor de meeste taken. Zie de [tabel batch bewerking spec](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) voor meer informatie over het wijzigen van deze instelling. |

## <a name="service-bus-queues"></a>Service Bus-wachtrijen

[Service Bus wachtrijen](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) bieden een FIFO-bericht levering aan een of meer concurrerende consumenten. Normaal gesp roken worden berichten ontvangen en verwerkt door de ontvangers in de tijdelijke volg orde waarin ze aan de wachtrij zijn toegevoegd. Elk bericht wordt door slechts één bericht verbruiker ontvangen en verwerkt.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een wachtrij-uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Service Bus wachtrij te sturen. |
| Service Bus-naamruimte |Een container voor een set met bericht entiteiten. |
| Wachtrijnaam |De naam van de Service Bus wachtrij. |
| Naam van het wachtrijbeleid |Wanneer u een wachtrij maakt, kunt u ook een beleid voor gedeelde toegang maken op het tabblad **configureren** van de wachtrij. Elk gedeeld toegangsbeleid heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Sleutel voor het wachtrijbeleid |De gedeelde toegangssleutel die wordt gebruikt voor het verifiëren van toegang tot de Service Bus-naamruimte. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON-, CSV- en Avro worden ondersteund. |
| Encoding |Voor CSV en JSON is UTF-8 de enige ondersteunde coderingsindeling op dit moment. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidingstekens om gegevens te serialiseren in csv-indeling. Ondersteunde waarden zijn met door komma's, door puntkomma's, spatie, tab en verticale balk. |
| Indeling |Alleen van toepassing op JSON-type. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. |
| Eigenschappenkolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Systeemeigenschaps-kolommen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [systeem eigenschappen voor service bus wachtrij en onderwerp-uitvoer](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Het aantal partities is [op basis van de Service Bus-SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). Partitiesleutel is een unieke integer-waarde voor elke partitie.

## <a name="service-bus-topics"></a>Service Bus-onderwerpen
Service Bus wachtrijen bieden een een-op-een-communicatie methode van afzender naar ontvanger. [Service Bus onderwerpen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieden een een-op-veel communicatie vorm.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een Service Bus topic-uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar dit Service Bus onderwerp te sturen. |
| Service Bus-naamruimte |Een container voor een set met bericht entiteiten. Wanneer u een nieuwe event hub hebt gemaakt, is ook een Service Bus-naamruimte gemaakt. |
| Onderwerpnaam |Onderwerpen zijn berichtentiteiten, vergelijkbaar met eventhubs en wachtrijen. Ze zijn ontworpen voor het verzamelen van gebeurtenis stromen van apparaten en services. Wanneer een onderwerp wordt gemaakt, wordt er ook een specifieke naam gegeven. De berichten die naar een onderwerp worden verzonden, zijn pas beschikbaar als er een abonnement is gemaakt. Zorg er dus voor dat er een of meer abonnementen zijn onder het onderwerp. |
| Naam van het onderwerpbeleid |Wanneer u een Service Bus onderwerp maakt, kunt u ook beleid voor gedeelde toegang maken op het tabblad **configureren** van het onderwerp. Elk gedeeld toegangsbeleid heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Sleutel voor het onderwerpbeleid |De gedeelde toegangssleutel die wordt gebruikt voor het verifiëren van toegang tot de Service Bus-naamruimte. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON-, CSV- en Avro worden ondersteund. |
| Encoding |Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. Alleen de coderingsindeling UTF-8 wordt momenteel ondersteund. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidingstekens om gegevens te serialiseren in csv-indeling. Ondersteunde waarden zijn met door komma's, door puntkomma's, spatie, tab en verticale balk. |
| Eigenschappenkolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Systeemeigenschaps-kolommen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [systeem eigenschappen voor service bus wachtrij en onderwerp-uitvoer](#system-properties-for-service-bus-queue-and-topic-outputs) |

Het aantal partities is [op basis van de Service Bus-SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is een wereld wijd gedistribueerde database service die onbeperkte elastische schaal rond de wereld, uitgebreide query en automatische indexering via schema-neutraal gegevens modellen biedt. Zie het artikel [Stream Analytics met Azure Cosmos DB als uitvoer](stream-analytics-documentdb-output.md) voor meer informatie over Azure Cosmos DB container opties voor stream Analytics.

Azure Cosmos DB uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

> [!Note]
> Op dit moment ondersteunt Azure Stream Analytics alleen de verbinding met Azure Cosmos DB met behulp van de SQL-API.
> Andere Azure Cosmos DB-API's zijn nog niet ondersteund. Als u punt Azure Stream Analytics met de Azure Cosmos DB-accounts die zijn gemaakt met andere API's, zijn de gegevens mogelijk niet juist opgeslagen.

De volgende tabel beschrijft de eigenschappen voor het maken van een Azure Cosmos DB-uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias | Een alias om te verwijzen deze uitvoer in uw Stream Analytics-query. |
| Sink | Azure Cosmos DB. |
| Importoptie | Kies **Cosmos DB selecteren in uw abonnement** of **Cosmos DB instellingen hand matig opgeven**.
| Account-id | De naam of eind punt-URI van het Azure Cosmos DB-account. |
| Accountcode | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account. |
| Database | De naam van de Azure Cosmos DB-Data Base. |
| Containernaam | De container naam die moet worden gebruikt, die voor komt in Cosmos DB. Voorbeeld:  <br /><ul><li> _MyContainer_: Er moet een container met de naam ' MyContainer ' bestaan.</li>|
| Document-id |Optioneel. De naam van het veld in uitvoer gebeurtenissen dat wordt gebruikt om de primaire sleutel op te geven waarop invoeg-of update bewerkingen zijn gebaseerd.

## <a name="azure-functions"></a>Azure Functions
Azure Functions is een serverloze compute-service die u kunt gebruiken om code op aanvraag uit te voeren zonder expliciet een infra structuur in te richten of te beheren. Hiermee kunt u code implementeren die wordt geactiveerd door gebeurtenissen die optreden in azure of partner services. Dankzij deze mogelijkheid van Azure Functions om op Triggers te reageren, is het een natuurlijke uitvoer voor Azure Stream Analytics. Met deze uitvoer adapter kunnen gebruikers Stream Analytics verbinding maken met Azure Functions en een script of stukje code uitvoeren als reactie op verschillende gebeurtenissen.

Azure Functions uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

Azure Stream Analytics activeert Azure Functions via HTTP-triggers. De Azure Functions uitvoer adapter is beschikbaar met de volgende Configureer bare eigenschappen:

| Naam van eigenschap | Description |
| --- | --- |
| Function App |De naam van uw Azure Functions-app. |
| Function |De naam van de functie in uw Azure Functions-app. |
| Sleutel |Als u een Azure-functie van een ander abonnement wilt gebruiken, kunt u dit doen door de sleutel voor toegang tot uw functie te geven. |
| Maximale batchgrootte |Een eigenschap waarmee u de maximale grootte kunt instellen voor elke uitvoer batch die wordt verzonden naar uw Azure-functie. De invoer eenheid bevindt zich in bytes. Deze waarde is standaard 262.144 bytes (256 KB). |
| Maximum aantal batches  |Een eigenschap waarmee u het maximum aantal gebeurtenissen in elke batch kunt opgeven dat naar Azure Functions wordt verzonden. De standaardwaarde is 100. |

Wanneer Azure Stream Analytics een uitzonde ring ontvangt van een 413 (' HTTP-aanvraag entiteit te groot ') van een Azure-functie, vermindert deze de grootte van de batches die worden verzonden naar Azure Functions. In de code van uw Azure-functie, gebruikt u deze uitzondering om ervoor te zorgen dat Azure Stream Analytics geen te grote batches niet verzenden. Zorg er ook voor dat het maximum aantal batch-en grootte waarden die worden gebruikt in de functie consistent zijn met de waarden die zijn ingevoerd in de Stream Analytics Portal.

> [!NOTE]
> Tijdens een test verbinding stuurt Stream Analytics een lege batch naar Azure Functions om te testen als de verbinding tussen de twee werken. Zorg ervoor dat uw functions-app lege batch-aanvragen verwerkt om ervoor te zorgen dat de test verbinding verloopt.

In een situatie waarin er geen gebeurtenis overloop plaatsvindt in een tijd venster, wordt er ook geen uitvoer gegenereerd. Als gevolg hiervan wordt de functie **computeResult** niet aangeroepen. Dit gedrag is consistent met de ingebouwde statistische functies in vensters.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste meta gegevens eigenschappen voor uitvoer 

U kunt query kolommen als gebruikers eigenschappen aan uw uitgaande berichten toevoegen. Deze kolommen gaan niet naar de payload. De eigenschappen zijn aanwezig in de vorm van een woorden lijst in het uitvoer bericht. *Sleutel* is de kolom naam en- *waarde* is de kolom waarde in de woorden lijst eigenschappen. Alle Stream Analytics gegevens typen worden ondersteund, behalve record en matrix.  

Ondersteunde uitvoer: 
* Service Bus-wachtrij 
* Service Bus-onderwerp 
* Event Hub 

In het volgende voor beeld voegen we de twee `DeviceId` velden `DeviceStatus` en toe aan de meta gegevens. 
* Ophalen`select *, DeviceId, DeviceStatus from iotHubInput`
* Uitvoer configuratie:`DeviceId,DeviceStatus`

![Eigenschappenkolommen](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

De volgende scherm afbeelding ziet u de eigenschappen van uitvoer berichten die zijn geïnspecteerd in EventHub via [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Aangepaste eigenschappen van gebeurtenis](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Systeem eigenschappen voor de Service Bus wachtrij en onderwerp-uitvoer 
U kunt query kolommen als [systeem eigenschappen](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) koppelen aan uw uitgaande service bus-wachtrij of onderwerp-berichten. Deze kolommen worden niet in de payload gezet, maar de bijbehorende [systeem eigenschap](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage wordt gevuld met de query kolom waarden.
Deze systeem eigenschappen worden ondersteund `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Teken reeks waarden van deze kolommen worden geparseerd als overeenkomend type eigenschaps waarde van het systeem en eventuele fouten bij het parseren worden behandeld als gegevens fouten.
Dit veld wordt als een JSON-object indeling gegeven. Meer informatie over deze indeling is als volgt:
* Omgeven door {}accolades.
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

De volgende tabel geeft een overzicht van de partitie ondersteuning en het aantal schrijvers van de uitvoer voor elk uitvoertype:

| Uitvoertype | Ondersteuning voor partitioneren | Partitiesleutel  | Aantal uitvoer schrijvers |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Gebruik {date} en {time} tokens in het pad voor voegsel patroon. Kies de datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. HH wordt gebruikt voor de tijd notatie. | De invoer voor het partitioneren van volgt [volledig worden opgestart query's](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ja, moet zijn ingeschakeld. | Op basis van de component PARTITION BY in de query. | Wanneer de optie partitioneren overnemen is ingeschakeld, volgt de invoer partitionering voor [volledige kan worden opgestart-query's](stream-analytics-scale-jobs.md). Zie [Azure stream Analytics output to Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie over het verbeteren van de prestaties van schrijf doorvoer tijdens het laden van gegevens in Azure SQL database. |
| Azure Blob Storage | Ja | Gebruik {date} en {time} tokens uit uw gebeurtenis velden in het pad patroon. Kies de datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. HH wordt gebruikt voor de tijd notatie. BLOB-uitvoer kan worden gepartitioneerd met één aangepast gebeurtenis kenmerk {FieldName} of {datetime\<: specificatie >}. | De invoer voor het partitioneren van volgt [volledig worden opgestart query's](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ja | Ja | Is afhankelijk van de uitlijning van de partitie.<br /> Wanneer de partitie sleutel voor Event Hub uitvoer gelijk wordt uitgelijnd met de stap upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in Event Hub uitvoer. Elke schrijver maakt gebruik van de [EventHubSender-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) voor het verzenden van gebeurtenissen naar de specifieke partitie. <br /> Wanneer de partitie sleutel voor Event Hub uitvoer niet is afgestemd op de stap van de upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in die vorige stap. Elke schrijver maakt gebruik van de [SendBatchAsync-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** om gebeurtenissen te verzenden naar alle uitvoer partities. |
| Power BI | Nee | Geen | Niet van toepassing. |
| Azure Table Storage | Ja | Elke uitvoerkolom.  | De invoer voor het partitioneren van volgt [volledig geparallelliseerd query's](stream-analytics-scale-jobs.md). |
| Azure Service Bus-onderwerp | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus-SKU's en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in het onderwerp van de uitvoer.  |
| Azure Service Bus-wachtrij | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus-SKU's en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in de uitvoerwachtrij. |
| Azure Cosmos DB | Ja | Op basis van de component PARTITION BY in de query. | De invoer voor het partitioneren van volgt [volledig geparallelliseerd query's](stream-analytics-scale-jobs.md). |
| Azure Functions | Ja | Op basis van de component PARTITION BY in de query. | De invoer voor het partitioneren van volgt [volledig geparallelliseerd query's](stream-analytics-scale-jobs.md). |

Het aantal schrijvers van uitvoer kan ook worden beheerd met `INTO <partition count>` behulp van de component (Zie [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) in uw query. Dit kan handig zijn bij het bereiken van een gewenste taak topologie. Als de uitvoeradapter niet is gepartitioneerd, gebrek aan gegevens in één invoer partitie, zorgt ervoor dat een vertraging van maximaal het latere aankomst van tijd. In dergelijke gevallen wordt de uitvoer samengevoegd met één schrijver, wat kan leiden tot knel punten in de pijp lijn. Zie [Azure stream Analytics overwegingen voor gebeurtenis orders](stream-analytics-out-of-order-and-late-events.md)voor meer informatie over het beleid voor late ontvangst.

## <a name="output-batch-size"></a>Batchgrootte voor uitvoer
Azure Stream Analytics gebruikt batches van het formaat variable om gebeurtenissen te verwerken en te schrijven naar uitvoer. Normaal gesp roken schrijft de Stream Analytics engine niet één bericht tegelijk en worden batches gebruikt voor efficiëntie. Wanneer het aantal binnenkomende en uitgaande gebeurtenissen hoog is, gebruikt Stream Analytics grotere batches. Wanneer de snelheid van uitgangsgebeurtenissen laag is, gebruikt het kleinere batches latentie laag te houden.

In de volgende tabel worden enkele van de overwegingen voor het uitvoeren van batch verwerking beschreven:

| Uitvoertype | Maximale berichtlengte | Optimalisatie van de grootte van batch |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zie [Data Lake Storage limieten](../azure-subscription-service-limits.md#data-lake-store-limits). | Gebruik Maxi maal 4 MB per schrijf bewerking. |
| Azure SQL Database | Configureerbaar met behulp van maximum aantal batches. Maxi maal 10.000 minimale en 100 minimum rijen per bulk invoer standaard.<br />Zie [Azure SQL](../sql-database/sql-database-resource-limits.md)-limieten. |  Elke batch wordt in eerste instantie bulksgewijs ingevoegd met een maximum aantal batches. Batch is gesplitst in tweeën (totdat het minimale aantal batches is) op basis van Herhaal bare fouten van SQL. |
| Azure Blob Storage | Zie [Azure Storage limieten](../azure-subscription-service-limits.md#storage-limits). | De maximale grootte van het BLOB-blok is 4 MB.<br />Het maximum aantal BLOB-Bock is 50.000. |
| Azure Event Hubs  | 256 KB of 1 MB per bericht. <br />Zie [Event hubs limieten](../event-hubs/event-hubs-quotas.md). |  Wanneer invoer/uitvoer-partitionering niet is uitgelijnd, wordt elke gebeurtenis afzonderlijk `EventData` verpakt in en verzonden in een batch van Maxi maal de maximale bericht grootte. Dit gebeurt ook als [Eigenschappen van aangepaste meta gegevens](#custom-metadata-properties-for-output) worden gebruikt. <br /><br />  Wanneer de invoer/uitvoer-partitionering is uitgelijnd, worden meerdere gebeurtenissen in één `EventData` exemplaar verpakt, tot de maximale bericht grootte en verzonden. |
| Power BI | Zie [Power bi-rest API](https://msdn.microsoft.com/library/dn950053.aspx)-limieten. |
| Azure Table Storage | Zie [Azure Storage limieten](../azure-subscription-service-limits.md#storage-limits). | De standaard waarde is 100 entiteiten per afzonderlijke trans actie. U kunt deze naar behoefte zo nodig naar een kleinere waarde configureren. |
| Azure Service Bus-wachtrij   | 256 KB per bericht voor de Standard-laag, 1 MB voor de Premium-laag.<br /> Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md). | Eén gebeurtenis per bericht gebruiken. |
| Azure Service Bus-onderwerp | 256 KB per bericht voor de Standard-laag, 1 MB voor de Premium-laag.<br /> Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md). | Eén gebeurtenis per bericht gebruiken. |
| Azure Cosmos DB   | Zie [Azure Cosmos DB limieten](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batch grootte en schrijf frequentie worden dynamisch aangepast op basis van Azure Cosmos DB reacties. <br /> Er zijn geen vooraf vastgestelde beperkingen van Stream Analytics. |
| Azure Functions   | | De standaard Batch grootte is 262.144 bytes (256 KB). <br /> Het aantal standaard gebeurtenissen per batch is 100. <br /> De batchgrootte is configureerbaar en kan worden vergroot of verkleind in de Stream Analytics [uitvoeropties](#azure-functions).

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> 
> [Snelstart: Een Stream Analytics taak maken met behulp van de Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
