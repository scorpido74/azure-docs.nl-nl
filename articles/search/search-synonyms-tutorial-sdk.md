---
title: Voorbeeld van synoniemen C#
titleSuffix: Azure Cognitive Search
description: In dit c#-voorbeeld leert u hoe u de functie synoniemen toevoegt aan een index in Azure Cognitive Search. Een synoniemenkaart is een lijst met equivalente termen. Voor velden met ondersteuning voor synoniemen worden query's uitgebreid met de door de gebruiker opgegeven term en alle gerelateerde synoniemen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794237"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Voorbeeld: synoniemen toevoegen voor Azure Cognitive Search in C #

Met synoniemen breidt u een query uit door termen te gebruiken die semantisch overeenkomen met de ingevoerde term. Mogelijk wilt u bijvoorbeeld dat met 'auto' ook documenten worden geretourneerd met de termen 'voertuig' of 'cabrio'. 

In Azure Cognitive Search worden synoniemen gedefinieerd in een *synoniemenkaart,* door middel *van toewijzingsregels* die gelijkwaardige termen associëren. In dit voorbeeld worden essentiële stappen voor het toevoegen en gebruiken van synoniemen met een bestaande index behandeld. Procedures voor:

> [!div class="checklist"]
> * Maak een synoniemenkaart met de klasse [SynonymMap.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) 
> * Stel de eigenschap [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) in op velden die query-uitbreiding via synoniemen moeten ondersteunen.

U een veld met synoniemen opvragen zoals u dat normaal zou doen. Er is geen extra querysyntaxis vereist om toegang te krijgen tot synoniemen.

U kunt meerdere synoniementoewijzingen maken en deze als algemene serviceresource beschikbaar maken voor alle indexen. Daarna kunt u op veldniveau aangeven welke u wilt gebruiken. Op querytijd doet Azure Cognitive Search niet alleen een index zoeken in een synoniemenkaart, als er een is opgegeven voor velden die in de query worden gebruikt.

> [!NOTE]
> Synoniemen kunnen programmatisch worden gemaakt, maar niet in de portal. Als u graag Azure Portal-ondersteuning voor synoniemen wilt, kunt u hieronder feedback verzenden via [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Vereisten

Voor de zelfstudie gelden de volgende vereisten:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search-service](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-bibliotheek](https://aka.ms/search-sdk)
* [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Overzicht

Met 'voor en na'-query's wordt de meerwaarde van synoniemen aangetoond. Gebruik in dit voorbeeld een voorbeeldtoepassing die query's uitvoert en resultaten retourneert op een voorbeeldindex. De voorbeeldtoepassing maakt een kleine index met de naam 'hotels' en vult deze met twee documenten. De toepassing voert zoekquery's uit met termen en zinnen die niet voorkomen in de index. Hierna wordt de synoniemenfunctie ingeschakeld en worden dezelfde zoekopdrachten opnieuw uitgevoerd. In de onderstaande code wordt de algehele stroom gedemonstreerd.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
De stappen voor het maken en invullen van de voorbeeldindex worden uitgelegd in [Het gebruik van Azure Cognitive Search vanuit een .NET-toepassing](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>'Voor'-query's

In `RunQueriesWithNonExistentTermsInIndex` voert u zoekquery's uit met 'vijfsterren', 'internet' en 'voordelig EN hotel'.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Geen van de twee geïndexeerde documenten bevat deze termen, dus wordt de volgende uitvoer gegenereerd met de eerste `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Synoniemen inschakelen

U kunt synoniemen op basis van twee stappen inschakelen. Als eerste definieert en uploadt u synoniemregels en daarna configureert u velden voor het gebruik van synoniemen. Het proces wordt beschreven in `UploadSynonyms` en `EnableSynonymsInHotelsIndex`.

1. Voeg een synoniemtoewijzing toe aan de Search-service. In `UploadSynonyms` worden vier regels gedefinieerd voor de synoniemtoewijzing desc-synonymmap, die vervolgens naar de service worden geüpload.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Een synoniemtoewijzing moet voldoen aan de standaard-open-source-indeling `solr`. De indeling wordt uitgelegd in [Synoniemen in](search-synonyms.md) `Apache Solr synonym format`Azure Cognitive Search onder de sectie .

2. Configureer doorzoekbare velden voor het gebruik van de synoniemtoewijzing in de indexdefinitie. In `EnableSynonymsInHotelsIndex` worden synoniemen ingeschakeld voor twee velden (`category` en `tags`) door de eigenschap `synonymMaps` in te stellen op de naam van de zojuist geüploade synoniemtoewijzing.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Wanneer u een synoniemtoewijzing toevoegt, is het niet nodig om indexen opnieuw op te bouwen. U kunt een synoniemtoewijzing toevoegen aan uw service en daarna de bestaande velddefinities aanpassen in een index om de nieuwe synoniemtoewijzing te gebruiken. Het toevoegen van nieuwe kenmerken heeft geen invloed op de beschikbaarheid van indexen. Ditzelfde is van toepassing op het uitschakelen van synoniemen voor een veld. U kunt eenvoudigweg de eigenschap `synonymMaps` instellen op een lege lijst.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>'Na'-query's

Wanneer de synoniemtoewijzing is geüpload en de index is bijgewerkt voor gebruik van de synoniemtoewijzing, wordt met de tweede `RunQueriesWithNonExistentTermsInIndex`-aanroep de volgende uitvoer gegenereerd:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Met de eerste query wordt het document opgehaald uit de regel `five star=>luxury`. Met de tweede query wordt de zoekopdracht uitgebreid met `internet,wifi` en met de derde met zowel `hotel, motel` als `economy,inexpensive=>budget` bij het zoeken naar de overeenkomende documenten.

Door synoniemen toe te voegen, wordt de zoekervaring volledig veranderd. In dit voorbeeld hebben de oorspronkelijke query's geen zinvolle resultaten opgeleverd, ook al waren de documenten in onze index relevant. Door synoniemen in te schakelen, breidt u de index zodanig uit dat deze ook termen bevat die regelmatig worden gebruikt, zonder dat daarbij wijzigingen worden aangebracht aan de onderliggende gegevens in de index.

## <a name="sample-application-source-code"></a>Broncode van een voorbeeldtoepassing
U vindt de volledige broncode van de voorbeeldtoepassing die in deze walkthrough wordt gebruikt, op [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om na een voorbeeld op te schonen, is door de brongroep met de Azure Cognitive Search-service te verwijderen. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. In de portal staat de naam van de brongroep op de pagina Overzicht van de Azure Cognitive Search-service.

## <a name="next-steps"></a>Volgende stappen

In dit voorbeeld wordt de synoniemenfunctie in C#-code getoond om regels te maken en na toewijzing te maken en vervolgens de synoniemenkaart op een query aan te roepen. Aanvullende informatie vindt u in de referentiedocumentatie voor de [.NET-SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) en [REST-API](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Synoniemen gebruiken in Azure Cognitive Search](search-synonyms.md)
