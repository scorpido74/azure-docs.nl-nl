---
title: Een IoT-oplossing bouwen met Azure Stream Analytics
description: Zelfstudie aan de slag voor de Stream Analytics IoT-oplossing van een tolhuisjescenario
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426442"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Een IoT-oplossing bouwen met Stream Analytics

## <a name="introduction"></a>Inleiding
In deze oplossing leert u hoe u Azure Stream Analytics gebruiken om realtime inzichten uit uw gegevens te krijgen. Ontwikkelaars kunnen gegevensstromen, zoals klikstromen, logboeken en door apparaten gegenereerde gebeurtenissen, eenvoudig combineren met historische records of referentiegegevens om bedrijfsinzichten te verkrijgen. Als een volledig beheerde, real-time streamberekeningsservice die wordt gehost in Microsoft Azure, biedt Azure Stream Analytics ingebouwde tolerantie, lage latentie en schaalbaarheid om u binnen enkele minuten aan de slag te krijgen.

Na het voltooien van deze oplossing u:

* Maak kennis met de Azure Stream Analytics-portal.
* Een streamingtaak configureren en implementeren.
* Beverwoord echte problemen en los ze op met behulp van de querytaal Stream Analytics.
* Ontwikkel streamingoplossingen voor uw klanten door Stream Analytics met vertrouwen te gebruiken.
* Gebruik de controle- en logboekervaring om problemen op te lossen.

