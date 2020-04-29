---
title: Een RESTful-eindpunt maken
description: In deze zelf studie wordt uitgelegd hoe u een REST-eind punt kunt ontwerpen voor aangepaste providers. U vindt hier informatie over het afhandelen van aanvragen en antwoorden op ondersteunde, resterende HTTP-methoden.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650021"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Een REST-eind punt ontwerpen voor aangepaste providers

Een aangepaste provider is een contract tussen Azure en een eind punt. Met aangepaste providers kunt u werk stromen aanpassen op Azure. In deze zelf studie leert u hoe u een aanstaand eind punt voor een aangepaste provider maakt. Als u niet bekend bent met aangepaste Azure-providers, raadpleegt u [het overzicht van aangepaste resource providers](overview.md).

> [!NOTE]
> In deze zelf studie wordt gebruikgemaakt van de zelf studie [Azure functions voor aangepaste Azure-providers instellen](./tutorial-custom-providers-function-setup.md). Enkele van de stappen in deze zelf studie werken alleen als er een Azure-functie-app is ingesteld voor gebruik met aangepaste providers.

## <a name="work-with-custom-actions-and-custom-resources"></a>Werken met aangepaste acties en aangepaste resources

In deze zelf studie werkt u de functie-app bij om te werken als een REST-eind punt voor uw aangepaste provider. Resources en acties in azure zijn gemodelleerd na de volgende basis specificatie:

- **Put**: een nieuwe resource maken
- **Get (instantie)**: een bestaande resource ophalen
- **Verwijderen**: een bestaande resource verwijderen
- **Bericht**: een actie activeren
- **Get (verzameling)**: alle bestaande resources weer geven

 Voor deze zelf studie gebruikt u Azure-tabel opslag. Maar elke Data Base of opslag service kan werken.

## <a name="partition-custom-resources-in-storage"></a>Aangepaste resources in de opslag partitioneren

Omdat u een REST-service maakt, moet u de gemaakte resources opslaan. Voor Azure Table-opslag moet u partitie-en rijgegevens genereren voor uw gegevens. Voor aangepaste providers moeten gegevens worden gepartitioneerd naar de aangepaste provider. Wanneer een binnenkomende aanvraag wordt verzonden naar de aangepaste provider, voegt de aangepaste provider de `x-ms-customproviders-requestpath` header toe aan uitgaande aanvragen aan het eind punt.

In het volgende voor beeld `x-ms-customproviders-requestpath` ziet u een koptekst voor een aangepaste resource:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Op basis van de `x-ms-customproviders-requestpath` koptekst van het voor beeld kunt u de *PartitionKey* -en *rowKey* -para meters voor uw opslag maken, zoals wordt weer gegeven in de volgende tabel:

Parameter | Template | Beschrijving
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | De *partitionKey* para meter geeft u op hoe de gegevens worden gepartitioneerd. Doorgaans worden de gegevens gepartitioneerd door het exemplaar van de aangepaste provider.
*rowKey* | `{myResourceType}:{myResourceName}` | De *rowKey* para meter geeft u de individuele id voor de gegevens op. Meestal is de id de naam van de resource.

U moet ook een nieuwe klasse maken om uw aangepaste resource te model leren. In deze zelf studie voegt u de volgende **CustomResource** -klasse toe aan uw functie-app:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** is een eenvoudige, generieke klasse die invoer gegevens accepteert. Het is gebaseerd op **TableEntity**, die wordt gebruikt om gegevens op te slaan. De klasse **CustomResource** neemt twee eigenschappen over van **TableEntity**: **partitionKey** en **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>RESTERENDE methoden voor aangepaste provider ondersteunen

> [!NOTE]
> Als u de code niet rechtstreeks vanuit deze zelf studie kopieert, moet de antwoord inhoud een geldige JSON zijn waarmee `Content-Type` de header `application/json`wordt ingesteld op.

Nu u het partitioneren van gegevens hebt ingesteld, maakt u de basis-en trigger methoden voor aangepaste resources en aangepaste acties. Omdat aangepaste providers als proxy's fungeren, moet het REST-eind punt de aanvraag en het antwoord model en verwerken. De volgende code fragmenten laten zien hoe u de eenvoudige, REST bewerkingen kunt afhandelen.

### <a name="trigger-a-custom-action"></a>Een aangepaste actie activeren

Voor aangepaste providers wordt een aangepaste actie geactiveerd via POST-aanvragen. Een aangepaste actie kan optioneel een aanvraag tekst accepteren die een set invoer parameters bevat. De actie retourneert vervolgens een reactie die het resultaat van de actie signaleert en of deze is geslaagd of mislukt.

Voeg de volgende **TriggerCustomAction** -methode toe aan uw functie-app:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

De methode **TriggerCustomAction** accepteert een binnenkomende aanvraag en ECHO keert het antwoord terug met een status code.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Voor aangepaste providers wordt een aangepaste resource gemaakt via PUT-aanvragen. De aangepaste provider accepteert een JSON-aanvraag tekst die een set eigenschappen voor de aangepaste resource bevat. Resources in azure volgen een REST model. U kunt dezelfde aanvraag-URL gebruiken om een resource te maken, op te halen of te verwijderen.

