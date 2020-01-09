---
title: Een IoT-oplossing bouwen met behulp van Azure Stream Analytics
description: Aan de slag-zelfstudie voor de Stream Analytics IoT-oplossing van een stencil-scenario
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426442"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Een IoT-oplossing bouwen met behulp van Stream Analytics

## <a name="introduction"></a>Inleiding
In deze oplossing leert u hoe u realtime inzicht krijgen in uw gegevens met Azure Stream Analytics. Ontwikkelaars combineren eenvoudig gegevensstromen, zoals de klik-streams, logboeken en gebeurtenissen apparaat worden gegenereerd, met historische records of referentiegegevens voor het afleiden van zakelijke inzichten worden verkregen. Als een volledig beheerde, realtime stream berekening service die wordt gehost in Microsoft Azure, biedt Azure Stream Analytics ingebouwde tolerantie, lage latentie en schaalbaarheid voor u en binnen enkele minuten.

Na het voltooien van deze oplossing, kunt u zich:

* Maak uzelf bekend met de Azure Stream Analytics-portal.
* Configureren en implementeren van een streaming-taak.
* Bewoordingen aan problemen en oplossen van problemen met behulp van de Stream Analytics-querytaal.
* Streaming van oplossingen voor uw klanten met behulp van Stream Analytics met vertrouwen te ontwikkelen.
* De bewaking en logboekregistratie ervaring om problemen te gebruiken.