## <a name="prerequisites"></a>Vereisten
U hebt de volgende vereisten nodig om deze oplossing te voltooien:
* Een [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenario-introductie: "Hallo, Tol!"
Een tolstation is een veel voorkomend fenomeen. Je komt ze tegen op vele snelwegen, bruggen en tunnels over de hele wereld. Elk tolstation heeft meerdere tolhuisjes. Bij handmatige cabines stop je om de tol te betalen aan een begeleider. Bij geautomatiseerde cabines scant een sensor bovenop elke stand een RFID-kaart die op de voorruit van uw voertuig is aangebracht terwijl u de tolcabine passeert. Het is gemakkelijk om de doorgang van voertuigen door deze tolstations te visualiseren als een gebeurtenisstroom waarover interessante bewerkingen kunnen worden uitgevoerd.

![De auto's bij tolcabines](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Binnenkomende gegevens
Deze oplossing werkt met twee gegevensstromen. Sensoren geïnstalleerd in de in- en uitgang van de tolstations produceren de eerste stroom. De tweede stroom is een statische opzoekgegevensset met voertuigregistratiegegevens.

### <a name="entry-data-stream"></a>Gegevensstroom invoeren
De invoergegevensstroom bevat informatie over auto's die tolstations binnenkomen. De exitdata-gebeurtenissen worden live gestreamd naar een gebeurtenishubwachtrij vanuit een web-app die is opgenomen in de voorbeeld-app.

| Tol-ID | Instaptijd | Kenteken | Status | Merk | Model | Voertuigtype | VoertuigGewicht | Tol | Label |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |Crv |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |Crv |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Hier is een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| Tol-ID |De tolpoort-ID die op unieke wijze een tolhuisje identificeert |
| Instaptijd |De datum en het tijdstip van binnenkomst van het voertuig naar de tolpoort in UTC |
| Kenteken |Het kenteken van het voertuig |
| Status |Een staat in De Verenigde Staten |
| Merk |De fabrikant van de auto |
| Model |Het modelnummer van de auto |
| Voertuigtype |1 voor personenauto's of 2 voor bedrijfsvoertuigen |
| WeightType |Gewicht van het voertuig in tonnen; 0 voor personenauto's |
| Tol |De tolwaarde in USD |
| Label |De e-Tag op de auto die de betaling automatiseert; leeg wanneer de betaling handmatig is gedaan |

### <a name="exit-data-stream"></a>Gegevensstroom afsluiten
De exit data stream bevat informatie over auto's die het tolstation verlaten. De exitdata-gebeurtenissen worden live gestreamd naar een gebeurtenishubwachtrij vanuit een web-app die is opgenomen in de voorbeeld-app.

| **Toli** | **ExitTime** | **Kenteken** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00000000Z |CDE 1007 |

Hier is een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| Tol-ID |De tolpoort-ID die op unieke wijze een tolhuisje identificeert |
| ExitTime |De datum en het tijdstip van het verlaten van het voertuig van tolpoort in UTC |
| Kenteken |Het kenteken van het voertuig |

### <a name="commercial-vehicle-registration-data"></a>Registratiegegevens voor bedrijfsvoertuigen
De oplossing maakt gebruik van een statische momentopname van een database voor de registratie van bedrijfsvoertuigen. Deze gegevens worden opgeslagen als een JSON-bestand in Azure blob-opslag, opgenomen in het voorbeeld.

| Kenteken | RegistratieId | Verlopen |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Hier is een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| Kenteken |Het kenteken van het voertuig |
| RegistratieId |De kenteken-id van het voertuig |
| Verlopen |De registratiestatus van het voertuig: 0 als de registratie van het voertuig actief is, 1 als de registratie is verlopen |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>De omgeving instellen voor Azure Stream Analytics
Om deze oplossing te voltooien, hebt u een Microsoft Azure-abonnement nodig. Als u geen Azure-account hebt, u [een gratis proefversie aanvragen.](https://azure.microsoft.com/pricing/free-trial/)

Volg de stappen in de sectie 'Uw Azure-account opschonen' aan het einde van dit artikel, zodat u uw Azure-tegoed optimaal gebruiken.

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren
Er zijn verschillende resources die gemakkelijk kunnen worden geïmplementeerd in een resourcegroep, samen met een paar klikken. De oplossingsdefinitie wordt gehost in [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)gitHub-repository op .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>De tollApp-sjabloon implementeren in de Azure-portal
1. Als u de TollApp-omgeving wilt implementeren in Azure, gebruikt u deze koppeling voor [Implementatie TollApp Azure Template](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Meld u aan bij de Azure-portal als u daarom wordt gevraagd.

3. Kies het abonnement waarin de verschillende resources worden gefactureerd.

4. Geef een nieuwe resourcegroep op, met `MyTollBooth`bijvoorbeeld een unieke naam.

5. Selecteer een Azure-locatie.

6. Geef een **interval** op als een aantal seconden. Deze waarde wordt gebruikt in de voorbeeldweb-app voor hoe vaak gegevens naar gebeurtenishub worden verzonden.

7. **Controleer** of u akkoord gaat met de algemene voorwaarden.

8. Selecteer **Vastmaken aan het dashboard,** zodat u de bronnen later eenvoudig vinden.

9. Selecteer **Kopen** om de voorbeeldsjabloon te implementeren.

10. Na enkele ogenblikken verschijnt er een melding om te bevestigen dat de **implementatie is geslaagd.**

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>De TollApp-bronnen van Azure Stream Analytics bekijken

1. Aanmelden bij Azure Portal

2. Zoek de resourcegroep die u in de vorige sectie hebt genoemd.

3. Controleer of de volgende bronnen in de resourcegroep worden vermeld:
   - Eén Cosmos DB-account
   - Eén Azure Stream Analytics-taak
   - Eén Azure-opslagaccount
   - Eén Azure-gebeurtenishub
   - Twee webapps

## <a name="examine-the-sample-tollapp-job"></a>De voorbeeldtaak TollApp onderzoeken
1. Selecteer vanaf de brongroep in de vorige sectie de streamingtaak Stream Analytics die begint met de **naamtolapp** (naam bevat willekeurige tekens voor uniciteit).

2. Op de pagina **Overzicht** van de taak ziet u het vak **Query** om de querysyntaxis weer te geven.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Als u de bedoeling van de query wilt parafraseren, moet u zeggen dat u het aantal voertuigen moet tellen dat een tolhuisje binnenkomt. Omdat een snelweg tolcabine heeft een continue stroom van voertuigen invoeren, dat zijn ingang gebeurtenissen zijn analoog aan een stroom die nooit stopt. Om de stroom te kwantificeren, moet u een "periode" definiëren om over te meten. Laten we de vraag verder verfijnen, tot "Hoeveel voertuigen komen elke drie minuten een tolhuisje binnen?" Dit wordt vaak aangeduid als de tumbling tellen.

   Zoals u zien, gebruikt Azure Stream Analytics een querytaal die lijkt op SQL en voegt een paar extensies toe om tijdgerelateerde aspecten van de query op te geven.  Lees voor meer informatie over [tijdbeheer-](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) en [vensterconstructies](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) die in de query worden gebruikt.

3. Bekijk de ingangen van de voorbeeldtaak TollApp. Alleen de EntryStream-invoer wordt gebruikt in de huidige query.
   - **EntryStream-invoer** is een Event Hub-verbinding die gegevens in de wachtrij plaatst die elke keer vertegenwoordigen wanneer een auto een tolhuisje op de snelweg binnenrijdt. Een web-app die deel uitmaakt van het voorbeeld maakt de gebeurtenissen en die gegevens staan in de wachtrij in deze gebeurtenishub. Houd er rekening mee dat deze invoer wordt opgevraagd in de FROM-component van de streamingquery.
   - **ExitStream-invoer** is een Gebeurtenishub-verbinding die gegevens in de wachtrij en die elke keer dat een auto een tolpoort op de snelweg verlaat, in de wachtrij en deze weergeeft. Deze streaming-invoer wordt gebruikt in latere varianten van de querysyntaxis.
   - **Registratie-invoer** is een Azure Blob-opslagverbinding die wijst op een statisch bestand registration.json, dat wordt gebruikt voor opzoekingen als dat nodig is. Deze referentiegegevensinvoer wordt gebruikt in latere varianten van de querysyntaxis.

4. Bekijk de uitvoer van de voorbeeldtaak TollApp.
   - **Cosmos DB-uitvoer** is een Cosmos-databasecontainer die de gebeurtenissen voor uitvoersink ontvangt. Houd er rekening mee dat deze uitvoer wordt gebruikt in de INTO-clausule van de streamingquery.

## <a name="start-the-tollapp-streaming-job"></a>De streamingtaak TollApp starten
Volg de volgende stappen om de streamingtaak te starten:

1. Selecteer **Start**op de pagina **Overzicht** van de taak .

2. Selecteer **Nu**in het **taakvenster Starten** .

3. Na een paar ogenblikken, zodra de taak wordt uitgevoerd, bekijkt u op de **pagina Overzicht** van de streamingtaak de grafiek **Controle.** De grafiek moet enkele duizenden invoergebeurtenissen en tientallen uitvoergebeurtenissen weergeven.

## <a name="review-the-cosmosdb-output-data"></a>Bekijk de uitvoergegevens van CosmosDB
1. Zoek de brongroep die de TollApp-bronnen bevat.

2. Selecteer het Azure Cosmos DB-account met het naampatroon **tolapp\<\>random -cosmos**.

3. Selecteer het kopje **Gegevensverkenner** om de pagina Gegevensverkenner te openen.

4. De > **tolappDatabase-tolcollectiedocumenten** > **Documents**uitbreiden. **tollAppDatabase**

5. In de lijst met id's worden verschillende documenten weergegeven zodra de uitvoer beschikbaar is.

6. Selecteer elke id om het JSON-document te bekijken. Let op elke tollid, windowend tijd, en de telling van auto's uit dat venster.

7. Na nog eens drie minuten is er nog een set van vier documenten beschikbaar, één document per tollid.


## <a name="report-total-time-for-each-car"></a>Totale tijd voor elke auto rapporteren
De gemiddelde tijd die nodig is voor een auto om de tol te passeren helpt bij het beoordelen van de efficiëntie van het proces en de klantervaring.

Als u de totale tijd wilt vinden, sluit u aan bij de EntryTime-stream met de ExitTime-stream. Sluit je aan bij de twee invoerstromen op de kolommen gelijke overeenkomende tollid- en licentieplaatkolommen. De **JOIN-operator** vereist dat u tijdelijke speelruimte opgeeft die het aanvaardbare tijdsverschil tussen de samengevoegde gebeurtenissen beschrijft. Gebruik de functie **DATEDIFF** om op te geven dat gebeurtenissen niet meer dan 15 minuten van elkaar mogen zijn. Pas ook de **FUNCTIE DATEDIFF** toe op uit- en instaptijden om de werkelijke tijd te berekenen die een auto in het tolstation doorbrengt. Let op het verschil in het gebruik van **DATEDIFF** wanneer het wordt gebruikt in een **SELECT-instructie** in plaats van een **JOIN-voorwaarde.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Ga als een overzicht van de syntaxis van de streamingtaak van TollApp:

1. Selecteer Op de pagina **Overzicht** van de taak de optie **Stoppen**.

2. Wacht even op de melding dat de taak is gestopt.

3. Selecteer onder de kop TAAKTOPOLOGIE de< > **Query**

4. Plak de aangepaste SQL-query voor streaming.

5. Selecteer **Opslaan** om de query op te slaan. Bevestig **Ja** om de wijzigingen op te slaan.

6. Selecteer **Start**op de pagina **Overzicht** van de taak .

7. Selecteer **Nu**in het **taakvenster Starten** .

### <a name="review-the-total-time-in-the-output"></a>De totale tijd in de uitvoer bekijken
Herhaal de stappen in de vorige sectie om de CosmosDB-uitvoergegevens van de streamingtaak te bekijken. Bekijk de nieuwste JSON-documenten.

Dit document toont bijvoorbeeld een voorbeeldauto met een bepaald kenteken, de in- en uitstaptijd en het door DATEDIFF berekende veld (timeinminutes) met de duur van de tolcabine als twee minuten:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Voertuigen met verlopen registratie melden
Azure Stream Analytics kan statische momentopnamen van referentiegegevens gebruiken om samen te werken met tijdelijke gegevensstromen. Gebruik de volgende voorbeeldvraag om deze mogelijkheid aan te tonen. De registratie-invoer is een statisch blobjson-bestand met de vervaldatums van licentietags. Door het samenvoegen op het kenteken, worden de referentiegegevens vergeleken met elk voertuig dat door de tol beide.

Als een bedrijfsvoertuig is geregistreerd bij het tolbedrijf, kan het door de tolcabine gaan zonder te worden gestopt voor inspectie. Gebruik de opzoektabel voor registratie om alle bedrijfsvoertuigen te identificeren die zijn verlopen.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Herhaal de stappen in de vorige sectie om de syntaxis van de streamingtaak van TollApp bij te werken.

2. Herhaal de stappen in de vorige sectie om de CosmosDB-uitvoergegevens van de streamingtaak te bekijken.

Voorbeelduitvoer:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Schaal de taak uit
Azure Stream Analytics is ontworpen om elastisch te schalen, zodat het grote hoeveelheden gegevens kan verwerken. De Azure Stream Analytics-query kan een **component PARTITIE BY** gebruiken om het systeem te vertellen dat deze stap wordt geschaald. **PartitionId** is een speciale kolom die het systeem toevoegt om de partitie-id van de invoer (gebeurtenishub) te matchen.

Als u de query wilt uitschalen naar partities, bewerkt u de querysyntaxis naar de volgende code:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Ga als u de streamingtaak opschalen naar meer streaming-eenheden:

1. **Stop** de huidige taak.

2. Werk de querysyntaxis bij op de **pagina< > query** en sla de wijzigingen op.

3. Selecteer **Schalen**onder de kop CONFIGUREREN voor de streamingtaak .

4. Schuif de schuifregelaar **Streaming-eenheden** van 1 naar 6. Streaming-eenheden definiëren de hoeveelheid rekenkracht die de taak kan ontvangen. Selecteer **Opslaan**.

5. **Start** de streamingtaak om de extra schaal aan te tonen. Azure Stream Analytics distribueert het werk over meer rekenbronnen en bereikt een betere doorvoer, waarbij het werk wordt verdeeld over resources met behulp van de kolom die is aangewezen in de component PARTITION BY.

## <a name="monitor-the-job"></a>De taak bewaken
Het **gebied MONITOR** bevat statistieken over de lopende taak. Een eerste configuratie is nodig om het opslagaccount in dezelfde regio te gebruiken (naamtol zoals de rest van dit document).

![Azure Stream Analytics-taakbewaking](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

U hebt ook toegang tot **activiteitslogboeken** vanuit het gebied **Instellingen voor taakdashboard.**

## <a name="clean-up-the-tollapp-resources"></a>De TollApp-bronnen opschonen
1. Stop de functie Stream Analytics in de Azure-portal.

2. Zoek de brongroep die acht bronnen bevat die gerelateerd zijn aan de sjabloon TollApp.

3. Selecteer **Resourcegroep verwijderen**. Typ de naam van de resourcegroep om verwijdering te bevestigen.

## <a name="conclusion"></a>Conclusie
Deze oplossing heeft u kennis laten maken met de Azure Stream Analytics-service. Het toonde aan hoe u ingangen en uitvoer configureren voor de streamanalytics-taak. Met behulp van het scenario Toll Data legde de oplossing veelvoorkomende soorten problemen uit die zich voordoen in de ruimte van bewegende gegevens en hoe ze kunnen worden opgelost met eenvoudige SQL-achtige query's in Azure Stream Analytics. De oplossing beschreef SQL-extensieconstructies voor het werken met tijdelijke gegevens. Het toonde aan hoe u gegevensstromen aansluiten, hoe u de gegevensstroom verrijken met statische referentiegegevens en hoe u een query schalen om een hogere doorvoer te bereiken.

Hoewel deze oplossing een goede introductie biedt, is deze op geen enkele manier compleet. U meer querypatronen vinden met behulp van de SAQL-taal bij [Query-voorbeelden voor algemene gebruikspatronen van Stream Analytics.](stream-analytics-stream-analytics-query-patterns.md)
