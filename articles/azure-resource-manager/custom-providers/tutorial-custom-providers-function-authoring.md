---
title: Een RESTful-eindpunt maken
description: In deze zelfstudie ziet u hoe u een RESTful-eindpunt voor aangepaste providers maakt. Hierin wordt beschreven hoe u aanvragen en antwoorden voor de ondersteunde RESTful HTTP-methoden verwerken.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650021"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Een RESTful-eindpunt voor aangepaste providers maken

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers u werkstromen op Azure aanpassen. In deze zelfstudie ziet u hoe u een aangepast restful-eindpunt van de provider maakt. Als u niet bekend bent met Azure Custom Providers, raadpleegt u [het overzicht over aangepaste resourceproviders.](overview.md)

> [!NOTE]
> Deze zelfstudie bouwt voort op de zelfstudie [Azure-functies instellen voor Azure Custom Providers.](./tutorial-custom-providers-function-setup.md) Sommige stappen in deze zelfstudie werken alleen als een Azure-functie-app is ingesteld om te werken met aangepaste providers.

## <a name="work-with-custom-actions-and-custom-resources"></a>Werken met aangepaste acties en aangepaste resources

In deze zelfstudie werkt u de functie-app bij om te werken als een RESTful-eindpunt voor uw aangepaste provider. Resources en acties in Azure worden gemodelleerd naar de volgende basisspecificatie voor RESTful:

- **PUT:** Een nieuwe bron maken
- **GET (instantie)**: Een bestaande resource ophalen
- **VERWIJDEREN:** een bestaande bron verwijderen
- **POST:** Een actie activeren
- **GET (verzameling)**: Alle bestaande bronnen weergeven

 Voor deze zelfstudie gebruikt u Azure Table-opslag. Maar elke database of opslagservice kan werken.

## <a name="partition-custom-resources-in-storage"></a>Aangepaste resources voor partitie in opslag

Omdat u een RESTful-service maakt, moet u de gemaakte resources opslaan. Voor Azure Table-opslag moet u partitie- en rijsleutels voor uw gegevens genereren. Voor aangepaste providers moeten gegevens worden verdeeld over de aangepaste provider. Wanneer een binnenkomende aanvraag naar de aangepaste provider `x-ms-customproviders-requestpath` wordt verzonden, voegt de aangepaste provider de header toe aan uitgaande aanvragen aan het eindpunt.

In het volgende `x-ms-customproviders-requestpath` voorbeeld wordt een koptekst voor een aangepaste resource weergegeven:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Op basis van `x-ms-customproviders-requestpath` de koptekst van het voorbeeld u de parameters *partitionKey* en *rowKey* voor uw opslag maken, zoals weergegeven in de volgende tabel:

Parameter | Template | Beschrijving
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | De parameter *partitionKey* geeft aan hoe de gegevens worden verdeeld. Meestal worden de gegevens verdeeld door de aangepaste providerinstantie.
*rowKey* | `{myResourceType}:{myResourceName}` | De parameter *rowKey* geeft de afzonderlijke id voor de gegevens op. Meestal is de id de naam van de resource.

U moet ook een nieuwe klasse maken om uw aangepaste resource te modelleren. In deze zelfstudie voegt u de volgende klasse **CustomResource** toe aan uw functie-app:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** is een eenvoudige, algemene klasse die alle invoergegevens accepteert. Het is gebaseerd op **TableEntity**, die wordt gebruikt om gegevens op te slaan. De klasse **CustomResource** erft twee eigenschappen van **TableEntity:** **partitionKey** en **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Ondersteuning voor restful-methoden voor aangepaste leveranciers

> [!NOTE]
> Als u de code niet rechtstreeks vanuit deze zelfstudie kopieert, moet `Content-Type` de `application/json`antwoordinhoud geldig JSON zijn die de koptekst instelt op .

Nu u gegevenspartitionering hebt ingesteld, maakt u de basisCRUd en activeert u de methoden voor aangepaste resources en aangepaste acties. Omdat aangepaste providers fungeren als proxy's, moet het RESTful-eindpunt de aanvraag en het antwoord modelleren en verwerken. In de volgende codefragmenten ziet u hoe u de basisbewerkingen van RESTful afhandelen.

### <a name="trigger-a-custom-action"></a>Een aangepaste actie activeren

Voor aangepaste providers wordt een aangepaste actie geactiveerd via POST-aanvragen. Een aangepaste actie kan optioneel een aanvraaginstantie accepteren die een set invoerparameters bevat. De actie retourneert vervolgens een reactie die het resultaat van de actie aangeeft en of deze is geslaagd of mislukt.

Voeg de volgende **Methode TriggerCustomAction** toe aan uw functie-app:

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

De **methode TriggerCustomAction** accepteert een binnenkomend verzoek en weerkaatst het antwoord met een statuscode.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Voor aangepaste providers wordt een aangepaste resource gemaakt via PUT-aanvragen. De aangepaste provider accepteert een JSON-aanvraaginstantie, die een set eigenschappen voor de aangepaste resource bevat. Resources in Azure volgen een RESTful-model. U dezelfde aanvraag-URL gebruiken om een bron te maken, op te halen of te verwijderen.

