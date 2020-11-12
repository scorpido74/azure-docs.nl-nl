---
title: De tweelinggrafiek met relaties beheren
titleSuffix: Azure Digital Twins
description: Zie een grafiek met digitale apparaatdubbels beheren door deze te verbinden met relaties.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73aa6f8f6ee36aeeb41fbc54afe217ac776a4ebc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533875"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Een grafiek van digitale apparaatdubbels beheren met behulp van relaties

De kern van Azure Digital Apparaatdubbels is de [dubbele grafiek](concepts-twins-graph.md) die uw hele omgeving weergeeft. Het dubbele diagram is gemaakt van individuele digitale apparaatdubbels die zijn verbonden via **relaties**. 

Zodra u een werkend [Azure Digital apparaatdubbels-exemplaar](how-to-set-up-instance-portal.md) hebt en [verificatie](how-to-authenticate-client.md) code hebt ingesteld in uw client-app, kunt u de [**DigitalTwins-api's**](/rest/api/digital-twins/dataplane/twins) gebruiken voor het maken, wijzigen en verwijderen van digitale Apparaatdubbels en hun relaties in een Azure Digital apparaatdubbels-instantie. U kunt ook de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md)gebruiken.

Dit artikel richt zich op het beheren van relaties en de hele grafiek; Zie [*How-to: Manage Digital apparaatdubbels*](how-to-manage-twin.md)(Engelstalig) als u wilt werken met afzonderlijke digitale apparaatdubbels.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Manieren om grafieken te beheren

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

U kunt ook wijzigingen aanbrengen in uw grafiek met behulp van het ADT-voor beeld (Azure Digital Apparaatdubbels) Explorer. Hiermee kunt u uw apparaatdubbels en Graph visualiseren en de SDK achter de schermen gebruiken. In de volgende sectie wordt dit voor beeld uitvoerig beschreven.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Relaties maken

Relaties beschrijven hoe verschillende digitale apparaatdubbels met elkaar zijn verbonden, die de basis vormen van het dubbele diagram.

Relaties worden gemaakt met behulp van de `CreateOrReplaceRelationshipAsync()` aanroep. 

Als u een relatie wilt maken, moet u het volgende opgeven:
* De bron-dubbele ID ( `srcId` in het onderstaande code voorbeeld): de id van de dubbele locatie van de relatie.
* De dubbele ID van het doel ( `targetId` in het onderstaande code voorbeeld): de id van de dubbele locatie van de relatie.
* Een relatie naam ( `relName` in het onderstaande code voorbeeld): het algemene type relatie, zoals _contains_.
* Een relatie-ID ( `relId` in het onderstaande code voorbeeld): de specifieke naam voor deze relatie, wat lijkt op _Relationship1_.

De relatie-ID moet uniek zijn binnen de opgegeven bron. Het hoeft niet wereld wijd uniek te zijn.
Voor de dubbele *Foo* moet elke specifieke relatie-id bijvoorbeeld uniek zijn. Een andere dubbele *balk* kan echter een uitgaande relatie hebben die overeenkomt met dezelfde id van een *Foo* -relatie.

In het volgende code voorbeeld ziet u hoe u een relatie maakt in uw Azure Digital Apparaatdubbels-exemplaar.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
In uw hoofd methode kunt u de functie nu aanroepen `CreateRelationship()` om een _contains_ -relatie te maken, zoals: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Als u meerdere relaties wilt maken, kunt u aanroepen naar dezelfde methode herhalen, waarbij verschillende relatie typen in het argument worden door gegeven. 

`BasicRelationship`Zie [*How-to: use the Azure Digital apparaatdubbels Api's and sdk's*](how-to-use-apis-sdks.md#serialization-helpers)(Engelstalig) voor meer informatie over de helper-klasse.

### <a name="create-multiple-relationships-between-twins"></a>Meerdere relaties maken tussen apparaatdubbels

Relaties kunnen worden geclassificeerd als een van de volgende: 

* Uitgaande relaties: relaties die deel uitmaken van dit dubbele punt om deze te verbinden met andere apparaatdubbels. De `GetRelationshipsAsync()` methode wordt gebruikt voor het ophalen van uitgaande relaties van een twee.
* Binnenkomende relaties: relaties die deel uitmaken van andere apparaatdubbels die naar dit dubbele punt verwijzen om een ' binnenkomende ' koppeling te maken. De `GetIncomingRelationshipsAsync()` methode wordt gebruikt voor het ophalen van binnenkomende relaties van een twee.

Er is geen beperking voor het aantal relaties dat u tussen twee apparaatdubbels kunt hebben: u kunt zo veel relaties tussen apparaatdubbels hebben als u wilt. 

Dit betekent dat u verschillende typen relaties tussen twee apparaatdubbels tegelijk kunt uitdrukken. *Dubbele a* kan bijvoorbeeld zowel een *opgeslagen* relatie als een *vervaardigde* relatie met *dubbele B* hebben.

U kunt zelfs meerdere exemplaren van hetzelfde type relatie maken tussen dezelfde twee apparaatdubbels, indien gewenst. In dit voor beeld kunnen *dubbele a* twee verschillende *opgeslagen* relaties hebben met *dubbele B* , zolang de relaties verschillende relatie-id's hebben.

## <a name="list-relationships"></a>Lijst met relaties

Als u toegang wilt krijgen tot de lijst met **uitgaande** relaties voor een bepaalde dubbele in de grafiek, kunt u de `GetRelationships()` methode als volgt gebruiken:

```csharp
await client.GetRelationships()
```

Dit retourneert een `Azure.Pageable<T>` of `Azure.AsyncPageable<T>` , afhankelijk van of u de synchrone of asynchrone versie van de aanroep gebruikt.

Hier volgt een voor beeld van het ophalen van een lijst met relaties:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
U kunt deze methode nu aanroepen om de uitgaande relaties van het apparaatdubbels als volgt te bekijken:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
U kunt de opgehaalde relaties gebruiken om naar andere apparaatdubbels in uw grafiek te navigeren. U doet dit door het veld te lezen `target` in de relatie die wordt geretourneerd en dit te gebruiken als de id voor uw volgende oproep naar `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Inkomende relaties zoeken naar een digitaal, twee

Azure Digital Apparaatdubbels heeft ook een API voor het vinden van alle _ *binnenkomende* *-relaties naar een bepaald dubbele. Dit is vaak handig voor omgekeerde navigatie of bij het verwijderen van een dubbele.

Het vorige code voorbeeld is gericht op het vinden van uitgaande relaties van een dubbele. Het volgende voor beeld is op dezelfde manier gestructureerd, maar detecteert *inkomende* relaties met de dubbele plaats.

Houd er rekening mee dat de `IncomingRelationship` aanroepen de volledige hoofd tekst van de relatie niet retour neren.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

U kunt deze methode nu aanroepen om de binnenkomende relaties van het apparaatdubbels als volgt te bekijken:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Alle dubbele eigenschappen en relaties weer geven

Met behulp van de bovenstaande methoden voor het weer geven van uitgaande en inkomende relaties naar een dubbele, kunt u een methode maken waarmee de volledige dubbele gegevens worden afgedrukt, met inbegrip van de eigenschappen van de twee en beide typen relaties. Hier volgt een voor beeld van de methode, `FetchAndPrintTwinAsync()` die wordt weer gegeven en hoe u dit doet.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

U kunt deze functie nu als volgt aanroepen in de methode Main: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Relaties verwijderen

Met de eerste para meter geeft u de bron op, twee (de dubbele locatie van de relatie). De andere para meter is de relatie-ID. U hebt zowel de dubbele ID als de relatie-ID nodig, omdat relatie-Id's alleen uniek zijn binnen het bereik van een dubbele.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

U kunt deze methode nu aanroepen om een relatie als volgt te verwijderen:

```csharp
await DeleteRelationship(client, srcId, relId);
```

## <a name="runnable-twin-graph-sample"></a>Uitvoer bare dubbele grafiek voorbeeld

Het volgende uitvoer bare code fragment maakt gebruik van de relatie bewerkingen uit dit artikel om een dubbele grafiek te maken van digitale apparaatdubbels en relaties.

### <a name="set-up-the-runnable-sample"></a>Het uitvoer bare-voor beeld instellen

Het fragment maakt gebruik van de [*Room.jsop*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) en [*Floor.jsop*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) model definities uit de [*zelf studie: Verken Azure Digital apparaatdubbels met een voor beeld-client-app*](tutorial-command-line-app.md). U kunt deze koppelingen gebruiken om rechtstreeks naar de bestanden te gaan of ze als onderdeel van het volledige end-to-end- [voorbeeld project te](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)downloaden. 

Ga als volgt te werk voordat u het voor beeld uitvoert:
1. Down load de model bestanden, plaats deze in uw project en vervang de `<path-to>` tijdelijke aanduidingen in de onderstaande code om uw programma te laten weten waar ze zich bevinden.
2. Vervang de tijdelijke aanduiding door de `<your-instance-hostname>` hostnaam van uw Azure Digital apparaatdubbels-exemplaar.
3. Voeg deze pakketten toe aan uw project:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

U moet ook lokale referenties instellen als u het voor beeld rechtstreeks wilt uitvoeren. In de volgende sectie wordt dit uitgelegd.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Nadat u de bovenstaande stappen hebt voltooid, kunt u de volgende voorbeeld code rechtstreeks uitvoeren.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Hier volgt de console-uitvoer van het bovenstaande programma: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Console-uitvoer met de dubbele Details, binnenkomende en uitgaande relaties van de apparaatdubbels." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Het dubbele diagram is een concept van het maken van relaties tussen apparaatdubbels. Als u de visuele weer gave van de dubbele grafiek wilt bekijken, raadpleegt u de sectie [_Visualization *](how-to-manage-graph.md#visualization) van dit artikel. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Een dubbele grafiek maken vanuit een CSV-bestand

In praktische use cases worden vaak dubbele hiërarchieën gemaakt op basis van gegevens die zijn opgeslagen in een andere data base, of mogelijk in een spread sheet of CSV-bestand. In deze sectie ziet u hoe u gegevens uit een CSV-bestand kunt lezen en hoe u er een dubbele grafiek van kunt maken.

Bekijk de volgende gegevens tabel, met een beschrijving van een set digitale apparaatdubbels en relaties.

|  Model-id    | Dubbele ID (moet uniek zijn) | Naam van relatie  | Doel-dubbele ID  | Dubbele init-gegevens |
| --- | --- | --- | --- | --- |
| dtmi: voor beeld: Floor; 1    | Floor1 | bevat | Room1 | |
| dtmi: voor beeld: Floor; 1    | Floor0 | bevat | Room0 | |
| dtmi: voor beeld: room; 1    | Room1 | | | {"Tempe ratuur": 80} |
| dtmi: voor beeld: room; 1    | Room0 | | | {"Tempe ratuur": 70} |

Een manier om deze gegevens op te halen in azure Digital Apparaatdubbels is door de tabel te converteren naar een CSV-bestand en code te schrijven om het bestand te interpreteren in opdrachten voor het maken van apparaatdubbels en relaties. In het volgende code voorbeeld ziet u hoe u de gegevens uit het CSV-bestand leest en een dubbele grafiek maakt in azure Digital Apparaatdubbels.

In de onderstaande code wordt het CSV-bestand *data.csv* genoemd en is er een tijdelijke aanduiding voor de **hostnaam** van uw Azure Digital apparaatdubbels-exemplaar. In het voor beeld wordt ook gebruikgemaakt van verschillende pakketten die u aan uw project kunt toevoegen om u te helpen bij dit proces.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
    }
}

```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitvoeren van query's op een Azure Digital Apparaatdubbels-grafiek:
* [*Concepten: query taal*](concepts-query-language.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)