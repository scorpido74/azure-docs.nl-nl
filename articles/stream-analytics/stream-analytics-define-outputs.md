---
title: Inzicht krijgen in uitvoer vanuit Azure Stream Analytics
description: In dit artikel worden gegevensuitvoeropties beschreven die beschikbaar zijn in Azure Stream Analytics, waaronder Power BI voor analyseresultaten.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475228"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Inzicht krijgen in uitvoer vanuit Azure Stream Analytics

In dit artikel worden de typen uitvoer beschreven die beschikbaar zijn voor een Azure Stream Analytics-taak. Met uitvoer u de resultaten van de streamanalytics-taak opslaan en opslaan. Door gebruik te maken van de outputdata u verdere business analytics en data warehousing van uw data doen.

Wanneer u uw Stream Analytics-query ontwerpt, raadpleegt u de naam van de uitvoer met behulp van de [INTO-component](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). U één uitvoer per taak gebruiken, of meerdere uitvoer per streamingtaak (als u ze nodig hebt) door meerdere INTO-clausules in de query op te nemen.

Als u de taakuitvoer van Stream Analytics wilt maken, bewerken en testen, u de [Azure-portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell,](stream-analytics-quick-create-powershell.md#configure-output-to-the-job) [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)en [Visual Studio](stream-analytics-quick-create-vs.md)gebruiken.

Sommige uitvoertypen ondersteunen [partitionering](#partitioning). [Output batchgroottes](#output-batch-size) variëren om de doorvoer te optimaliseren.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics ondersteunt [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage is een bedrijfsbrede, hyperscale repository voor big data analytische workloads. U Data Lake Storage gebruiken om gegevens van elke grootte, elk type en opnamesnelheid op te slaan voor operationele en verkennende analyses. Stream Analytics moet zijn geautoriseerd om toegang te krijgen tot Data Lake Storage.

Azure Data Lake Storage-uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's Azure China 21Vianet en Azure Germany (T-Systems International).

In de volgende tabel worden eigenschapsnamen en hun beschrijvingen weergegeven om uw Data Lake Storage Gen 1-uitvoer te configureren.   

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar Data Lake Store te leiden. |
| Abonnement | Het abonnement dat uw Azure Data Lake Storage-account bevat. |
| Accountnaam | De naam van het Data Lake Store-account waar u uw uitvoer verzendt. U krijgt een vervolgkeuzelijst met Data Lake Store-accounts die beschikbaar zijn in uw abonnement. |
| Padvoorvoegingspatroon | Het bestandspad dat wordt gebruikt om uw bestanden te schrijven binnen het opgegeven Data Lake Store-account. U een of meer exemplaren van de variabelen {date} en {time} opgeven:<br /><ul><li>Voorbeeld 1: map1/logs/{date}/{time}</li><li>Voorbeeld 2: map1/logs/{date}</li></ul><br />De tijdstempel van de gemaakte mapstructuur volgt UTC en niet de lokale tijd.<br /><br />Als het patroon van het bestandspad geen trailing slash (/) bevat, wordt het laatste patroon in het bestandspad behandeld als een voorvoegsel van bestandsnamen. <br /><br />In deze omstandigheden worden nieuwe bestanden gemaakt:<ul><li>Wijziging in uitvoerschema</li><li>Externe of interne herstart van een taak</li></ul> |
| Datumnotatie | Optioneel. Als het datumtoken wordt gebruikt in het voorvoegselpad, u de datumnotatie selecteren waarin uw bestanden zijn ingedeeld. Voorbeeld: YYYY/MM/DD |
|Tijdnotatie | Optioneel. Als het tijdtoken wordt gebruikt in het voorvoegselpad, geeft u de tijdindeling op waarin uw bestanden zijn ingedeeld. Momenteel is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoergegevens. JSON, CSV en Avro worden ondersteund.|
| Encoding | Als u de CSV- of JSON-indeling gebruikt, moet een codering worden opgegeven. UTF-8 is op dit moment het enige ondersteunde coderingsformaat.|
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal veelvoorkomende scheidingstekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, puntkomma, ruimte, tabblad en verticale balk.|
| Indeling | Alleen van toepassing op JSON serialisatie. **De scheiding van** de regel geeft aan dat de uitvoer wordt opgemaakt door elk JSON-object te laten scheiden door een nieuwe regel. Als u **Gescheiden lijn**selecteert, wordt de JSON één object tegelijk gelezen. De hele inhoud op zich zou niet een geldige JSON.  **Array** geeft aan dat de uitvoer is opgemaakt als een array van JSON-objecten. Deze array wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijdvenster. In het algemeen is het beter om line-separate JSON te gebruiken, omdat het geen speciale behandeling vereist terwijl het uitvoerbestand nog steeds wordt geschreven.|
| Verificatiemodus | U de toegang tot uw Data Lake Storage-account autoriseren met [managed identity](stream-analytics-managed-identities-adls.md) of user token. Zodra u toegang verleent, u de toegang intrekken door het wachtwoord van het gebruikersaccount te wijzigen, de uitvoer van Data Lake Storage voor deze taak te verwijderen of de streamanalytics-taak te verwijderen. |

## <a name="sql-database"></a>SQL Database

U [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) gebruiken als uitvoer voor gegevens die relationeel zijn of voor toepassingen die afhankelijk zijn van inhoud die wordt gehost in een relationele database. Stream Analytics-taken schrijven naar een bestaande tabel in SQL Database. Het tabelschema moet precies overeenkomen met de velden en hun typen in de uitvoer van uw taak. U Azure [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) ook opgeven als uitvoer via de sql-databaseuitvoeroptie. Zie het [streamanalyse-artikel met Azure SQL Database als uitvoerartikel voor](stream-analytics-sql-output-perf.md) meer informatie over manieren om de schrijfdoorvoer te verbeteren.

U azure [SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) ook als uitvoer gebruiken. U moet [het openbare eindpunt in Azure SQL Database Managed Instance configureren](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) en vervolgens handmatig de volgende instellingen in Azure Stream Analytics configureren. Azure virtual machine running SQL Server with a database attached is also supported by manually configuring the settings below.

In de volgende tabel worden de eigenschapsnamen en de beschrijving ervan weergegeven voor het maken van een SQL-database-uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een vriendelijke naam die in query's wordt gebruikt om de queryuitvoer naar deze database te leiden. |
| Database | De naam van de database waar u uw uitvoer verzendt. |
| Servernaam | De naam van de SQL Database-server. Voor Azure SQL Database Managed Instance is het vereist om de poort 3342 op te geven. Voorbeeld van *sampleserver.public.database.windows.net,3342* |
| Gebruikersnaam | De gebruikersnaam die schrijftoegang heeft tot de database. Stream Analytics ondersteunt alleen SQL-verificatie. |
| Wachtwoord | Het wachtwoord om verbinding te maken met de database. |
| Tabel | De tabelnaam waar de uitvoer is geschreven. De tabelnaam is hoofdlettergevoelig. Het schema van deze tabel moet precies overeenkomen met het aantal velden en de typen die uw taakuitvoer genereert. |
|Partitieschema overnemen| Een optie voor het overnemen van het partitieschema van uw vorige querystap, om volledig parallelle topologie met meerdere schrijvers aan de tabel toe te laten. Zie [Azure Stream Analytics-uitvoer naar Azure SQL Database voor](stream-analytics-sql-output-perf.md)meer informatie.|
|Maximum aantal batch's| De aanbevolen bovengrens voor het aantal records dat bij elke bulkwisseltransactie wordt verzonden.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob-opslag en Azure Data Lake Gen2

Data Lake Storage Gen2 maakt Azure Storage de basis voor het bouwen van bedrijfsgegevensmeren op Azure. Data Lake Storage Gen2 is vanaf het begin ontworpen om meerdere petabytes aan informatie te onderhouden en tegelijkertijd honderden gigabits doorvoer te ondersteunen, zodat u eenvoudig enorme hoeveelheden gegevens beheren. Een fundamenteel onderdeel van Data Lake Storage Gen2 is de toevoeging van een hiërarchische naamruimte aan Blob-opslag.

Azure Blob-opslag biedt een kosteneffectieve en schaalbare oplossing voor het opslaan van grote hoeveelheden ongestructureerde gegevens in de cloud. Zie [Blobs uploaden, downloaden en aanbieden met de Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor een inleiding over Blob-opslag en het gebruik ervan.

In de volgende tabel worden de eigenschapsnamen en hun beschrijvingen voor het maken van een blob- of ADLS Gen2-uitvoer weergegeven.

| Naam van eigenschap       | Beschrijving                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Uitvoeralias        | Een vriendelijke naam die in query's wordt gebruikt om de queryuitvoer naar deze blobopslag te leiden. |
| Storage-account     | De naam van het opslagaccount waar u uw uitvoer verzendt.               |
| Opslagaccountsleutel | De geheime sleutel die is gekoppeld aan het opslagaccount.                              |
| Opslagcontainer   | Een logische groepering voor blobs die zijn opgeslagen in de Azure Blob-service. Wanneer u een blob uploadt naar de Blob-service, moet u een container voor die blob opgeven. |
| Padpatroon | Optioneel. Het patroon van het bestandspad dat wordt gebruikt om uw blobs in de opgegeven container te schrijven. <br /><br /> In het padpatroon u ervoor kiezen om een of meer exemplaren van de datum- en tijdvariabelen te gebruiken om de frequentie op te geven die blobs zijn geschreven: <br /> {date}, {time} <br /><br />U aangepaste blobpartities gebruiken om één aangepaste {veld} naam op te geven van uw gebeurtenisgegevens naar partitieblobs. De veldnaam is alfanumeriek en kan spaties, koppeltekens en underscores bevatten. Beperkingen voor aangepaste velden zijn onder andere: <ul><li>Veldnamen zijn niet hoofdlettergevoelig. De service kan bijvoorbeeld geen onderscheid maken tussen kolom 'ID' en kolom 'id'.</li><li>Geneste velden zijn niet toegestaan. Gebruik in plaats daarvan een alias in de taakquery om het veld te 'afvlakken'.</li><li>Expressies kunnen niet worden gebruikt als veldnaam.</li></ul> <br />Met deze functie u de specifiekeconfiguratie van de aangepaste datum-/tijdindeling in het pad gebruiken. Aangepaste datum- en tijdnotaties moeten één voor één\<worden opgegeven, ingesloten door het trefwoord {datetime: specificifier>}. Toegestane ingangen \<voor specifier> yyyy, MM, M, dd, dd, d, HH, H, mm, m, ss, of s. Het trefwoord {datetime:\<specifier>} kan meerdere keren in het pad worden gebruikt om aangepaste datum-/tijdconfiguraties te vormen. <br /><br />Voorbeelden: <ul><li>Voorbeeld 1: cluster1/logs/{date}/{time}</li><li>Voorbeeld 2: cluster1/logs/{date}</li><li>Voorbeeld 3: cluster1/{client_id}/{date}/{time}</li><li>Voorbeeld 4: cluster1/{datetime:ss}/{myField} waarbij de query is: SELECT data.myField AS myField FROM Input;</li><li>Voorbeeld 5: cluster1/jaar={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />De tijdstempel van de gemaakte mapstructuur volgt UTC en niet de lokale tijd.<br /><br />Bestandsnaamgeving gebruikt de volgende conventie: <br /><br />{Padvoorvoegingspatroon}/schemaHashcode_Guid_Number.extensie<br /><br />Voorbeelduitvoerbestanden:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Zie Azure Stream Analytics [aangepaste blob-uitvoerpartities](stream-analytics-custom-path-patterns-blob-storage-output.md)voor meer informatie over deze functie. |
| Datumnotatie | Optioneel. Als het datumtoken wordt gebruikt in het voorvoegselpad, u de datumnotatie selecteren waarin uw bestanden zijn ingedeeld. Voorbeeld: YYYY/MM/DD |
| Tijdnotatie | Optioneel. Als het tijdtoken wordt gebruikt in het voorvoegselpad, geeft u de tijdindeling op waarin uw bestanden zijn ingedeeld. Momenteel is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | Serialisatie-indeling voor uitvoergegevens. JSON, CSV, Avro en Parket worden ondersteund. |
|Minimumrijen (alleen parket)|Het aantal minimumrijen per batch. Voor Parket maakt elke batch een nieuw bestand. De huidige standaardwaarde is 2.000 rijen en het toegestane maximum is 10.000 rijen.|
|Maximale tijd (alleen parket)|De maximale wachttijd per batch. Na deze tijd wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste minimumrijen wordt voldaan. De huidige standaardwaarde is 1 minuut en het toegestane maximum is 2 uur. Als de blob-uitvoer de frequentie van het padpatroon heeft, kan de wachttijd niet hoger zijn dan het tijdsbereik van de partitie.|
| Encoding    | Als u de CSV- of JSON-indeling gebruikt, moet een codering worden opgegeven. UTF-8 is op dit moment het enige ondersteunde coderingsformaat. |
| Scheidingsteken   | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal veelvoorkomende scheidingstekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, puntkomma, ruimte, tabblad en verticale balk. |
| Indeling      | Alleen van toepassing op JSON serialisatie. **De scheiding van** de regel geeft aan dat de uitvoer wordt opgemaakt door elk JSON-object te laten scheiden door een nieuwe regel. Als u **Gescheiden lijn**selecteert, wordt de JSON één object tegelijk gelezen. De hele inhoud op zich zou niet een geldige JSON. **Array** geeft aan dat de uitvoer is opgemaakt als een array van JSON-objecten. Deze array wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijdvenster. In het algemeen is het beter om line-separate JSON te gebruiken, omdat het geen speciale behandeling vereist terwijl het uitvoerbestand nog steeds wordt geschreven. |

Wanneer u Blob-opslag als uitvoer gebruikt, wordt in de volgende gevallen een nieuw bestand gemaakt in de blob:

* Als het bestand het maximum aantal toegestane blokken overschrijdt (momenteel 50.000). U het maximaal toegestane aantal blokken bereiken zonder de maximaal toegestane blobgrootte te bereiken. Als de uitvoersnelheid bijvoorbeeld hoog is, u meer bytes per blok zien en is de bestandsgrootte groter. Als de uitvoersnelheid laag is, heeft elk blok minder gegevens en is de bestandsgrootte kleiner.
* Als er een schemawijziging in de uitvoer is en de uitvoerindeling een vast schema (CSV en Avro) vereist.
* Als een taak opnieuw wordt gestart, extern door een gebruiker die deze stopt en start, of intern voor systeemonderhoud of foutherstel.
* Als de query volledig is verdeeld en er voor elke uitvoerpartitie een nieuw bestand wordt gemaakt.
* Als de gebruiker een bestand of een container van het opslagaccount verwijdert.
* Als de uitvoer tijd wordt verdeeld met behulp van het padvoorvoegingspatroon en een nieuwe blob wordt gebruikt wanneer de query naar het volgende uur wordt verplaatst.
* Als de uitvoer wordt verdeeld door een aangepast veld en er per partitiesleutel een nieuwe blob wordt gemaakt als deze niet bestaat.
* Als de uitvoer wordt verdeeld door een aangepast veld waarbij de hoofddkardalinaliteit van de partitiesleutel meer dan 8.000 bedraagt en per partitiesleutel een nieuwe blob wordt gemaakt.

## <a name="event-hubs"></a>Event Hubs

De [Azure Event Hubs-service](https://azure.microsoft.com/services/event-hubs/) is een zeer schaalbare gebeurtenis-inname van een abonnement op een publiceren. Het kan miljoenen evenementen per seconde verzamelen. Een gebruik van een gebeurtenishub als uitvoer is wanneer de uitvoer van een Stream Analytics-taak de invoer wordt van een andere streamingtaak. Zie de sectie [uitvoerbatchgrootte](#output-batch-size) voor informatie over de maximale berichtgrootte en optimalisatie van batchgrootte.

U hebt een paar parameters nodig om gegevensstromen van gebeurtenishubs als uitvoer te configureren.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar deze gebeurtenishub te leiden. |
| Event hub-naamruimte | Een container voor een reeks berichtenentiteiten. Wanneer u een nieuwe gebeurtenishub hebt gemaakt, hebt u ook een naamruimte voor gebeurtenishubs gemaakt. |
| Naam van Event Hub | De naam van de uitvoer van de gebeurtenishub. |
| Naam van het beleid voor gebeurtenishub | Het beleid voor gedeelde toegang, dat u maken op het tabblad **Configureren van** de gebeurtenishub. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Beleidssleutel gebeurtenishub | De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de naamruimte van de gebeurtenishub te verifiëren. |
| Kolom partitiesleutel | Optioneel. Een kolom met de partitiesleutel voor het uitvoeren van gebeurtenishubs. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoergegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding | Voor CSV en JSON is UTF-8 op dit moment het enige ondersteunde coderingsformaat. |
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal veelvoorkomende scheidingstekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, puntkomma, ruimte, tabblad en verticale balk. |
| Indeling | Alleen van toepassing op JSON serialisatie. **De scheiding van** de regel geeft aan dat de uitvoer wordt opgemaakt door elk JSON-object te laten scheiden door een nieuwe regel. Als u **Gescheiden lijn**selecteert, wordt de JSON één object tegelijk gelezen. De hele inhoud op zich zou niet een geldige JSON. **Array** geeft aan dat de uitvoer is opgemaakt als een array van JSON-objecten.  |
| Eigenschapskolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden gekoppeld als gebruikerseigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [Aangepaste metagegevenseigenschappen voor uitvoer](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

U [Power BI](https://powerbi.microsoft.com/) gebruiken als uitvoer voor een Stream Analytics-taak om te zorgen voor een uitgebreide visualisatie-ervaring met analyseresultaten. U deze mogelijkheid gebruiken voor operationele dashboards, rapportgeneratie en metrische rapportage.

Power BI-uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's Azure China 21Vianet en Azure Germany (T-Systems International).

In de volgende tabel worden eigenschapsnamen en hun beschrijvingen weergegeven om uw Power BI-uitvoer te configureren.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Geef een vriendelijke naam op die wordt gebruikt in query's om de queryuitvoer naar deze Power BI-uitvoer te leiden. |
| Werkruimte groeperen |Als u het delen van gegevens met andere Power BI-gebruikers wilt inschakelen, u groepen selecteren in uw Power BI-account of **Mijn werkruimte** kiezen als u niet naar een groep wilt schrijven. Voor het bijwerken van een bestaande groep moet de Power BI-verificatie worden verlengd. |
| Naam van de gegevensset |Geef een gegevenssetnaam op die u wilt dat de Power BI-uitvoer gebruikt. |
| Tabelnaam |Geef een tabelnaam op onder de gegevensset van de Power BI-uitvoer. Op dit moment kan de Power BI-uitvoer vanuit Stream Analytics-taken slechts één tabel in een gegevensset bevatten. |
| Verbinding machtigen | U moet autoriseren met Power BI om uw uitvoerinstellingen te configureren. Zodra u deze uitvoertoegang tot uw Power BI-dashboard verleent, u de toegang intrekken door het wachtwoord van het gebruikersaccount te wijzigen, de taakuitvoer te verwijderen of de taak Stream Analytics te verwijderen. | 

Zie de zelfstudie [Azure Stream Analytics en Power BI](stream-analytics-power-bi-dashboard.md) voor een walkthrough van het configureren van een Power BI-uitvoer en -dashboard.

> [!NOTE]
> Maak de gegevensset en tabel niet expliciet in het Power BI-dashboard. De gegevensset en tabel worden automatisch ingevuld wanneer de taak wordt gestart en de taak begint met het pompen van uitvoer naar Power BI. Als de taakquery geen resultaten genereert, worden de gegevensset en tabel niet gemaakt. Als Power BI al een gegevensset en tabel had met dezelfde naam als die in deze streamanalytics-taak, worden de bestaande gegevens overschreven.
>

### <a name="create-a-schema"></a>Een schema maken
Azure Stream Analytics maakt een Power BI-gegevensset en tabelschema voor de gebruiker als deze nog niet bestaan. In alle andere gevallen wordt de tabel bijgewerkt met nieuwe waarden. Momenteel kan er slechts één tabel in een gegevensset bestaan. 

Power BI maakt gebruik van het first-in, first-out (FIFO) retentiebeleid. Gegevens verzamelen zich in een tabel totdat deze 200.000 rijen bereikt.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Een gegevenstype converteren van Stream Analytics naar Power BI
Azure Stream Analytics werkt het gegevensmodel dynamisch bij tijdens runtime als het uitvoerschema verandert. Kolomnaamwijzigingen, kolomtypewijzigingen en het toevoegen of verwijderen van kolommen worden allemaal bijgehouden.

Deze tabel bevat de gegevenstypeconversies van [Stream Analytics-gegevenstypen](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) naar [EDM-typen (Power](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)BI Entity Data Model), als een Power BI-gegevensset en -tabel niet bestaan.

Van Stream Analytics | Naar Power BI
-----|-----
bigint | Int64
nvarchar(max. | Tekenreeks
datum/tijd | Datum/tijd
float | Double
Recordarray | Tekenreekstype, constante waarde 'IRecord' of 'IArray'

### <a name="update-the-schema"></a>Het schema bijwerken
Stream Analytics leidt het gegevensmodelschema af op basis van de eerste set gebeurtenissen in de uitvoer. Later wordt het gegevensmodelschema, indien nodig, bijgewerkt om inkomende gebeurtenissen aan te passen die mogelijk niet in het oorspronkelijke schema passen.

Vermijd `SELECT *` de query om dynamische schema-updates tussen rijen te voorkomen. Naast mogelijke gevolgen voor de prestaties, kan dit leiden tot onzekerheid over de tijd die nodig is voor de resultaten. Selecteer de exacte velden die moeten worden weergegeven op het Power BI-dashboard. Bovendien moeten de gegevenswaarden voldoen aan het gekozen gegevenstype.


Vorige/huidige | Int64 | Tekenreeks | Datum/tijd | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Tekenreeks | Tekenreeks | Double
Double | Double | Tekenreeks | Tekenreeks | Double
Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks 
Datum/tijd | Tekenreeks | Tekenreeks |  Datum/tijd | Tekenreeks

## <a name="table-storage"></a>Table Storage

[Azure Table-opslag](../storage/common/storage-introduction.md) biedt zeer beschikbare, enorm schaalbare opslag, zodat een toepassing automatisch kan schalen om aan de vraag van de gebruiker te voldoen. Tabelopslag is het NoSQL-sleutel-/kenmerkarchief van Microsoft, dat u gebruiken voor gestructureerde gegevens met minder beperkingen op het schema. Azure Table-opslag kan worden gebruikt om gegevens op te slaan voor persistentie en efficiënt ophalen.

In de volgende tabel worden de eigenschapsnamen en hun beschrijvingen voor het maken van een tabeluitvoer weergegeven.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar deze tabelopslag te leiden. |
| Storage-account |De naam van het opslagaccount waar u uw uitvoer verzendt. |
| Opslagaccountsleutel |De toegangssleutel die is gekoppeld aan het opslagaccount. |
| Tabelnaam |De naam van de tafel. De tabel wordt gemaakt als deze niet bestaat. |
| Partitiesleutel |De naam van de uitvoerkolom die de partitiesleutel bevat. De partitiesleutel is een unieke id voor de partitie in een tabel die het eerste deel van de primaire sleutel van een entiteit vormt. Het is een tekenreekswaarde die tot 1 KB groot kan zijn. |
| Rijtoets |De naam van de uitvoerkolom die de rijsleutel bevat. De rijsleutel is een unieke id voor een entiteit binnen een partitie. Het vormt het tweede deel van de primaire sleutel van een entiteit. De rijsleutel is een tekenreekswaarde die maximaal 1 KB groot kan zijn. |
| Batchgrootte |Het aantal records voor een batchbewerking. De standaardwaarde (100) is voldoende voor de meeste taken. Zie de [spec van tabelbatchbewerking](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) voor meer informatie over het wijzigen van deze instelling. |

## <a name="service-bus-queues"></a>Service Bus-wachtrijen

[Service Bus wachtrijen](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) bieden een FIFO bericht levering aan een of meer concurrerende consumenten. Berichten worden doorgaans ontvangen en verwerkt door de ontvangers in de tijdelijke volgorde waarin ze aan de wachtrij zijn toegevoegd. Elk bericht wordt ontvangen en verwerkt door slechts één berichtconsument.

In [compatibiliteitsniveau 1.2](stream-analytics-compatibility-level.md)gebruikt Azure Stream Analytics [het ADQP-berichtenprotocol (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) om naar wachtrijen en onderwerpen van servicebussen te schrijven. AMQP stelt u in staat om cross-platform, hybride applicaties te bouwen met behulp van een open standaard protocol.

In de volgende tabel worden de eigenschapsnamen en hun beschrijvingen voor het maken van een wachtrijuitvoer weergegeven.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar deze wachtrij voor servicebus te leiden. |
| Naamruimte servicebus |Een container voor een reeks berichtenentiteiten. |
| Wachtrijnaam |De naam van de wachtrij voor de servicebus. |
| Naam wachtrijbeleid |Wanneer u een wachtrij maakt, u ook beleid voor gedeelde toegang maken op het tabblad **Configureren in** de wachtrij. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Wachtrijbeleidssleutel |De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de naamruimte van de servicebus te verifiëren. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoergegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Voor CSV en JSON is UTF-8 op dit moment het enige ondersteunde coderingsformaat. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal veelvoorkomende scheidingstekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, puntkomma, ruimte, tabblad en verticale balk. |
| Indeling |Alleen van toepassing op JSON-type. **De scheiding van** de regel geeft aan dat de uitvoer wordt opgemaakt door elk JSON-object te laten scheiden door een nieuwe regel. Als u **Gescheiden lijn**selecteert, wordt de JSON één object tegelijk gelezen. De hele inhoud op zich zou niet een geldige JSON. **Array** geeft aan dat de uitvoer is opgemaakt als een array van JSON-objecten. |
| Eigenschapskolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden gekoppeld als gebruikerseigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [Aangepaste metagegevenseigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolommen met systeemeigenschap | Optioneel. Sleutelwaardeparen van systeemeigenschappen en bijbehorende kolomnamen die aan het uitgaande bericht moeten worden gekoppeld in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [Systeemeigenschappen voor servicebuswachtrij- en onderwerpuitvoer](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Het aantal partities is [gebaseerd op de Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). Partitiesleutel is een unieke gehele waarde voor elke partitie.

## <a name="service-bus-topics"></a>Service Bus-onderwerpen
ServiceBuswachtrijen bieden een één-op-één communicatiemethode van verzender naar ontvanger. [Service Bus onderwerpen](https://msdn.microsoft.com/library/azure/hh367516.aspx) bieden een een-op-veel vorm van communicatie.

In de volgende tabel worden de eigenschapsnamen en hun beschrijvingen vermeld voor het maken van een onderwerpuitvoer van servicebus.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar dit onderwerp servicebus te leiden. |
| Naamruimte servicebus |Een container voor een reeks berichtenentiteiten. Wanneer u een nieuwe gebeurtenishub hebt gemaakt, hebt u ook een naamruimte voor servicebus gemaakt. |
| Onderwerpnaam |Onderwerpen zijn berichtenentiteiten, vergelijkbaar met gebeurtenishubs en wachtrijen. Ze zijn ontworpen om gebeurtenisstreams te verzamelen van apparaten en services. Wanneer een onderwerp wordt gemaakt, krijgt het ook een specifieke naam. De berichten die naar een onderwerp worden verzonden, zijn alleen beschikbaar als er een abonnement is gemaakt, dus zorg ervoor dat er een of meer abonnementen onder het onderwerp staan. |
| Naam van onderwerpbeleid |Wanneer u een servicebusonderwerp maakt, u ook beleid voor gedeelde toegang maken op het tabblad **Configureren van** het onderwerp. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. |
| Themabeleidssleutel |De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de naamruimte van de servicebus te verifiëren. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoergegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Als u de CSV- of JSON-indeling gebruikt, moet een codering worden opgegeven. UTF-8 is op dit moment het enige ondersteunde coderingsformaat. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal veelvoorkomende scheidingstekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, puntkomma, ruimte, tabblad en verticale balk. |
| Eigenschapskolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden gekoppeld als gebruikerseigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [Aangepaste metagegevenseigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolommen met systeemeigenschap | Optioneel. Sleutelwaardeparen van systeemeigenschappen en bijbehorende kolomnamen die aan het uitgaande bericht moeten worden gekoppeld in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [Systeemeigenschappen voor servicebuswachtrij- en onderwerpuitvoer](#system-properties-for-service-bus-queue-and-topic-outputs) |

Het aantal partities is [gebaseerd op de Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitiesleutel is een unieke gehele waarde voor elke partitie.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is een wereldwijd gedistribueerde databaseservice die onbeperkte elastische schaal over de hele wereld biedt, uitgebreide query's en automatische indexering ten opzichte van schema-agnostische gegevensmodellen. Zie het [streamanalyseartikel Stream Analytics met Azure Cosmos DB als uitvoerartikel voor](stream-analytics-documentdb-output.md) meer informatie over Azure Cosmos DB-containeropties voor Stream Analytics.

Azure Cosmos DB-uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's Azure China 21Vianet en Azure Germany (T-Systems International).

> [!Note]
> Op dit moment ondersteunt Azure Stream Analytics alleen verbinding met Azure Cosmos DB met behulp van de SQL API.
> Andere Azure Cosmos DB API's worden nog niet ondersteund. Als u Azure Stream Analytics aanwijst naar de Azure Cosmos DB-accounts die met andere API's zijn gemaakt, worden de gegevens mogelijk niet goed opgeslagen.

In de volgende tabel worden de eigenschappen beschreven voor het maken van een Azure Cosmos DB-uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een alias om deze uitvoer door te verwijzen in uw Stream Analytics-query. |
| Sink | Azure Cosmos DB. |
| Importoptie | Kies **Cosmos DB selecteren in uw abonnement** of Cosmos **DB-instellingen handmatig opgeven.**
| Account-id | De naam of eindpunt URI van het Azure Cosmos DB-account. |
| Accountsleutel | De gedeelde toegangssleutel voor het Azure Cosmos DB-account. |
| Database | De naam van de Azure Cosmos DB-database. |
| Containernaam | De te gebruiken containernaam, die in Kosmos DB moet bestaan. Voorbeeld:  <br /><ul><li> _MyContainer_: Een container met de naam "MyContainer" moet bestaan.</li>|
| Document-id |Optioneel. De naam van het veld in uitvoergebeurtenissen die worden gebruikt om de primaire sleutel op te geven waarop invoeg- of updatebewerkingen zijn gebaseerd.

## <a name="azure-functions"></a>Azure Functions
Azure Functions is een serverloze compute-service die u gebruiken om code on-demand uit te voeren zonder dat u de infrastructuur expliciet hoeft in te richten of te beheren. Hiermee u code implementeren die wordt geactiveerd door gebeurtenissen die zich voordoen in Azure- of partnerservices. Deze mogelijkheid van Azure-functies om te reageren op triggers maakt het een natuurlijke uitvoer voor Azure Stream Analytics. Met deze uitvoeradapter kunnen gebruikers Stream Analytics verbinden met Azure-functies en een script of een code uitvoeren als reactie op verschillende gebeurtenissen.

Azure Functions-uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's Azure China 21Vianet en Azure Germany (T-Systems International).

Azure Stream Analytics roept Azure Functions aan via HTTP-triggers. De uitvoeradapter Azure Functions is beschikbaar met de volgende configureerbare eigenschappen:

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Function App |De naam van uw Azure Functions-app. |
| Functie |De naam van de functie in uw Azure Functions-app. |
| Sleutel |Als u een Azure-functie van een ander abonnement wilt gebruiken, u dit doen door de sleutel op te geven om toegang te krijgen tot uw functie. |
| Maximale batchgrootte |Een eigenschap waarmee u de maximale grootte instellen voor elke uitvoerbatch die naar uw Azure-functie wordt verzonden. De invoereenheid bevindt zich in bytes. Standaard bedraagt deze waarde 262.144 bytes (256 KB). |
| Maximum aantal batch's  |Een eigenschap waarmee u het maximum aantal gebeurtenissen opgeven in elke batch die naar Azure-functies wordt verzonden. De standaardwaarde is 100. |

Azure Stream Analytics verwacht HTTP-status 200 van de functie-app voor batches die met succes zijn verwerkt.

Wanneer Azure Stream Analytics een uitzondering van 413 ('http-aanvraagentiteit te groot') ontvangt van een Azure-functie, wordt de grootte van de batches die naar Azure-functies worden verzendt, verminderd. Gebruik deze uitzondering in uw Azure-functiecode om ervoor te zorgen dat Azure Stream Analytics geen te grote batches verzendt. Zorg er ook voor dat het maximale aantal batch- en groottewaarden dat in de functie wordt gebruikt, overeenkomt met de waarden die zijn ingevoerd in de Stream Analytics-portal.

> [!NOTE]
> Tijdens de testverbinding stuurt Stream Analytics een lege batch naar Azure Functions om te testen of de verbinding tussen de twee werkt. Zorg ervoor dat de app Functies lege batchaanvragen verwerkt om ervoor te zorgen dat de testverbinding slaagt.

Ook in een situatie waarin er geen gebeurtenis in een tijdvenster wordt geland, wordt er geen uitvoer gegenereerd. Als gevolg hiervan wordt de **computeResult-functie** niet aangeroepen. Dit gedrag komt overeen met de ingebouwde samengevoegde functies.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste metagegevenseigenschappen voor uitvoer 

U querykolommen toevoegen als gebruikerseigenschappen aan uw uitgaande berichten. Deze kolommen gaan niet in op de lading. De eigenschappen zijn aanwezig in de vorm van een woordenboek op het uitvoerbericht. *Sleutel* is de kolomnaam en *waarde* is de kolomwaarde in de eigenschappenwoordenboek. Alle Stream Analytics-gegevenstypen worden ondersteund, behalve Record en Array.  

Ondersteunde uitgangen: 
* Service Bus-wachtrij 
* Service Bus-onderwerp 
* Event Hub 

In het volgende voorbeeld voegen `DeviceId` we `DeviceStatus` de twee velden en de metagegevens toe. 
* Query:`select *, DeviceId, DeviceStatus from iotHubInput`
* Uitvoerconfiguratie:`DeviceId,DeviceStatus`

![Eigenschapskolommen](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

In de volgende schermafbeelding worden uitvoerberichteigenschappen weergegeven die zijn geïnspecteerd in EventHub via [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer)

![Aangepaste eigenschappen voor gebeurtenissen](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Systeemeigenschappen voor servicebuswachtrij- en onderwerpuitvoer 
U querykolommen als [systeemeigenschappen](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) koppelen aan uw uitgaande servicebus Wachtrij of Onderwerp berichten. Deze kolommen gaan niet in op de payload in plaats van de overeenkomstige [eigenschap BrokeredMessage-systeem](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) wordt gevuld met de waarden van de querykolom.
Deze systeemeigenschappen worden `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`ondersteund - .
Tekenreekswaarden van deze kolommen worden ontleed als het bijbehorende type waarde van de systeemeigenschap en eventuele parsingsfouten worden behandeld als gegevensfouten.
Dit veld wordt geleverd als JSON-objectindeling. Details over deze indeling zijn als volgt -
* Omringd door krullende beugels. {}
* Geschreven in sleutel/waardeparen.
* Toetsen en waarden moeten tekenreeksen zijn.
* Sleutel is de naam van de systeemeigenschap en de waarde is de naam van de querykolom.
* Toetsen en waarden worden gescheiden door een dikke darm.
* Elk sleutel/waardepaar wordt gescheiden door een komma.

Dit laat zien hoe deze eigenschap te gebruiken -

* Query:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolommen met systeemeigenschappen:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Hiermee worden `MessageId` de berichten met `column1`de wachtrijberichten van de `column2`servicebus met 's-waarden en PartitionKey ingesteld met 's-waarden.

## <a name="partitioning"></a>Partitionering

In de volgende tabel worden de partitieondersteuning en het aantal uitvoerschrijvers voor elk uitvoertype samengevat:

| Uitvoertype | Ondersteuning voor partitionering | Partitiesleutel  | Aantal outputschrijvers |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Gebruik {date} en {time} tokens in het padvoorvoegingspatroon. Kies de datumnotatie, zoals YYYY/MM/DD, DD/MM/YYYY of MM-DD-YYYY. HH wordt gebruikt voor het tijdsformaat. | Volgt de invoerpartitionering voor [volledig parallelizable query's.](stream-analytics-scale-jobs.md) |
| Azure SQL Database | Ja, moet ingeschakeld worden. | Gebaseerd op de COMPONENT VERDELING BY in de query. | Wanneer de optie Partitionering overnemen is ingeschakeld, volgt u de invoerpartitie voor [volledig parallelizable query's.](stream-analytics-scale-jobs.md) Zie [Azure Stream Analytics-uitvoer naar Azure SQL Database](stream-analytics-sql-output-perf.md)voor meer informatie over het bereiken van betere schrijfdoorvoerprestaties wanneer u gegevens laadt in Azure SQL Database. |
| Azure Blob Storage | Ja | Gebruik {date} en {time} tokens uit uw gebeurtenisvelden in het padpatroon. Kies de datumnotatie, zoals YYYY/MM/DD, DD/MM/YYYY of MM-DD-YYYY. HH wordt gebruikt voor het tijdsformaat. Blob-uitvoer kan worden verdeeld door één aangepast gebeurteniskenmerk {veldnaam} of {datetime:\<specificifier>}. | Volgt de invoerpartitionering voor [volledig parallelizable query's.](stream-analytics-scale-jobs.md) |
| Azure Event Hubs | Ja | Ja | Varieert afhankelijk van de verdeling uitlijning.<br /> Wanneer de partitiesleutel voor gebeurtenishub-uitvoer even goed is uitgelijnd met de upstreamquerystap (vorige query, is het aantal schrijvers gelijk aan het aantal partities in gebeurtenishub-uitvoer. Elke schrijver gebruikt de [klasse EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) om gebeurtenissen naar de specifieke partitie te verzenden. <br /> Wanneer de partitiesleutel voor gebeurtenishub-uitvoer niet is uitgelijnd met de upstreamquerystap (vorige query, is het aantal schrijvers gelijk aan het aantal partities in die vorige stap. Elke schrijver gebruikt de [klasse SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** om gebeurtenissen naar alle uitvoerpartities te verzenden. |
| Power BI | Nee | Geen | Niet van toepassing. |
| Azure Table Storage | Ja | Elke uitvoerkolom.  | Volgt de invoerpartitionering voor [volledig parallelle query's.](stream-analytics-scale-jobs.md) |
| Azure Service Bus-onderwerp | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitiesleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in het uitvoeronderwerp.  |
| Azure Service Bus-wachtrij | Ja | Automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitiesleutel is een unieke gehele waarde voor elke partitie.| Hetzelfde als het aantal partities in de uitvoerwachtrij. |
| Azure Cosmos DB | Ja | Gebaseerd op de COMPONENT VERDELING BY in de query. | Volgt de invoerpartitionering voor [volledig parallelle query's.](stream-analytics-scale-jobs.md) |
| Azure Functions | Ja | Gebaseerd op de COMPONENT VERDELING BY in de query. | Volgt de invoerpartitionering voor [volledig parallelle query's.](stream-analytics-scale-jobs.md) |

Het aantal output schrijvers kan `INTO <partition count>` ook worden gecontroleerd met behulp van (zie [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)clausule in uw query, die nuttig kan zijn bij het bereiken van een gewenste baan topologie. Als uw uitvoeradapter niet is verdeeld, zorgt het ontbreken van gegevens in één invoerpartitie voor vertraging tot de late aankomsthoeveelheid. In dergelijke gevallen wordt de uitvoer samengevoegd tot één schrijver, wat knelpunten in uw pijplijn kan veroorzaken. Zie overwegingen voor [gebeurtenisvolgorde van Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)voor meer informatie over het beleid voor late aankomst.

## <a name="output-batch-size"></a>Grootte van de uitvoerbatch
Azure Stream Analytics maakt gebruik van batches van variabele grootte om gebeurtenissen te verwerken en naar uitvoer te schrijven. Meestal schrijft de Stream Analytics-engine niet één bericht tegelijk en gebruikt ze batches voor efficiëntie. Wanneer de snelheid van zowel de inkomende als uitgaande gebeurtenissen hoog is, maakt Stream Analytics gebruik van grotere batches. Wanneer de uitgangssnelheid laag is, gebruikt het kleinere batches om de latentie laag te houden.

In de volgende tabel worden enkele overwegingen voor outputbatching uitgelegd:

| Uitvoertype |    Maximale berichtgrootte | Optimalisatie van batchgrootte |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zie [Limieten voor gegevensmeeropslag](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Gebruik tot 4 MB per schrijfbewerking. |
| Azure SQL Database | Configureerbaar met het aantal Max-batch.Configureable using Max batch count. Standaard 10.000 maximale en 100 minimale rijen per bulkinvoeging.<br />Zie [Azure SQL-limieten](../sql-database/sql-database-resource-limits.md). |  Elke batch wordt in eerste instantie bulk ingevoegd met maximale batch tellen. Batch wordt in tweeën gesplitst (tot minimaal batchaantal) op basis van opnieuw te proberen fouten van SQL. |
| Azure Blob Storage | Zie [Azure Storage-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | De maximale blobblokgrootte is 4 MB.<br />Het maximale aantal blobbocks is 50.000. |
| Azure Event Hubs    | 256 KB of 1 MB per bericht. <br />Zie [Limieten voor gebeurtenishubs](../event-hubs/event-hubs-quotas.md). |    Wanneer de invoer/uitvoerpartitionering niet is uitgelijnd, wordt `EventData` elke gebeurtenis afzonderlijk verpakt en verzonden in een batch van maximaal de maximale berichtgrootte. Dit gebeurt ook als [aangepaste metagegevenseigenschappen](#custom-metadata-properties-for-output) worden gebruikt. <br /><br />  Wanneer invoer-/uitvoerpartitionering is uitgelijnd, worden meerdere `EventData` gebeurtenissen in één instantie verpakt, tot de maximale berichtgrootte, en verzonden.    |
| Power BI | Zie [Power BI Rest API-limieten](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zie [Azure Storage-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | De standaardinstelling is 100 entiteiten per afzonderlijke transactie. U het zo nodig configureren naar een kleinere waarde. |
| Azure Service Bus-wachtrij    | 256 KB per bericht voor Standaardlaag, 1 MB voor Premium-laag.<br /> Zie [ServiceBus-limieten](../service-bus-messaging/service-bus-quotas.md). | Gebruik één gebeurtenis per bericht. |
| Azure Service Bus-onderwerp | 256 KB per bericht voor Standaardlaag, 1 MB voor Premium-laag.<br /> Zie [ServiceBus-limieten](../service-bus-messaging/service-bus-quotas.md). | Gebruik één gebeurtenis per bericht. |
| Azure Cosmos DB    | Zie [Azure Cosmos DB-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batchgrootte en schrijffrequentie worden dynamisch aangepast op basis van Azure Cosmos DB-antwoorden. <br /> Er zijn geen vooraf bepaalde beperkingen van Stream Analytics. |
| Azure Functions    | | De standaardbatchgrootte is 262.144 bytes (256 KB). <br /> Het standaardaantal gebeurtenissen per batch is 100. <br /> De batchgrootte is configureerbaar en kan worden verhoogd of verlaagd in de [uitvoeropties](#azure-functions)van Stream Analytics.

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
