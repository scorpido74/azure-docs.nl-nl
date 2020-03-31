---
title: Api voor gegevensverzamelaar gebruiken om een gegevenspijplijn te maken
description: U de AZURE Monitor HTTP Data Collector API gebruiken om JSON-gegevens van POST toe te voegen aan de Werkruimte Log Analytics vanaf elke client die de REST API kan aanroepen. In dit artikel wordt beschreven hoe u gegevens die in bestanden zijn opgeslagen op een geautomatiseerde manier uploaden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055113"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Een gegevenspijplijn maken met de API voor gegevensverzamelaar

Met de [API azure monitor data collector](data-collector-api.md) u aangepaste logboekgegevens importeren in een Log Analytics-werkruimte in Azure Monitor. De enige vereisten zijn dat de gegevens worden opgemaakt en opgesplitst in segmenten van 30 MB of minder. Dit is een volledig flexibel mechanisme dat op vele manieren kan worden aangesloten: van gegevens die rechtstreeks vanuit uw toepassing worden verzonden tot eenmalige adhoc-uploads. In dit artikel worden enkele uitgangspunten voor een gemeenschappelijk scenario beschreven: de noodzaak om gegevens die zijn opgeslagen in bestanden regelmatig, geautomatiseerd te uploaden. Hoewel de hier gepresenteerde pijplijn niet de meest performante of anderszins geoptimaliseerd zal zijn, is het bedoeld om te dienen als uitgangspunt voor het bouwen van een eigen productiepijplijn.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Voorbeeldprobleem
Voor de rest van dit artikel onderzoeken we paginaweergavegegevens in Application Insights. In ons hypothetische scenario willen we geografische informatie die standaard door de Application Insights SDK wordt verzameld, correleren met aangepaste gegevens die de bevolking van elk land/regio in de wereld bevatten, met als doel te bepalen waar we de meeste marketingdollars zouden moeten uitgeven. 