## <a name="prerequisites"></a>Vereisten
U moet de volgende vereisten voor het voltooien van deze oplossing:
* Een [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenario-Inleiding: "Hallo, gratis!"
Een station nummer is een algemene verschijnsel. U ondervindt ze op tal van autowegen, bruggen en tunnels over de hele wereld. Elk station nummer heeft meerdere nummer stands. Bij handmatige stands stoppen u om te betalen van de gratis naar een daarmee gepaard gaande. Bij geautomatiseerde stands scant een sensor boven op elke stand een RFID-kaart die wordt aangebracht op de voorruit van het voertuig als u het nummer tolloket doorgeven. Het is gemakkelijk om te visualiseren van het verstrijken van de door middel van deze stations nummer als een gebeurtenisstroom waarover interessante bewerkingen kunnen worden uitgevoerd.

![Afbeelding van auto's op het telefoon nummer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Binnenkomende gegevens
Deze oplossing werkt met twee gegevensstromen. Sensoren die in de in- en afsluiten van de stations nummer geïnstalleerd produceren de eerste stream. De tweede stream is een statische lookup-gegevensset waarvoor vehicle-registratiegegevens.

### <a name="entry-data-stream"></a>Post-gegevensstroom
De gegevensstroom vermelding bevat informatie over auto's als ze gratis stations invoeren. De gegevens afsluiten gebeurtenissen zijn live gestreamd naar een Event Hub-wachtrij vanuit een Web-App die is opgenomen in de voorbeeld-app.

| TollID | EntryTime | LicensePlate | Staat | Maken | Model | VehicleType | VehicleWeight | Nummer | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4 x 4 |1 |0 |6 |321987654 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| TollID |De gratis stand-ID die een unieke identificatie van een tolloket nummer |
| EntryTime |De datum en tijd van de vermelding van het voertuig naar de stand nummer in UTC |
| LicensePlate |De sofi-nummer van het voertuig |
| Staat |Een status in de Verenigde Staten |
| Maken |De fabrikant van de auto |
| Model |Het modelnummer van het auto |
| VehicleType |1 voor passagier voertuigen of 2 voor commerciële voertuigen |
| WeightType |Vehicle gewicht in ton; 0 voor passagier voertuigen |
| Nummer |De waarde van het nummer in USD |
| Tag |De e-code op de auto waarmee betaling. leeg wanneer de betaling handmatig is uitgevoerd |

### <a name="exit-data-stream"></a>Sluit de gegevensstroom
De gegevensstroom afsluiten bevat informatie over auto's zodat het station dat nummer. De gegevens afsluiten gebeurtenissen zijn live gestreamd naar een Event Hub-wachtrij vanuit een Web-App die is opgenomen in de voorbeeld-app.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| TollID |De gratis stand-ID die een unieke identificatie van een tolloket nummer |
| ExitTime |De datum en tijd van af te sluiten van het voertuig in stand nummer in UTC |
| LicensePlate |De sofi-nummer van het voertuig |

### <a name="commercial-vehicle-registration-data"></a>Registratiegegevens commerciële voertuigen
De oplossing maakt gebruik van een momentopname van een commerciële voertuigen registratie-database. Deze gegevens worden opgeslagen als JSON-bestand in Azure blob-opslag, opgenomen in het voorbeeld.

| LicensePlate | Registratie-id | Verlopen |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| LicensePlate |De sofi-nummer van het voertuig |
| Registratie-id |Registratie-ID van het voertuig |
| Verlopen |De registratiestatus van het voertuig: 0 of vehicle registratie actief is, 1 als de registratie is verlopen |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Stel de omgeving voor Azure Stream Analytics
Voor deze oplossing, hebt u een Microsoft Azure-abonnement nodig. Als u een Azure-account hebt, kunt u [aanvragen van een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).

Zorg ervoor dat de stappen in de sectie 'Opschonen van uw Azure-account' aan het einde van dit artikel zodat u kunt het beste gebruik van uw Azure-tegoed.

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren
Er zijn verschillende bronnen die eenvoudig kunnen worden geïmplementeerd in een resourcegroep, samen met enkele klikken. De definitie van de oplossing wordt in de GitHub-opslag plaats gehost op [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>De sjabloon TollApp in Azure portal implementeren
1. Voor het implementeren van de omgeving TollApp naar Azure, gebruikt u deze koppeling naar [TollApp Azure-sjabloon implementeren](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Meld u aan de Azure-portal als u hierom wordt gevraagd.

3. Kies het abonnement waarin de verschillende bronnen worden in rekening gebracht.

4. Geef een nieuwe resourcegroep met een unieke naam, bijvoorbeeld `MyTollBooth`.

5. Selecteer een Azure-locatie.

6. Geef een **Interval** als een aantal seconden. Deze waarde wordt gebruikt in de voorbeeld-web-app, hoe vaak om gegevens te verzenden naar Event Hub.

7. **Controleer** om de voorwaarden en bepalingen te accepteren.

8. Selecteer **vastmaken aan dashboard** zodat u de resources die later kan vinden.

9. Selecteer **aankoop** om de voorbeeldsjabloon te implementeren.

10. Na een paar seconden een melding wordt weergegeven om te bevestigen de **implementatie is voltooid**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Bekijk de Azure Stream Analytics TollApp-resources

1. Aanmelden bij Azure Portal

2. Zoek de resourcegroep die u met de naam in de vorige sectie.

3. Controleer of dat de volgende bronnen worden weergegeven in de resourcegroep:
   - Een Cosmos DB-Account
   - Een Azure Stream Analytics-taak
   - Een Azure Storage-Account
   - Een Azure Event Hub
   - Twee Web-Apps

## <a name="examine-the-sample-tollapp-job"></a>Bekijk het voorbeeld TollApp taak
1. Starten van de resourcegroep in de vorige sectie, selecteert u de Stream Analytics streaming-taak starten met de naam van de **tollapp** (de naam bevat willekeurige tekens uniek).

2. Op de **overzicht** pagina van de taak, u ziet dat de **Query** vak om de query-syntaxis weer te geven.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Stel de die u wilt tellen het aantal voertuigen die invoeren van een tolloket nummer om u te parafraseren de bedoeling van de query. Omdat een snelweg nummer tolloket een continue stroom van voertuigen heeft, zijn de ingang gebeurtenissen zijn vergelijkbaar met een stroom waarmee nooit wordt gestopt. Als u wilt de stroom kwantificeren, die u hebt voor het definiëren van een 'periode' voor het meten van. Laten we de vraag verder verfijnen op "hoeveel voertuigen Voer een nummer stand om de drie minuten?" Dit wordt vaak aangeduid als het aantal tumblingvenstertrigger.

   Zoals u ziet, Azure Stream Analytics maakt gebruik van een querytaal die is zoals SQL en biedt een aantal extensies om op te geven met betrekking tot tijd aspecten van de query.  Lees voor meer informatie over [Time Management](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) en [Windowing](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) constructies die worden gebruikt in de query.

3. Controleer de invoer van de taak TollApp-voorbeeld. Alleen de EntryStream invoer wordt gebruikt in de huidige query.
   - **EntryStream** invoer is een Event Hub-verbinding die gegevens die telkens wanneer een auto voert een stencil op de weg vertegenwoordigt in de wachtrij geplaatst. Een web-app die deel uitmaakt van het voorbeeld is het maken van de gebeurtenissen en die gegevens in deze Event Hub in de wachtrij is geplaatst. Houd er rekening mee dat deze invoer query wordt uitgevoerd in de component FROM van de streaming-query.
   - **ExitStream** invoer is een Event Hub-verbinding die gegevens die telkens wanneer een auto afgesloten een stencil op de weg vertegenwoordigt in de wachtrij geplaatst. Dit streaming invoer wordt gebruikt in latere variaties van de query-syntaxis.
   - **Registratie** invoer is een Azure Blob storage-verbinding, die verwijst naar een bestand statische registration.json, die wordt gebruikt voor zoekopdrachten, indien nodig. Deze referentie-invoer voor gegevens wordt gebruikt in latere variaties van de query-syntaxis.

4. Bekijk de uitvoer van de taak TollApp-voorbeeld.
   - **Cosmos DB** -uitvoer is een Cosmos-database container die de uitvoer-Sink-gebeurtenissen ontvangt. Houd er rekening mee dat deze uitvoer in een component van de streaming-query wordt gebruikt in.

## <a name="start-the-tollapp-streaming-job"></a>Start de TollApp streaming-taak
Volg deze stappen voor het starten van de streaming-taak:

1. Op de **overzicht** pagina van de taak, selecteer **Start**.

2. Op de **starttaak** venster **nu**.

3. Na enkele ogenblikken, zodra de taak wordt uitgevoerd, op de **overzicht** pagina van de streaming-taak, weergave de **bewaking** graph. De grafiek moet worden verschillende duizend invoergebeurtenissen en tientallen uitvoergebeurtenissen weergegeven.

## <a name="review-the-cosmosdb-output-data"></a>Controleer de CosmosDB-uitvoer-gegevens
1. Zoek de resourcegroep die de TollApp resources bevat.

2. Selecteer het Azure Cosmos DB account met het naam patroon **tollapp\<wille keurige\>-Cosmos**.

3. Selecteer de **Data Explorer** kop om de pagina Data Explorer te openen.

4. Vouw de **tollAppDatabase** > **tollAppCollection** > **documenten**.

5. In de lijst met id's, worden diverse docs worden weergegeven wanneer de uitvoer beschikbaar is.

6. Selecteer elke id om te controleren van het JSON-document. Let op elke tollid, windowend tijd en de telling van auto's in dit venster.

7. Na een extra drie minuten een andere set met vier documenten die beschikbaar is, wordt één document per tollid.


## <a name="report-total-time-for-each-car"></a>Totale tijd voor elke auto rapport
De gemiddelde tijd die is vereist voor een auto om door te geven via de gratis helpt bij het beoordelen van de efficiëntie van het proces en de gebruikerservaring.

Als u de totale tijd zoekt, neem deel aan de EntryTime streamen met de stroom ExitTime. Neem deel aan de twee invoerstromen op gelijke overeenkomende TollId en LicencePlate kolommen. De **JOIN** operator moet u tijdelijke eenheidsprofiel die het acceptabele tijdsverschil tussen de gekoppelde gebeurtenissen beschrijft opgeven. Gebruik de **DATEDIFF** functie waarmee u kunt opgeven dat gebeurtenissen niet meer dan 15 minuten van elkaar worden verbonden moeten zijn. Ook van toepassing de **DATEDIFF** functie om af te sluiten en tijden van de vermelding voor het berekenen van de werkelijke hoeveelheid tijd die een auto doorbrengt in de gratis-station. Let op het verschil van het gebruik van **DATEDIFF** wanneer deze wordt gebruikt een **Selecteer** instructie in plaats van een **JOIN** voorwaarde.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Bijwerken van de TollApp streaming job query-syntaxis:

1. Op de **overzicht** pagina van de taak, selecteer **stoppen**.

2. Wacht een paar seconden voor de melding dat de taak is gestopt.

3. Selecteer onder de kop TAAKTOPOLOGIE **combinatie Query**

4. Plak de aangepaste streaming SQL-query.

5. Selecteer **opslaan** de query op te slaan. Controleer of **Ja** de wijzigingen op te slaan.

6. Op de **overzicht** pagina van de taak, selecteer **Start**.

7. Op de **starttaak** venster **nu**.

### <a name="review-the-total-time-in-the-output"></a>De totale tijd in de uitvoer controleren
Herhaal de stappen in de vorige sectie om te controleren van de CosmosDB-uitvoergegevens van de streaming-taak. Raadpleegt u de meest recente JSON-documenten.

Dit document ziet u bijvoorbeeld een auto voorbeeld met een bepaalde licentie-element, de tijd entrytime en afsluiten en het DATEDIFF durationinminutes berekende veld weergegeven van de duur van de stand nummer als twee minuten:
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

## <a name="report-vehicles-with-expired-registration"></a>Rapport voertuigen met de registratie van verlopen
Azure Stream Analytics kunt statische momentopnamen van de referentiegegevens samen te voegen met tijdelijke gegevensstromen. Om te demonstreren van deze mogelijkheid, het volgende Voorbeeldvraag te gebruiken. De invoer van de registratie is een vaste blob json-bestand met een lijst met het verlopen van licentie-codes. De referentiegegevens wordt lid van het licentie-element, vergeleken met elke vehicle doorgegeven via het nummer.

Als een commerciële voertuigen is geregistreerd bij het bedrijf nummer, kan deze de stand nummer passeren zonder te zijn gestopt voor inspectie. Gebruik de opzoektabel voor registratie voor het identificeren van alle commerciële voertuigen die registraties zijn verlopen.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Herhaal de stappen in de vorige sectie om bij te werken van de TollApp streaming job query-syntaxis.

2. Herhaal de stappen in de vorige sectie om te controleren van de CosmosDB-uitvoergegevens van de streaming-taak.

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

## <a name="scale-out-the-job"></a>De taak uitschalen
Azure Stream Analytics is ontworpen voor flexibele manier schalen zodat deze kan grote hoeveelheden gegevens worden verwerkt. De Azure Stream Analytics-query kan een **partitie by** -component gebruiken om het systeem te laten opschalen dat deze stap uitschaalt. **PartitionId** is een speciale kolom die wordt toegevoegd aan het systeem en die overeenkomt met de partitie-id van de invoer (Event hub).

Als u wilt schalen de query met partities, bewerken de query-syntaxis in de volgende code:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Om omhoog te schalen naar meer streamingeenheden de streaming-taak:

1. **Stop** de huidige taak.

2. Bijwerken van de query-syntaxis in de **combinatie Query** pagina en de wijzigingen opslaan.

3. Selecteer onder de kop configureren op de streaming-taak **schaal**.

4. Schuif de **Streaming-eenheden** schuifregelaar van 1 tot en met 6. Streaming-eenheden definiëren de hoeveelheid rekenkracht die de taak kan ontvangen. Selecteer **Opslaan**.

5. **Start** de streaming-taak ter illustratie van de schaal te vergroten. Azure Stream Analytics werk verdeeld over meer rekenresources en betere doorvoer, maar liefst partitioneren van het werk voor resources met behulp van de kolom die is aangeduid in de component PARTITION BY.

## <a name="monitor-the-job"></a>De taak bewaken
De **MONITOR** gebied statistieken over de actieve taak bevat. Configuratie van de eerste keer is nodig voor het gebruik van het opslagaccount in dezelfde regio (naam nummer als de rest van dit document).

![Azure Stream Analytics-taak controleren](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

U hebt toegang tot **activiteitenlogboeken** vanuit het dashboard van de taak **instellingen** ook gebied.

## <a name="clean-up-the-tollapp-resources"></a>De TollApp resources opschonen
1. Stop de Stream Analytics-taak in Azure portal.

2. Zoek de resourcegroep die acht bronnen die betrekking hebben op de sjabloon TollApp bevat.

3. Selecteer **Resourcegroep verwijderen**. Typ de naam van de resourcegroep verwijderen te bevestigen.

## <a name="conclusion"></a>Conclusie
Deze oplossing hebt u kennisgemaakt met de Azure Stream Analytics-service. Het configureren van de invoer en uitvoer voor de Stream Analytics-taak aangetoond. Met behulp van het scenario niet gratis gegevens, beschreven de oplossing voorkomende problemen die optreden in de ruimte van gegevens in beweging en hoe ze kunnen worden opgelost met eenvoudige SQL-achtige query's in Azure Stream Analytics. De oplossing beschreven SQL-constructs-extensie voor het werken met tijdelijke gegevens. Deze hebt u geleerd hoe u gegevensstromen kunt samenvoegen, hoe u de gegevensstroom met statische referentiegegevens verrijken en hoe u uit een query groeperen voor hogere doorvoer te schalen.

Hoewel deze oplossing een goede inleiding biedt, is het niet voltooid door middel van. U vindt meer querypatronen met behulp van de taal SAQL op [voorbeelden voor algemene patronen voor het gebruik van Stream Analytics Query](stream-analytics-stream-analytics-query-patterns.md).
