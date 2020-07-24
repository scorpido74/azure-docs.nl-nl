---
title: De Azure Digital Twins-API's en -SDK's gebruiken
titleSuffix: Azure Digital Twins
description: Zie werken met de Azure Digital Apparaatdubbels Api's, inclusief via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 34a02de8a6789a028c776e784ea4aa9692f53d9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079161"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>De Azure Digital Twins-API's en -SDK's gebruiken

Azure Digital Apparaatdubbels wordt geleverd met beide **Control-api's** en **datacenter api's** voor het beheren van uw exemplaar en de bijbehorende elementen. Dit artikel geeft een overzicht van de beschik bare Api's en de methoden om ermee te werken. U kunt de REST-Api's rechtstreeks met hun bijbehorende Swaggers of via een SDK gebruiken.

## <a name="overview-control-plane-apis"></a>Overzicht: Controling-Api's

De besturings vlak-Api's worden gebruikt voor het beheren van uw Azure Digital Apparaatdubbels-exemplaar als geheel, zodat deze bewerkingen kunnen uitvoeren zoals het maken of verwijderen van het hele exemplaar. U kunt deze ook gebruiken om eind punten te maken en te verwijderen.

De meest recente versie van de Control vlak-API voor open bare preview is _**2020-03-01-preview**_.