Hiervoor gebruiken we een openbare gegevensbron zoals de [VN-wereldranglijst.](https://esa.un.org/unpd/wpp/) De gegevens hebben het volgende eenvoudige schema:

![Voorbeeld eenvoudig schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

In ons voorbeeld gaan we ervan uit dat we een nieuw bestand met de gegevens van het laatste jaar zullen uploaden zodra het beschikbaar is.

## <a name="general-design"></a>Algemeen ontwerp
We gebruiken een klassieke ETL-achtige logica om onze pijplijn te ontwerpen. De architectuur ziet er als volgt uit:

![Pijplijnarchitectuur voor gegevensverzameling](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

In dit artikel wordt niet ingaan op het maken van gegevens of [het uploaden naar een Azure Blob Storage-account.](../../storage/blobs/storage-upload-process-images.md) In plaats daarvan pakken we de stroom op zodra een nieuw bestand is geüpload naar de blob. Vanaf hier:

1. Een proces detecteert dat nieuwe gegevens zijn geüpload.  In ons voorbeeld wordt een [Azure Logic App](../../logic-apps/logic-apps-overview.md)gebruikt, die een trigger beschikbaar heeft om te detecteren dat nieuwe gegevens worden geüpload naar een blob.

2. Een processor leest deze nieuwe gegevens en converteert deze naar JSON, het formaat dat vereist is door Azure Monitor In dit voorbeeld gebruiken we een [Azure-functie](../../azure-functions/functions-overview.md) als een lichtgewicht, kostenefficiënte manier om onze verwerkingscode uit te voeren. De functie wordt afgetrapt door dezelfde Logic App die we gebruikten om de nieuwe gegevens te detecteren.

3. Ten slotte wordt het JSON-object, zodra het JSON-object beschikbaar is, naar Azure Monitor verzonden. Dezelfde Logic App stuurt de gegevens naar Azure Monitor met behulp van de ingebouwde Activiteit Logboekanalysegegevensverzamelaar.

Hoewel de gedetailleerde installatie van de blob-opslag, logic-app of Azure-functie niet in dit artikel wordt beschreven, zijn gedetailleerde instructies beschikbaar op de pagina's van de specifieke producten.

Om deze pijplijn te controleren, gebruiken we Application Insights om [onze Azure-functiegegevens hier](../../azure-functions/functions-monitoring.md)te controleren en Azure Monitor om onze [Logic App-gegevens hier](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)te controleren. 

## <a name="setting-up-the-pipeline"></a>De pijplijn instellen
Als u de pijplijn wilt instellen, moet u eerst ervoor zorgen dat de blobcontainer is gemaakt en geconfigureerd. Zorg er ook voor dat de werkruimte Log Analytics waarnaar u de gegevens wilt verzenden, wordt gemaakt.

## <a name="ingesting-json-data"></a>Json-gegevens opnemen
Het opnemen van JSON-gegevens is triviaal met Logic Apps en omdat er geen transformatie hoeft plaats te vinden, kunnen we de hele pijplijn in één Logic-app inhouden. Zodra zowel de blobcontainer als de werkruimte Log Analytics zijn geconfigureerd, maakt u een nieuwe Logic App en configureert u deze als volgt:

![Voorbeeld van de werkstroom van logische apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Sla uw Logic App op en test deze.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML, CSV of andere indelingen van gegevens innemen
Logic Apps beschikt vandaag de dag niet over ingebouwde mogelijkheden om XML, CSV of andere typen eenvoudig om te zetten in JSON-indeling. Daarom moeten we een ander middel gebruiken om deze transformatie te voltooien. Voor dit artikel gebruiken we de serverloze rekenmogelijkheden van Azure Functions als een zeer lichtgewicht en kostenvriendelijke manier om dit te doen. 

In dit voorbeeld ontschepen we een CSV-bestand, maar elk ander bestandstype kan op dezelfde manier worden verwerkt. Wijzig eenvoudig het deserialiserende gedeelte van de Azure-functie om de juiste logica voor uw specifieke gegevenstype weer te geven.

1.  Maak een nieuwe Azure-functie met behulp van de functie runtime v1 en op basis van verbruik wanneer daarom wordt gevraagd.  Selecteer de **HTTP-triggersjabloon** die is gericht op C# als uitgangspunt dat uw bindingen configureert zoals we dat nodig hebben. 
2.  Maak op het tabblad **Bestanden weergeven** in het rechterdeelvenster een nieuw bestand genaamd **project.json** en plak de volgende code uit NuGet-pakketten die we gebruiken:

    ![Voorbeeldproject Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Schakel over naar **run.csx** vanuit het rechterdeelvenster en vervang de standaardcode door het volgende. 

    >[!NOTE]
    >Voor uw project moet u het recordmodel (de klasse 'Bevolkingsrecord') vervangen door uw eigen gegevensschema.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Sla uw functie op.
5. Test de functie om te zien of de code correct werkt. Ga naar het tabblad **Testen** in het rechterdeelvenster en configureer de test als volgt. Plaats een koppeling naar een blob met voorbeeldgegevens in het tekstvak **Hoofdtekst aanvragen.** Nadat u op **Uitvoeren**hebt geklikt, ziet u de JSON-uitvoer in het vak **Uitvoer:**

    ![Testcode functie-apps](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nu moeten we teruggaan en de Logic App aanpassen die we eerder zijn begonnen met het bouwen om de gegevens op te nemen die zijn ingenomen en geconverteerd naar JSON-indeling.  Configureer met View Designer als volgt en sla vervolgens uw Logic App op:

![Voorbeeld van het volledige voorbeeld van de werkstroom van Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>De pijplijn testen
Nu u een nieuw bestand uploaden naar de blob die eerder is geconfigureerd en het laten controleren door uw Logic App. Binnenkort ziet u een nieuw exemplaar van de logic-app start, roep naar uw Azure-functie en vervolgens met succes de gegevens naar Azure Monitor. 

>[!NOTE]
>Het kan tot 30 minuten duren voordat de gegevens worden weergegeven in Azure Monitor wanneer u voor het eerst een nieuw gegevenstype verzendt.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correleren met andere gegevens in Log Analytics en Application Insights
Als u ons doel wilt voltooien om de paginaweergavegegevens van Application Insights te correleren met de populatiegegevens die we van onze aangepaste gegevensbron hebben ingenomen, voert u de volgende query uit vanuit uw venster Application Insights Analytics of de werkruimte Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

De uitvoer moet de twee gegevensbronnen weergeven die nu zijn samengevoegd.  

![Ontjoining van onsamengevoegde gegevens in een voorbeeld van zoekresultaten](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Voorgestelde verbeteringen voor een productiepijplijn
Dit artikel presenteerde een werkend prototype, de logica waarachter kan worden toegepast op een echte productie-kwaliteit oplossing. Voor een dergelijke oplossing van productiekwaliteit worden de volgende verbeteringen aanbevolen:

* Voeg foutafhandeling toe en probeer logica opnieuw in uw logische app en functie.
* Voeg logica toe om ervoor te zorgen dat de API-aanroeplimiet voor 30 MB/single Log Analytics-inname niet wordt overschreden. Splits de gegevens indien nodig op in kleinere segmenten.
* Stel een opschoonbeleid in voor de opslag van uw blob. Zodra u met succes naar de werkruimte Log Analytics is verzonden, tenzij u de ruwe gegevens beschikbaar wilt houden voor archiveringsdoeleinden, is er geen reden om deze verder op te slaan. 
* Controleer of de bewaking is ingeschakeld in de volledige pijplijn en voeg waar nodig tracepoints en waarschuwingen toe.
* Maak gebruik van bronbeheer om de code voor uw functie en Logic App te beheren.
* Zorg ervoor dat een goed change management beleid wordt gevolgd, zodat als het schema verandert, de functie en Logic Apps dienovereenkomstig worden gewijzigd.
* Als u meerdere verschillende gegevenstypen uploadt, scheidt u deze in afzonderlijke mappen in uw blobcontainer en maakt u logica om de logica uit te waaieren op basis van het gegevenstype. 


## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Api voor gegevensverzamelaar](data-collector-api.md) om gegevens te schrijven naar de werkruimte Log Analytics van elke REST API-client.
