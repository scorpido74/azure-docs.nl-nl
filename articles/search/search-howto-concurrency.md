---
title: Gelijktijdige schrijf bewerkingen naar resources beheren-Azure Search
description: Gebruik optimistische gelijktijdigheid om te voor komen dat er bij het bijwerken of verwijderen van Azure Search indexen, Indexeer functies en gegevens bronnen Mid-Air botsingen worden vermeden.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 67f2dad016d3958dc10ba87e785d31694a1c94f5
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656725"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Gelijktijdigheid beheren in Azure Search

Bij het beheren van Azure Search resources, zoals indexen en gegevens bronnen, is het belang rijk om bronnen veilig bij te werken, met name als bronnen gelijktijdig worden geopend door verschillende onderdelen van uw toepassing. Wanneer twee clients gelijktijdig een resource bijwerken zonder coördinatie, zijn race voorwaarden mogelijk. Azure Search biedt een *optimistisch gelijktijdigheids model*om dit te voor komen. Er zijn geen vergren delingen op een resource. In plaats daarvan is er een ETag voor elke resource waarmee de resource versie wordt geïdentificeerd, zodat u aanvragen kunt bezorgen die onbedoelde overschrijvingen voor komen.

> [!Tip]
> Conceptuele code in een [voorbeeld C# oplossing](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) legt uit hoe gelijktijdigheids beheer werkt in azure Search. De code maakt voor waarden die gelijktijdigheids beheer aanroepen. Lees het [onderstaande code fragment](#samplecode) is waarschijnlijk voldoende voor de meeste ontwikkel aars, maar als u het wilt uitvoeren, bewerkt u appSettings. json om de service naam en een beheer-API-sleutel toe te voegen. Op basis van de service `http://myservice.search.windows.net`-URL van is `myservice`de service naam.

## <a name="how-it-works"></a>Hoe werkt het?

Optimistische gelijktijdigheid wordt geïmplementeerd via controle van toegangs voorwaarden in API-aanroepen die schrijven naar indexen, Indexeer functies, gegevens bronnen en synonymMap-resources.

Alle resources hebben een [*entity tag (ETAG)* ](https://en.wikipedia.org/wiki/HTTP_ETag) die informatie over de object versie bevat. Door de ETag eerst te controleren, kunt u gelijktijdige updates voor komen in een normale werk stroom (Get, Modify lokaal, update) door ervoor te zorgen dat de resource ETag overeenkomt met uw lokale kopie.

+ De REST API gebruikt een [ETAG](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) op de aanvraag header.
+ De .NET SDK stelt de ETag in via een accessCondition-object, waarbij de [if-match | If-match-geen-header](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) voor de resource. Elk object dat wordt overgenomen van [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) heeft een accessCondition-object.

Telkens wanneer u een resource bijwerkt, wordt de ETag automatisch gewijzigd. Wanneer u gelijktijdigheids beheer implementeert, hoeft u alleen maar een voor waarde voor de update aanvraag te plaatsen waarvoor de externe resource dezelfde ETag moet hebben als de kopie van de resource die u op de client hebt gewijzigd. Als een gelijktijdig proces de externe resource al heeft gewijzigd, komt de ETag niet overeen met de voor waarde en mislukt de aanvraag met HTTP 412. Als u de .NET SDK gebruikt, wordt dit manifest als een `CloudException` locatie waar de `IsAccessConditionFailed()` extensie methode True retourneert.

> [!Note]
> Er is slechts één mechanisme voor gelijktijdigheid. Deze wordt altijd gebruikt, ongeacht welke API wordt gebruikt voor resource-updates.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Use cases en voorbeeld code

De volgende code toont accessCondition controles op belang rijke update bewerkingen:

+ Een update uitvoeren als de bron niet meer bestaat
+ Een update mislukt als de bron versie wordt gewijzigd

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Voorbeeld code uit het [programma DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

## <a name="design-pattern"></a>Ontwerp patroon

Een ontwerp patroon voor de implementatie van optimistische gelijktijdigheid moet een lus bevatten die de controle van de toegangs voorwaarde opnieuw probeert, een test voor de toegangs voorwaarde en eventueel een bijgewerkte bron ophalen voordat de wijzigingen opnieuw moeten worden toegepast.

Dit code fragment illustreert de toevoeging van een synonymMap aan een index die al bestaat. Deze code is afkomstig uit [het C# synoniem voor beeld voor Azure Search](search-synonyms-tutorial-sdk.md).

Met het fragment wordt de index "Hotels" opgehaald, de object versie wordt gecontroleerd op een update bewerking, wordt een uitzonde ring gegenereerd als de voor waarde is mislukt en wordt de bewerking vervolgens opnieuw geprobeerd (Maxi maal drie keer), te beginnen met het ophalen van index van de server om de meest recente versie te verkrijgen.

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

Raadpleeg het voor [ C# beeld synoniemen](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) voor meer context informatie over het veilig bijwerken van een bestaande index.

Wijzig een van de volgende voor beelden om ETags-of AccessCondition-objecten op te nemen.

+ [REST API voor beeld op GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-voor beeld op github](https://github.com/Azure-Samples/search-dotnet-getting-started). Deze oplossing bevat het project ' DotNetEtagsExplainer ' met de code die in dit artikel wordt weer gegeven.

## <a name="see-also"></a>Zie ook

[Algemene HTTP-aanvraag en antwoord headers](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-status codes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[index bewerkingen (rest API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