Voeg de volgende **CreateCustomResource** -methode toe om nieuwe resources te maken:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Met de methode **CreateCustomResource** wordt de inkomende aanvraag bijgewerkt zodat de **id**, **naam**en het **type**van de Azure-specifieke velden worden meegenomen. Deze velden zijn eigenschappen op het hoogste niveau die door services in Azure worden gebruikt. Ze kunnen de aangepaste provider samen werken met andere services, zoals Azure Policy, Azure Resource Manager sjablonen en Azure-activiteiten logboek.

Eigenschap | Voorbeeld | Beschrijving
---|---|---
**naam** | {myCustomResourceName} | De naam van de aangepaste resource
**voert** | Micro soft. CustomProviders/resourceProviders/{resourceTypeName} | De naam ruimte van het bron type
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/micro soft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | De resource-ID

Naast het toevoegen van de eigenschappen, hebt u het JSON-document ook opgeslagen in azure-tabel opslag.

### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

Voor aangepaste providers wordt een aangepaste resource opgehaald via GET-aanvragen. Een aangepaste provider accepteert *geen* JSON-aanvraag tekst. Voor GET-aanvragen gebruikt het eind punt `x-ms-customproviders-requestpath` de koptekst om de al gemaakte resource te retour neren.

Voeg de volgende **RetrieveCustomResource** -methode toe om bestaande resources op te halen:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

In azure volgen resources een onderliggend model. De aanvraag-URL waarmee een resource wordt gemaakt, retourneert ook de resource als er een GET-aanvraag wordt uitgevoerd.

### <a name="remove-a-custom-resource"></a>Een aangepaste resource verwijderen

Voor aangepaste providers wordt een aangepaste resource verwijderd via VERWIJDERings aanvragen. Een aangepaste provider accepteert *geen* JSON-aanvraag tekst. Voor een Verwijder aanvraag gebruikt het eind punt de `x-ms-customproviders-requestpath` koptekst om de al gemaakte resource te verwijderen.

Voeg de volgende **RemoveCustomResource** -methode toe om bestaande resources te verwijderen:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

In azure volgen resources een onderliggend model. De aanvraag-URL waarmee een resource wordt gemaakt, verwijdert ook de resource als er een aanvraag voor verwijderen wordt uitgevoerd.

### <a name="list-all-custom-resources"></a>Alle aangepaste resources weer geven

Voor aangepaste providers kunt u een lijst met bestaande aangepaste resources opsommen met behulp van verzameling GET-aanvragen. Een aangepaste provider accepteert *geen* JSON-aanvraag tekst. Voor een verzameling GET-aanvragen gebruikt het eind punt de `x-ms-customproviders-requestpath` koptekst om de al gemaakte resources op te sommen.

Voeg de volgende **EnumerateAllCustomResources** -methode toe om de bestaande resources op te sommen:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> De syntaxis van de RowKey QueryComparisons. GreaterThan en QueryComparisons. LessThan is Azure Table Storage voor het uitvoeren van een ' startsWith-query voor teken reeksen.

Als u alle bestaande resources wilt weer geven, genereert u een Azure Table Storage-query die ervoor zorgt dat de resources bestaan onder uw aangepaste provider partitie. De query controleert vervolgens of de rij-sleutel met dezelfde `{myResourceType}` waarde begint.

## <a name="integrate-restful-operations"></a>REST-bewerkingen integreren

Nadat alle resterende methoden zijn toegevoegd aan de functie-app, werkt u de methode Main **Run** bij die de functies aanroept voor het afhandelen van de verschillende rest-aanvragen:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

De bijgewerkte **uitvoerings** methode bevat nu de *tableStorage* -invoer binding die u hebt toegevoegd voor Azure-tabel opslag. Het eerste deel van de methode leest de `x-ms-customproviders-requestpath` header en gebruikt de `Microsoft.Azure.Management.ResourceManager.Fluent` bibliotheek om de waarde te parseren als een resource-id. De `x-ms-customproviders-requestpath` header wordt verzonden door de aangepaste provider en geeft het pad op van de binnenkomende aanvraag.

Door de geparseerde Resource-ID te gebruiken, kunt u de **partitionKey** -en **rowKey** -waarden genereren voor de gegevens om aangepaste resources op te zoeken of op te slaan.

Nadat u de methoden en klassen hebt toegevoegd, moet u de methoden **die worden gebruikt** voor de functie-app bijwerken. Voeg de volgende code toe boven aan het C#-bestand:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Als u op een wille keurig moment van deze zelf studie bent kwijt geraakt, kunt u het voor beeld van de volledige code vinden in de referentie van de [GEruste eind punt van de aangepaste provider](./reference-custom-providers-csharp-endpoint.md). Nadat u de functie-app hebt voltooid, slaat u de URL van de functie-app op. Het kan worden gebruikt om de functie-app in latere zelf studies te activeren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een REST-eind punt ontworpen om te werken met een Azure-eind punt voor een aangepaste provider. Ga voor meer informatie over het maken van een aangepaste provider naar de artikel [zelf studie: een aangepaste provider maken](./tutorial-custom-providers-create.md).