De Control-Api's gebruiken:
* U kunt de Api's rechtstreeks aanroepen door te verwijzen naar de meest recente [Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). Deze opslag plaats bevat ook een map met voor beelden waarin het gebruik wordt weer gegeven.
* U kunt momenteel toegang krijgen tot Sdk's voor besturings-Api's in...
  - [.Net (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([verwijzing [automatisch gegenereerd]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([bron](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([verwijzing [automatisch gegenereerd]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview))
  - [Java script](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([bron](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([bron](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go-source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

U kunt ook beheer vlak-Api's uitoefenen door interactie met Azure Digital Apparaatdubbels via de [Azure Portal](https://portal.azure.com) en [cli](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Overzicht: gegevens vlak-Api's

De gegevensfeed-Api's worden gebruikt voor het beheren van de elementen in uw Azure Digital Apparaatdubbels-exemplaar. Ze omvatten bewerkingen zoals het maken van routes, het uploaden van modellen, het maken van relaties en het beheren van apparaatdubbels. Ze kunnen breed worden onderverdeeld in de volgende categorieën:
* **DigitalTwinsModels** : de categorie DigitalTwinsModels bevat api's voor het beheren van de [modellen](concepts-models.md) in een Azure Digital apparaatdubbels-exemplaar. Beheer activiteiten zijn onder andere het uploaden, valideren, ophalen en verwijderen van modellen die zijn gemaakt in DTDL.
* **DigitalTwins** : de categorie DigitalTwins bevat de api's waarmee ontwikkel aars [digitale apparaatdubbels](concepts-twins-graph.md) en hun relaties kunnen maken, wijzigen en verwijderen in een Azure Digital apparaatdubbels-exemplaar.
* **Query** : de query categorie stelt ontwikkel aars in staat om [sets van digitale apparaatdubbels te vinden in de dubbele grafiek over de](how-to-query-graph.md) relaties.
* **EventRoutes** : de categorie EventRoutes bevat api's voor het door [sturen van gegevens](concepts-route-events.md), via het systeem en naar downstream-Services.

De meest recente versie van de data vlak-API voor open bare preview is _**2020-05-31-preview**_.

De data-vlak-Api's gebruiken:
* U kunt de Api's rechtstreeks aanroepen, door...
   - verwijzen naar de meest recente [Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Deze opslag plaats bevat ook een map met voor beelden waarin het gebruik wordt weer gegeven. 
   - de [API-referentie documentatie](https://docs.microsoft.com/rest/api/azure-digitaltwins/)weer geven.
* U kunt de .NET-SDK (C#) gebruiken. Dit is momenteel de enige gepubliceerde SDK voor interactie met deze Api's. De .NET SDK gebruiken...
   - u kunt het pakket weer geven op NuGet: [Azure. DigitalTwins. core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - u vindt de SDK-bron, met inbegrip van een map met voor beelden, in GitHub: [Azure IOT Digital apparaatdubbels-client bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - u kunt de [SDK-referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview)raadplegen.
   - u kunt gedetailleerde informatie en voor beelden van gebruik bekijken door door te gaan naar de sectie [.net (C#) SDK (Data-vlieg tuig)](#net-c-sdk-data-plane) van dit artikel.
* U kunt een SDK voor een andere taal genereren met behulp van auto rest. Volg de instructies in [*How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest*](how-to-create-custom-sdks.md).

U kunt ook datum vlak-Api's uitoefenen door interactie met Azure Digital Apparaatdubbels via de [cli](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (Data-vlak)

De Azure Digital Apparaatdubbels .NET (C#) SDK maakt deel uit van de Azure SDK voor .NET. Het is open source en is gebaseerd op de Azure Digital Apparaatdubbels data vlak-Api's.

> [!NOTE]
> Zie algemene [ontwerp principes voor Azure sdk's](https://azure.github.io/azure-sdk/general_introduction.html) en de specifieke [.net-ontwerp richtlijnen](https://azure.github.io/azure-sdk/dotnet_introduction.html)voor uitgebreide informatie over het ontwerp van de SDK.

Als u de SDK wilt gebruiken, voegt u het NuGet-pakket **Azure. DigitalTwins. core** toe aan uw project. U hebt ook het **Azure. Identity** -pakket nodig.

* In Visual Studio kunt u pakketten toevoegen met NuGet Package Manager (toegankelijk via *Hulpprogram ma's > NuGet package manager > NuGet-pakketten beheren voor oplossing*). 
* Met het opdracht regel programma .NET kunt u het volgende uitvoeren:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Zie de [*zelf studie: een client-app coderen*](tutorial-code.md)voor een gedetailleerde procedure voor het gebruik van de api's in de praktijk. 

### <a name="net-sdk-usage-examples"></a>Gebruiks voorbeelden van .NET SDK

Hier volgen enkele code voorbeelden die het gebruik van de .NET SDK illustreren.

Verificatie voor de service:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Upload een model en lijst modellen:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Apparaatdubbels maken en doorzoeken:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Raadpleeg de [*zelf studie: een client-app coderen*](tutorial-code.md) voor een overzicht van deze app-voorbeeld code. 

U kunt ook aanvullende voor beelden vinden in de [github-opslag plaats voor de .net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Helpers voor serialisatie

Zoals eerder beschreven, retour neren de kern SDK-methoden dubbele gegevens als JSON. De SDK bevat echter ook hulp klassen voor serialisatie. Met deze hulp functies kunt u snel dubbele gegevens maken of deserialiseren voor toegang tot basis informatie.

De beschik bare Help klassen zijn:
* `BasicDigitalTwin`: Hiermee worden de belangrijkste gegevens van een digitale dubbele
* `BasicRelationship`: Hiermee worden de kern gegevens van een relatie aangegeven
* `UpdateOperationUtility`: Hiermee wordt informatie over de JSON-patch gebruikt in Update aanroepen
* `WriteableProperty`: Vertegenwoordigt meta gegevens van eigenschap

##### <a name="deserialize-a-digital-twin"></a>Een digitale dubbele

U kunt dubbele gegevens altijd deserialiseren met de JSON-bibliotheek van uw keuze, zoals `System.Test.Json` of `Newtonsoft.Json` . Voor eenvoudige toegang tot een dubbele manier is de Help-klasse zo wat handiger.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

De `BasicDigitalTwin` helperklasse biedt u ook toegang tot de eigenschappen die zijn gedefinieerd op de dubbele, via een `Dictionary<string, object>` . Als u de eigenschappen van de dubbele wilt weer geven, kunt u het volgende gebruiken:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Een digitale dubbele

U kunt met behulp `BasicDigitalTwin` van de-klasse gegevens voorbereiden voor het maken van een dubbele instantie:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

De bovenstaande code is gelijk aan de volgende ' hand matig ' variant:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Een relatie deserialiseren

U kunt relatie gegevens altijd deserialiseren met de JSON-bibliotheek van uw keuze, zoals `System.Test.Json` of `Newtonsoft.Json` . Voor eenvoudige toegang tot een relatie is de Help-klasse zo wat handiger.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

De `BasicRelationship` helperklasse biedt u ook toegang tot de eigenschappen die zijn gedefinieerd voor de relatie, via een `Dictionary<string, object>` . Als u eigenschappen wilt weer geven, kunt u het volgende gebruiken:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Relatie maken

Met behulp van de `BasicDigitalTwin` -klasse kunt u ook gegevens voorbereiden voor het maken van relaties op een dubbele instantie:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Een patch maken voor dubbele update

Update-aanroepen voor apparaatdubbels en relaties gebruiken [JSON-patch](http://jsonpatch.com/) structuur. Als u lijsten met JSON-patch bewerkingen wilt maken, kunt u de `UpdateOperationsUtility` klasse gebruiken zoals hieronder wordt weer gegeven.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Algemene opmerkingen over API/SDK-gebruik

> [!NOTE]
> Tijdens de preview-versie biedt Azure Digital Apparaatdubbels geen ondersteuning voor **het delen van cross-Origin-resources (CORS)**. Als u dus een REST API aanroept vanuit een browser-app, een API Management-interface [(APIM)](../api-management/api-management-key-concepts.md) of een [Power apps](https://docs.microsoft.com/powerapps/powerapps-overview) -connector, wordt er mogelijk een beleids fout weer geven.
> U kunt dit probleem op een van de volgende manieren oplossen:
> * De CORS-header `Access-Control-Allow-Origin` van het bericht verwijderen. Deze header geeft aan of het antwoord kan worden gedeeld. 
> * U kunt ook een CORS-proxy maken en de Azure Digital Apparaatdubbels REST API aanvraag door voeren. 

De volgende lijst bevat aanvullende details en algemene richt lijnen voor het gebruik van de Api's en Sdk's.

* Als u de SDK wilt gebruiken, maakt u een instantie van de `DigitalTwinsClient` klasse. De constructor vereist referenties die kunnen worden verkregen met diverse verificatie methoden in het `Azure.Identity` pakket. `Azure.Identity`Zie de documentatie van de [naam ruimte](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)voor meer informatie. 
* Het is `InteractiveBrowserCredential` handig om aan de slag te gaan, maar er zijn verschillende andere opties, waaronder referenties voor [beheerde identiteit](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet), die u waarschijnlijk gebruikt voor het verifiëren van [Azure functions die zijn ingesteld met MSI](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) op Azure Digital apparaatdubbels. `InteractiveBrowserCredential`Zie de documentatie van de [klasse](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)voor meer informatie.
* Alle service-API-aanroepen worden weer gegeven als lidfuncties op de `DigitalTwinsClient` klasse.
* Alle service functies bestaan in synchrone en asynchrone versies.
* Alle service functies genereren een uitzonde ring voor elke retour status van 400 of hoger. Zorg ervoor dat u aanroepen in een sectie inpakt `try` en ten minste hebt ondervangen `RequestFailedExceptions` . Zie [hier](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet)voor meer informatie over dit type uitzonde ring.
* De meeste service methoden retour neren `Response<T>` of ( `Task<Response<T>>` voor de asynchrone aanroepen), waarbij `T` de klasse van het retour object voor de service aanroep is. De [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet) klasse bevat de retour waarde van de service en geeft retour waarden in het `Value` veld weer.  
* Service methoden met geretourneerde resultaten `Pageable<T>` of `AsyncPageable<T>` als resultaat. Zie hier voor meer informatie over de `Pageable<T>` - [here](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview)klasse `AsyncPageable<T>` . Zie [hier](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview)voor meer informatie.
* U kunt met behulp van een lus de resultaten van de pagina door lopen `await foreach` . Zie [hier](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)voor meer informatie over dit proces.
* De onderliggende SDK is `Azure.Core` . Raadpleeg de [documentatie van Azure namespace](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) voor naslag informatie over de SDK-infra structuur en typen.

Service methoden retour neren waar mogelijk sterk getypeerde objecten. Omdat Azure Digital Apparaatdubbels echter is gebaseerd op modellen die zijn geconfigureerd door de gebruiker tijdens runtime (via DTDL-modellen die zijn geüpload naar de service), nemen veel service-Api's dubbele gegevens in JSON-indeling en retour neer.

## <a name="monitor-api-metrics"></a>Metrische gegevens van de API bewaken

API-metrische gegevens, zoals aanvragen, latentie en fout frequentie, kunnen worden weer gegeven in de [Azure Portal](https://portal.azure.com/). 

Zoek op de start pagina van de portal naar uw Azure Digital Apparaatdubbels-exemplaar om de details ervan op te halen. Selecteer de optie **metrische gegevens** in het menu van het Azure Digital apparaatdubbels-exemplaar om de pagina *metrische gegevens* weer te geven.

:::image type="content" source="media/how-to-use-apis-sdks/metrics.png" alt-text="De pagina metrische gegevens van een Azure Digital Apparaatdubbels-instantie in de Azure Portal":::

Hier kunt u de metrische gegevens voor uw exemplaar bekijken en aangepaste weer gaven maken.

## <a name="next-steps"></a>Volgende stappen

Zie hoe u de Api's gebruikt om een Azure Digital Apparaatdubbels-exemplaar en-verificatie in te stellen:
* [*Instructies: een exemplaar en verificatie instellen*](how-to-set-up-instance-scripted.md)

Of volg de stappen voor het maken van een client-app, zoals de toepassing die wordt gebruikt in deze procedure:
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
