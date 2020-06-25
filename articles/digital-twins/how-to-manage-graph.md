---
title: Het dubbele diagram met relaties beheren
titleSuffix: Azure Digital Twins
description: Zie een grafiek met digitale apparaatdubbels beheren door deze te verbinden met relaties.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 13ceed847b3946b47fb3389145843ad085391dc5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362795"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Een grafiek van digitale apparaatdubbels beheren met behulp van relaties

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

De kern van Azure Digital Apparaatdubbels is de [dubbele grafiek](concepts-twins-graph.md) die uw hele omgeving weergeeft. Het dubbele diagram bestaat uit afzonderlijke digitale apparaatdubbels die zijn verbonden via **relaties**.

Zodra u een werkend [Azure Digital apparaatdubbels-exemplaar](how-to-set-up-instance.md) hebt en [verificatie](how-to-authenticate-client.md) hebt ingesteld voor uw client-app, kunt u de [**DigitalTwins-api's**](how-to-use-apis-sdks.md) gebruiken voor het maken, wijzigen en verwijderen van digitale Apparaatdubbels en hun relaties in een Azure Digital apparaatdubbels-instantie. U kunt ook de [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md)gebruiken.

Dit artikel richt zich op het beheren van relaties en de hele grafiek; Zie [How-to: Manage Digital apparaatdubbels](how-to-manage-twin.md)(Engelstalig) als u wilt werken met afzonderlijke digitale apparaatdubbels.

## <a name="create-relationships"></a>Relaties maken

Relaties beschrijven hoe verschillende digitale apparaatdubbels met elkaar zijn verbonden, die de basis vormen van het dubbele diagram.

Relaties worden gemaakt met behulp van de `CreateRelationship` aanroep. 

Als u een relatie wilt maken, moet u het volgende opgeven:
* De bron-dubbele ID (de dubbele locatie van de relatie)
* De doel-dubbele ID (de dubbele locatie van de relatie)
* Een relatie naam
* Een relatie-ID

De relatie-ID moet uniek zijn binnen de opgegeven bron. Het hoeft niet wereld wijd uniek te zijn.
Voor de dubbele *Foo*moet elke specifieke relatie-id bijvoorbeeld uniek zijn. Een andere dubbele *balk* kan echter een uitgaande relatie hebben die overeenkomt met dezelfde id van een *Foo* -relatie. 

In het volgende code voorbeeld ziet u hoe u een relatie kunt toevoegen aan uw Azure Digital Apparaatdubbels-exemplaar.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

`BasicRelationship`Zie [How-to: use the Azure Digital apparaatdubbels Api's and sdk's](how-to-use-apis-sdks.md)(Engelstalig) voor meer informatie over de helper-klasse.

## <a name="list-relationships"></a>Lijst met relaties

Voor toegang tot de lijst met relaties voor een bepaalde dubbele in de grafiek kunt u het volgende gebruiken:

```csharp
await client.GetRelationshipsAsync(id);
```

Dit retourneert een `Azure.Pageable<T>` of `Azure.AsyncPageable<T>` , afhankelijk van of u de synchrone of asynchrone versie van de aanroep gebruikt.

Hier volgt een volledig voor beeld van het ophalen van een lijst met relaties:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

U kunt de opgehaalde relaties gebruiken om naar andere apparaatdubbels in uw grafiek te navigeren. U doet dit door het veld te lezen `target` in de relatie die wordt geretourneerd en dit te gebruiken als de id voor uw volgende oproep naar `GetDigitalTwin` . 

### <a name="find-relationships-to-a-digital-twin"></a>Zoek relaties met een digitale dubbele

Azure Digital Apparaatdubbels heeft ook een API voor het zoeken van alle inkomende relaties naar een opgegeven dubbele. Dit is vaak handig voor omgekeerde navigatie of bij het verwijderen van een dubbele.

Het vorige code voorbeeld is gericht op het vinden van uitgaande relaties. Het volgende voor beeld is vergelijkbaar, maar zoekt in plaats daarvan binnenkomende relaties. Ze worden ook verwijderd zodra ze zijn gevonden.

Houd er rekening mee dat de IncomingRelationship-aanroepen niet de volledige waarde Retour neren

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Relaties verwijderen

U kunt relaties verwijderen met `DeleteRelationship(source, relId);` .

Met de eerste para meter geeft u de bron op, twee (de dubbele locatie van de relatie). De andere para meter is de relatie-ID. U hebt zowel de dubbele ID als de relatie-ID nodig, omdat relatie-Id's alleen uniek zijn binnen het bereik van een dubbele.

## <a name="create-a-twin-graph"></a>Een dubbele grafiek maken 

In het volgende code fragment worden de bewerkingen van de relatie van dit artikel gebruikt voor het maken van een dubbele grafiek van de digitale apparaatdubbels en relaties.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Een dubbele grafiek maken vanuit een spread sheet

In praktische use cases worden vaak dubbele hiërarchieën gemaakt op basis van gegevens die zijn opgeslagen in een andere data base, of mogelijk in een werk blad. In deze sectie ziet u hoe een spread sheet kan worden geparseerd.

Bekijk de volgende gegevens tabel, met een beschrijving van een set digitale apparaatdubbels en relaties die moeten worden gemaakt.

| Model    | Id | Bovenliggend | Naam van relatie | Andere gegevens |
| --- | --- | --- | --- | --- |
| Floor    | Floor01 | | | … |
| ruimte    | Room10 | Floor01 | bevat | … |
| ruimte    | Room11 | Floor01 | bevat | … |
| ruimte    | Room12 | Floor01 | bevat | … |
| Floor    | Floor02 | | | … |
| ruimte    | Room21 | Floor02 | bevat | … |
| ruimte    | Room22 | Floor02 | bevat | … |

De volgende code maakt gebruik van de [Microsoft Graph-API](https://docs.microsoft.com/graph/overview) om een spread sheet te lezen en een Azure Digital apparaatdubbels-grafiek te maken op basis van de resultaten.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Relaties beheren met CLI

Apparaatdubbels en hun relaties kunnen ook worden beheerd met behulp van de Azure Digital Apparaatdubbels CLI. De opdrachten zijn te vinden in [How to: gebruik de Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitvoeren van query's op een Azure Digital Apparaatdubbels-grafiek:
* [Concepten: query taal](concepts-query-language.md)
* [Instructies: een query uitvoeren op de dubbele grafiek](how-to-query-graph.md)