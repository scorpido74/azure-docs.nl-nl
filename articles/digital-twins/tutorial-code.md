---
title: Een client-app coderen
titleSuffix: Azure Digital Twins
description: Zelfstudie voor het schrijven van de minimale code voor een client-app, met behulp van de .NET ( C# ) SDK.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 52a22dd215769208b60f180b576ae5763d67eade
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723466"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Coderen met de Azure Digital Twins-API's

Het is gebruikelijk dat ontwikkelaars die met Azure Digital Twins werken, een clienttoepassing schrijven voor interactie met hun exemplaar van de Azure Digital Twins-service. Deze zelfstudie voor ontwikkelaars biedt een inleiding op het programmeren van de Azure Digital Twins-service, met behulp van de [Azure IoT Digital Twins-clientbibliotheek voor .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). U wordt stap voor stap begeleid bij het schrijven van een C# console-client-app, vanaf het begin.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt de opdrachtregel gebruikt voor het instellen en projectwerk. Daarom kunt u elke code-editor gebruiken om de oefeningen door te lopen.

U gaat als volgt aan de slag:
* Elk soort code-editor
* **.NET Core 3.1** op uw ontwikkelcomputer. U kunt deze versie van .NET Core SDK voor meerdere platforms downloaden van [.NET Core 3.1. downloaden](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Project instellen

Wanneer u klaar bent om met uw Azure Digital Twins-exemplaar, moet u het client-app-project instellen. 

Open een opdrachtprompt of een ander consolevenster op uw computer en maak een lege projectmap waarin u uw werk wilt opslaan tijdens deze zelfstudie. Geef de map een willekeurige naam (bijvoorbeeld *DigitalTwinsCodeTutorial*).

Navigeer naar de nieuwe map.

Maak een leeg .NET-console-app-project in de projectmap. Voer in het opdrachtvenster de volgende opdracht uit om een minimaal C#-project voor de console te maken:

```cmd/sh
dotnet new console
```

Hiermee maakt u een aantal bestanden in uw map, met daarin de naam *Program.cs* waarin u de meeste code schrijft.

Voeg vervolgens twee vereiste afhankelijkheden toe voor het werken met Azure Digital Twins:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.1.1
```

De eerste afhankelijkheid is de [Azure IoT Digital-clientbibliotheek voor .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). De tweede afhankelijkheid biedt hulpprogramma's voor de verificatie bij Azure.

Houd het opdrachtvenster geopend, omdat u het in de zelfstudie blijft gebruiken.

## <a name="get-started-with-project-code"></a>Aan de slag met projectcode

In deze sectie gaat u beginnen met het schrijven van de code voor uw nieuwe app-project om met Azure Digital Twins te werken. De behandelde acties zijn onder meer:
* Verificatie op basis van de service
* Een model uploaden
* Fouten waarnemen
* Digitale tweelingen maken
* Relaties maken
* Query uitvoeren voor digitale tweeling

Er is ook een sectie met de volledige code aan het einde van de zelfstudie. U kunt dit als referentie gebruiken om uw programma te controleren terwijl u dit doet.

Open het bestand *Program.cs* in een code-editor om te beginnen. Er wordt een sjabloon met minimale code weergegeven die er als volgt uitziet:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Voeg eerst enkele `using` regels toe boven aan de code om de benodigde afhankelijkheden op te halen.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Vervolgens voegt u code toe aan dit bestand om een deel van de functionaliteit in te vullen. 

### <a name="authenticate-against-the-service"></a>Verificatie op basis van de service

Het eerste wat uw app moet doen, is verifiëren op basis van de Azure Digital Twins-service. Vervolgens kunt u een service-clientklasse maken voor toegang tot de SDK-functies.

Als u zich wilt verifiëren, hebt u soorten informatie nodig:
* De *Map-id* voor uw abonnement
* De *Toepassings-id (client)* die is gemaakt bij het eerder instellen van Azure Digital Twins
* The *hostName* van uw exemplaar van Azure Digital Twins

>[!TIP]
> Als u uw *Map-id* niet weet, kunt u deze downloaden door deze opdracht uit te voeren in [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

Plak de volgende code in *Program.cs* onder de regel "Hello World!" in de `Main` methode. Stel de waarde van `adtInstanceUrl` in op uw Azure Digital Twins-exemplaar *hostnaam*, `clientId` naar uw *Toepassings-ID* en `tenantId` naar uw *Map-id*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Sla het bestand op. 

In dit voorbeeld wordt een interactieve browserreferentie gebruikt:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Dit type referentie zorgt ervoor dat er een browservenster wordt geopend, waarin u wordt gevraagd uw Azure-referenties op te geven. 

>[!NOTE]
> Zie de documentatie voor de [Microsoft Identity platform-verificatiebibliotheken](../active-directory/develop/reference-v2-libraries.md) voor informatie over andere typen referenties.

Voer in het opdrachtvenster de code uit met de volgende opdracht: 

```cmd/sh
dotnet run
```

Hiermee herstelt u de afhankelijkheden bij de eerste uitvoering en voert u het programma uit. 
* Als er geen fout optreedt, drukt het programma *Serviceclient gemaakt, klaar on te starten* af.
* Omdat er nog geen foutafhandeling in dit project is, ziet u een uitzondering die is gegenereerd door de code als er iets mis gaat.

### <a name="upload-a-model"></a>Een model uploaden

Azure Digital Twins heeft geen intrinsieke domeinwoordenlijst. De typen elementen in uw omgeving die u kunt vertegenwoordigen in Azure Digital Twins, worden door u gedefinieerd met behulp van **Modellen**. [Modellen](concepts-models.md) lijken op klassen in objectgeoriënteerde programmeertalen. Ze bieden door de gebruiker gedefinieerde sjablonen die [digitale tweelingen](concepts-twins-graph.md) later kunnen volgen en instantiëren. Ze worden geschreven in een JSON-achtige taal met de naam **Digital Twins Definition Language (DTDL)** .

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van ten minste een model in een DTDL-bestand.

Maak in de map waar u het project hebt gemaakt een nieuw *.json*-bestand met de naam *SampleModel.json*. Plak in de volgende hoofdtekst van het bestand: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Als u Visual Studio voor deze zelfstudie gebruikt, wilt u mogelijk het zojuist gemaakte JSON-bestand selecteren en de eigenschap *Kopiëren naar uitvoermap* instellen in de Eigenschappencontrole naar te *Kopiëren als nieuwer* of *Altijd kopiëren*. Hierdoor kan Visual Studio het JSON-bestand met het standaardpad vinden wanneer u het programma uitvoert met **F5** tijdens de rest van de zelfstudie.

> [!TIP] 
> Er is een taalagnostisch [DTDL-validatorvoorbeeld](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) dat u kunt gebruiken om modeldocumenten te controleren en u ervan te verzekeren dat de DTDL geldig is. Het voorbeeld is gemaakt op basis van de DTDL-parserbibliotheek, waarover u meer kunt lezen in [*Instructies: Modellen parseren en valideren*](how-to-parse-models.md).

Voeg vervolgens wat meer code toe aan *Program.cs* om het model dat u zojuist hebt gemaakt, te uploaden naar uw Azure Digital Twins-exemplaar.

Voer eerst boven aan het bestand de volgende `using`-instructies toe:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Vervolgens bereidt u het gebruik van de asynchrone methoden in de C# service-SDK voor door de handtekening van de `Main` methode te wijzigen zodat asynchrone uitvoering kan worden uitgevoerd. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> Het gebruik van `async` is niet strikt vereist, omdat de SDK ook synchrone versies van alle aanroepen biedt. In deze zelfstudie gebruikt `async`.

Vervolgens wordt de eerste bit van code geleverd die samenwerkt met de Azure Digital Twins-service. Met deze code wordt het DTDL-bestand dat u hebt gemaakt op basis van de schijf, geladen en vervolgens geüpload naar uw Azure Digital Twins-service-exemplaar. 

Plak de volgende code onder de autorisatiecode die u eerder hebt toegevoegd.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Voer in het opdrachtvenster de programma uit met de volgende opdracht: 

```cmd/sh
dotnet run
```
‘Een model uploaden’ wordt afgedrukt in de uitvoer, maar er is nog geen uitvoer om aan te geven of het uploaden van modellen is geslaagd.

Als u een afdrukinstructie wilt toevoegen om aan te geven of de modellen daadwerkelijk zijn geüpload, voegt u de volgende code toe, direct na de vorige sectie:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Voordat u het programma opnieuw uitvoert om deze nieuwe code te testen, houd er rekening mee dat de laatste keer dat u het programma hebt uitgevoerd, u uw model al hebt geüpload. Met Azure Digital Twins kunt u hetzelfde model niet twee keer uploaden, dus verwacht een uitzondering wanneer u het programma opnieuw uitvoert.

Voer in het opdrachtvenster de programma uit met de volgende opdracht:

```cmd/sh
dotnet run
```

Het programma moet een uitzondering genereren. Wanneer u probeert een model te uploaden dat al is geüpload, retourneert de service de fout "ongeldige aanvraag" via de REST API. Als gevolg hiervan zal de Azure Digital Twins-client SDK een uitzondering genereren voor elke serviceretourcode die niet geslaagd is. 

In de volgende sectie vindt u informatie over uitzonderingen zoals deze en hoe u deze in uw code kunt verwerken.

### <a name="catch-errors"></a>Fouten detecteren

Als u wilt voorkomen dat het programma vastloopt, kunt u uitzonderingscode toevoegen rond de code voor het uploaden van het model. Verpak de bestaande clientaanroep `client.CreateModelsAsync` in een try/catch-handler, zoals:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Als u het programma nu uitvoert met `dotnet run` in uw opdrachtvenster, ziet u dat er een foutcode wordt weergegeven. De uitvoer ziet er ongeveer zo uit:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Vanaf dit punt verstuurt de zelfstudie alle aanroepen naar servicemethoden in try/catch-handlers.

### <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu u een model naar Azure Digital Twins hebt geüpload, kunt u deze modeldefinitie gebruiken voor het maken van **digitale tweelingen**. [Digitale tweelingen](concepts-twins-graph.md) zijn exemplaren van een model en vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. In deze sectie wordt een aantal digitale tweelingen gemaakt op basis van het model dat u eerder hebt geüpload.

Voeg bovenaan een nieuwe `using`-instructie toe, omdat u de ingebouwde .NET JSON-serializer nodig hebt in `System.Text.Json`:

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Voeg vervolgens de volgende code toe aan het einde van de `Main` methode om drie digitale tweelingen te maken en initialiseren op basis van dit model.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. Herhaal dit vervolgens om het programma opnieuw uit te voeren. 

U ziet dat er geen fout wordt gegenereerd wanneer de tweelingen de tweede keer worden gemaakt, zelfs als tweeling al bestaan na de eerste uitvoering. In tegenstelling tot het maken van een model, is het maken van tweelingen op het REST-niveau een *PUT*-aanroep met *upsert*-semantiek. Dit betekent dat als er al een tweeling bestaat, er wordt geprobeerd om deze opnieuw te maken. Geen fout vereist.

### <a name="create-relationships"></a>Relaties maken

Vervolgens kunt u **relaties** maken tussen deze gemaakte tweelingen, om ze te verbinden in een **tweelinggrafiek**. [Tweelinggrafieken](concepts-twins-graph.md) worden gebruikt om uw gehele omgeving voor te stellen.

Als u relaties wilt maken, voegt u een `using`-instructie toe voor het basistype relatie in de SDK: sla dit over als deze al is toegevoegd.
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Voeg vervolgens een nieuwe statische methode toe aan de klasse `Program`, onder de methode `Main`:
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

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `CreateRelationship`-code aan te roepen:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Voer in het opdrachtvenster de programma uit met `dotnet run`.

Houd er rekening mee dat Azure Digital Twins niet in staat is om een relatie te maken als er al een met dezelfde id bestaat, dus als u het programma meerdere keren uitvoert, worden er uitzonderingen voor het maken van relaties weergegeven. Met deze code worden de uitzonderingen onderschept en genegeerd. 

### <a name="list-relationships"></a>Lijst met relaties

Met de volgende code die u toevoegt, kunt u de lijst met relaties zien die u hebt gemaakt.

Voeg de volgende nieuwe methode toe aan de klasse `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `ListRelationships`-code aan te roepen:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. Er wordt een lijst weergegeven met alle relaties die u hebt gemaakt.

### <a name="query-digital-twins"></a>Query's uitvoeren op digitale tweelingen

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden.

In de laatste sectie van de code die in deze zelfstudie moet worden toegevoegd, wordt een query uitgevoerd op het Azure Digital Twins-exemplaar. Met de query die in dit voorbeeld wordt gebruikt, worden alle digitale tweelingen in het exemplaar geretourneerd.

Voeg de volgende code aan het einde van de methode `Main` toe:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. In de uitvoer ziet u alle digitale tweelingen in dit exemplaar.

## <a name="complete-code-example"></a>Volledig voorbeeld van de code

Op dit punt in de zelfstudie hebt u een volledige client-app, waarmee u basisacties kunt uitvoeren op Azure Digital Twins. Ter referentie wordt de volledige code van het programma in *Program.cs* hieronder weergegeven:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
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

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

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
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Resources opschonen
 
Het exemplaar dat in deze zelfstudie wordt gebruikt, kan opnieuw worden gebruikt in de volgende zelfstudie, [*Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app*](tutorial-command-line-app.md). Als u van plan bent om door te gaan naar de volgende zelfstudie, kunt u het Azure Digital Twins-exemplaar dat u hier instelt, behouden.
 
Als u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen met de volgende stappen.

Met behulp van [Azure Cloud Shell](https://shell.azure.com) kunt u alle Azure-resources in een resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Hiermee verwijdert u de resourcegroep en het Azure Digital Twins-exemplaar.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Open een Azure Cloud Shell en voer de volgende opdracht uit om de resourcegroep en alles daarin te verwijderen.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Verwijder vervolgens de Azure Active Directory-app-registratie die u voor uw client-app hebt gemaakt met deze opdracht:

```azurecli
az ad app delete --id <your-application-ID>
```

Verwijder ten slotte de projectmap die u hebt gemaakt op uw lokale machine.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een volledig nieuwe .NET-console-clienttoepassing gemaakt. U hebt code voor deze client-app geschreven om de basisacties uit te voeren op een Azure Digital Twins-exemplaar.

Ga verder met de volgende zelfstudie om de dingen te bekijken die u kunt doen met een dergelijke voorbeeld-client-app: 

> [!div class="nextstepaction"]
> [*Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app*](tutorial-command-line-app.md)

U kunt ook de code uitbreiden die u in deze zelfstudie hebt geschreven door meer beheerbewerkingen te leren in de artikelen met procedures of door te kijken naar de conceptdocumentatie voor meer informatie over de elementen waarmee u in de zelfstudie hebt gewerkt.
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)
* [*Concepten: Aangepaste modellen*](concepts-models.md)
