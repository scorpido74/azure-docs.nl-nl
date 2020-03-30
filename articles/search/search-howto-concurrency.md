---
title: Gelijktijdigschrijven beheren voor resources
titleSuffix: Azure Cognitive Search
description: Gebruik optimistische gelijktijdigheid om botsingen in de lucht op updates of verwijderingen naar Azure Cognitive Search-indexen, indexeerders, gegevensbronnen te voorkomen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792221"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Gelijktijdigheid beheren in Azure Cognitive Search

Wanneer u Azure Cognitive Search-bronnen zoals indexen en gegevensbronnen beheert, is het belangrijk om resources veilig bij te werken, vooral als resources gelijktijdig worden benaderd door verschillende onderdelen van uw toepassing. Wanneer twee clients tegelijkertijd een resource bijwerken zonder coördinatie, zijn racevoorwaarden mogelijk. Om dit te voorkomen biedt Azure Cognitive Search een *optimistisch gelijktijdigheidsmodel.* Er zijn geen sloten op een bron. In plaats daarvan is er een ETag voor elke resource die de resourceversie identificeert, zodat u aanvragen maken die per ongeluk overschrijven voorkomen.

> [!Tip]
> Conceptuele code in een [voorbeeld C#-oplossing](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) legt uit hoe gelijktijdigheidscontrole werkt in Azure Cognitive Search. De code maakt voorwaarden die een beroep doen op gelijktijdigheidscontrole. Het lezen van de [onderstaande code fragment](#samplecode) is waarschijnlijk voldoende voor de meeste ontwikkelaars, maar als je wilt uitvoeren, bewerken appsettings.json om de service naam en een admin api-key toe te voegen. Gezien een service-URL van `http://myservice.search.windows.net` `myservice`, de service naam is .

## <a name="how-it-works"></a>Hoe werkt het?

Optimistische gelijktijdigheid wordt geïmplementeerd door middel van toegangscontroles van toegangscondities in API-aanroepen die schrijven naar indexen, indexers, gegevensbronnen en synoniemMapbronnen.

Alle resources hebben een [*entiteitstag (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) die informatie over objectversie biedt. Door eerst de ETag te controleren, u gelijktijdige updates in een typische werkstroom vermijden (lokaal downloaden, wijzigen) door ervoor te zorgen dat de ETag van de bron overeenkomt met uw lokale kopie.

+ De REST API maakt gebruik van een [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) op de aanvraagheader.
+ De .NET SDK stelt de ETag in via een accessCondition-object en stelt de [If-Match in | Als-Match-None header](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) op de resource. Elk object dat overneemt van [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) heeft een accessCondition-object.

Elke keer dat u een bron bijwerkt, wordt de ETag automatisch gewijzigd. Wanneer u gelijktijdigheidsbeheer implementeert, stelt u alleen een voorwaarde voor het updateverzoek waarvoor de externe bron dezelfde ETag moet hebben als de kopie van de bron die u op de client hebt gewijzigd. Als een gelijktijdig proces de externe bron al heeft gewijzigd, komt de ETag niet overeen met de voorwaarde en mislukt de aanvraag met HTTP 412. Als u de .NET SDK gebruikt, wordt `CloudException` dit `IsAccessConditionFailed()` gemanifesteerd als een waarbij de extensiemethode true retourneert.

> [!Note]
> Er is maar één mechanisme voor gelijktijdigheid. Het wordt altijd gebruikt, ongeacht welke API wordt gebruikt voor resource-updates.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Use cases en voorbeeldcode

De volgende code toont accessCondition-controles voor belangrijke updatebewerkingen:

+ Een update mislukken als de bron niet meer bestaat
+ Een update mislukken als de bronversie verandert

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Voorbeeldcode van [het DotNetETagsExplainer-programma](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Ontwerppatroon

Een ontwerppatroon voor het implementeren van optimistische gelijktijdigheid moet een lus bevatten waarin de toegangscontrole voor de toegangsvoorwaarde, een test voor de toegangsvoorwaarde, wordt gewijzigd en optioneel een bijgewerkte bron wordt opgehaald voordat wordt geprobeerd de wijzigingen opnieuw toe te passen.

Dit codefragment illustreert de toevoeging van een synoniemMap aan een index die al bestaat. Deze code is afkomstig uit het [voorbeeld Synoniem C# voor Azure Cognitive Search](search-synonyms-tutorial-sdk.md).

Het fragment krijgt de "hotels" index, controleert de objectversie op een updatebewerking, gooit een uitzondering als de voorwaarde mislukt en probeert vervolgens de bewerking opnieuw (tot drie keer), te beginnen met het ophalen van de index van de server om de nieuwste versie te krijgen.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Volgende stappen

Bekijk het [voorbeeld van synoniemen C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) voor meer context over het veilig bijwerken van een bestaande index.

Probeer een van de volgende voorbeelden te wijzigen om ETags- of AccessCondition-objecten op te nemen.

+ [REST API-voorbeeld op GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-voorbeeld op GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Deze oplossing omvat het project "DotNetEtagsExplainer" met de code in dit artikel.

## <a name="see-also"></a>Zie ook

[Algemene HTTP-aanvraag- en antwoordkoppen](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-statuscodes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Indexbewerkingen (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