Voeg de volgende **methode CreateCustomResource** toe om nieuwe bronnen te maken:

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

Met de methode **CreateCustomResource** wordt de binnenkomende aanvraag bijgewerkt om de Azure-specifieke velden **id,** **naam**en **type**op te nemen . Deze velden zijn eigenschappen op het hoogste niveau die worden gebruikt door services in Azure. Hiermee kunnen de aangepaste provider samenwerken met andere services zoals Azure Policy, Azure Resource Manager Templates en Azure Activity Log.

Eigenschap | Voorbeeld | Beschrijving
---|---|---
**Naam** | {myCustomResourceName} | De naam van de aangepaste resource
**Type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | De naamruimte resourcetype
**id** | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | De resource-id

Naast het toevoegen van de eigenschappen, hebt u het JSON-document ook opgeslagen in Azure Table-opslag.

### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

Voor aangepaste providers wordt een aangepaste resource opgehaald via GET-aanvragen. Een aangepaste provider *accepteert geen* JSON-aanvraaginstantie. Voor GET-aanvragen gebruikt het `x-ms-customproviders-requestpath` eindpunt de koptekst om de reeds gemaakte bron terug te sturen.

Voeg de volgende **methode CustomResource ophalen** toe om bestaande bronnen op te halen:

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

In Azure volgen resources een RESTful-model. De url van de aanvraag waarmee een resource wordt gemaakt, retourneert de bron ook als een GET-aanvraag wordt uitgevoerd.

### <a name="remove-a-custom-resource"></a>Een aangepaste resource verwijderen

Voor aangepaste providers wordt een aangepaste bron verwijderd via DELETE-aanvragen. Een aangepaste provider *accepteert geen* JSON-aanvraaginstantie. Voor een DELETE-aanvraag gebruikt `x-ms-customproviders-requestpath` het eindpunt de koptekst om de reeds gemaakte bron te verwijderen.

Voeg de volgende **methode RemoveCustomResource** toe om bestaande bronnen te verwijderen:

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

In Azure volgen resources een RESTful-model. De url van de aanvraag waarmee een resource wordt gemaakt, verwijdert ook de bron als een delete-aanvraag wordt uitgevoerd.

### <a name="list-all-custom-resources"></a>Alle aangepaste bronnen weergeven

Voor aangepaste providers u een lijst met bestaande aangepaste bronnen opsommen met behulp van get-aanvragen voor verzamelingen. Een aangepaste provider *accepteert geen* JSON-aanvraaginstantie. Voor een verzameling GET-aanvragen gebruikt `x-ms-customproviders-requestpath` het eindpunt de koptekst om de reeds gemaakte resources op te sommen.

Voeg de volgende **methode EnumerateAllCustomResources toe** om de bestaande resources op te sommen:

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
> De RowKey QueryComparisons.GreaterThan en QueryComparisons.LessThan is de syntaxis van Azure Table-opslag om een 'beginmet'-query voor tekenreeksen uit te voeren.

Als u alle bestaande resources wilt weergeven, genereert u een Azure Table-opslagquery die ervoor zorgt dat de resources onder uw aangepaste providerpartitie bestaan. De query controleert vervolgens of de `{myResourceType}` rijsleutel met dezelfde waarde begint.

## <a name="integrate-restful-operations"></a>ResTful-bewerkingen integreren

Nadat alle RESTful-methoden aan de functie-app zijn toegevoegd, werkt u de belangrijkste **run-methode** bij die de functies aanroept om de verschillende REST-aanvragen te verwerken:

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

De bijgewerkte **methode Run** bevat nu de *tabelOpslaginvoerbinding* die u hebt toegevoegd voor Azure Table-opslag. Het eerste deel van `x-ms-customproviders-requestpath` de methode `Microsoft.Azure.Management.ResourceManager.Fluent` leest de koptekst en gebruikt de bibliotheek om de waarde als resource-id te ontleden. De `x-ms-customproviders-requestpath` koptekst wordt verzonden door de aangepaste provider en geeft het pad van de binnenkomende aanvraag aan.

Met behulp van de parsed resource ID u de **partitionKey-** en **rowKey-waarden** genereren voor de gegevens om aangepaste resources op te zoeken of op te slaan.

Nadat u de methoden en klassen hebt toegevoegd, moet u de **gebruiksmethoden** voor de functie-app bijwerken. Voeg de volgende code toe aan de bovenkant van het C#-bestand:

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

Als u op enig moment van deze zelfstudie verdwaalt, u het volledige codevoorbeeld vinden in de verwijzing naar het [aangepaste doelpunt van de provider C# RESTful.](./reference-custom-providers-csharp-endpoint.md) Nadat u de functie-app hebt voltooid, slaat u de URL van de functie-app op. Het kan worden gebruikt om de functie-app te activeren in latere tutorials.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een RESTful-eindpunt geschreven om te werken met een Azure Custom Provider-eindpunt. Ga voor meer informatie over het maken van een aangepaste provider naar het artikel [Zelfstudie: Een aangepaste provider maken.](./tutorial-custom-providers-create.md)
