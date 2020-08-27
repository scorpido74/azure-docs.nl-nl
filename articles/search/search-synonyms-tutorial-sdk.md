---
title: Synoniemen C#-voor beeld
titleSuffix: Azure Cognitive Search
description: In dit C#-voor beeld leert u hoe u de synoniemen functie kunt toevoegen aan een index in azure Cognitive Search. Een synoniemenkaart is een lijst met equivalente termen. Voor velden met ondersteuning voor synoniemen worden query's uitgebreid met de door de gebruiker opgegeven term en alle gerelateerde synoniemen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f27822ee6be26f955e4db52548da7fa2a22236c2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922749"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Voor beeld: synoniemen voor Azure-Cognitive Search in C toevoegen #

Met synoniemen breidt u een query uit door termen te gebruiken die semantisch overeenkomen met de ingevoerde term. Mogelijk wilt u bijvoorbeeld dat met 'auto' ook documenten worden geretourneerd met de termen 'voertuig' of 'cabrio'. 

In azure Cognitive Search worden synoniemen gedefinieerd in een *synoniemen toewijzing*via *toewijzings regels* die gelijkwaardige voor waarden koppelen. In dit voor beeld worden de essentiële stappen beschreven voor het toevoegen en gebruiken van synoniemen met een bestaande index. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Maak een synoniemen toewijzing met behulp van de  [SynonymMap](/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) -klasse. 
> * Stel de eigenschap [SynonymMaps](/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) in op velden die query-uitbrei ding moeten ondersteunen via synoniemen.

U kunt een veld met synoniemen uitvoeren zoals u dat normaal zou doen. Er is geen aanvullende query syntaxis vereist voor toegang tot synoniemen.

U kunt meerdere synoniementoewijzingen maken en deze als algemene serviceresource beschikbaar maken voor alle indexen. Daarna kunt u op veldniveau aangeven welke u wilt gebruiken. Op het moment dat een index wordt doorzocht, voert Azure Cognitive Search een zoek opdracht in een synoniemen kaart uit, als er een is opgegeven voor velden die worden gebruikt in de query.

> [!NOTE]
> Synoniemen kunnen worden gemaakt via een programma, maar niet in de portal. Als u graag Azure Portal-ondersteuning voor synoniemen wilt, kunt u hieronder feedback verzenden via [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Vereisten

Voor de zelfstudie gelden de volgende vereisten:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search-service](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET-bibliotheek](/dotnet/api/overview/azure/search)
* [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](./search-howto-dotnet-sdk.md)

## <a name="overview"></a>Overzicht

Met 'voor en na'-query's wordt de meerwaarde van synoniemen aangetoond. In dit voor beeld gebruikt u een voorbeeld toepassing om query's uit te voeren en resultaten te retour neren voor een voor beeld-index. De voorbeeldtoepassing maakt een kleine index met de naam 'hotels' en vult deze met twee documenten. De toepassing voert zoekquery's uit met termen en zinnen die niet voorkomen in de index. Hierna wordt de synoniemenfunctie ingeschakeld en worden dezelfde zoekopdrachten opnieuw uitgevoerd. In de onderstaande code wordt de algehele stroom gedemonstreerd.

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
De stappen voor het maken en vullen van de voor beeld-index worden uitgelegd in [het gebruik van Azure Cognitive Search van een .NET-toepassing](./search-howto-dotnet-sdk.md).

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
   Een synoniemtoewijzing moet voldoen aan de standaard-open-source-indeling `solr`. De indeling wordt uitgelegd in [synoniemen in Azure Cognitive Search](search-synonyms.md) onder de sectie `Apache Solr synonym format` .

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

Door synoniemen toe te voegen, wordt de zoekervaring volledig veranderd. In dit voor beeld zijn de oorspronkelijke query's niet in staat om zinvolle resultaten te retour neren, zelfs als de documenten in onze index relevant waren. Door synoniemen in te schakelen, breidt u de index zodanig uit dat deze ook termen bevat die regelmatig worden gebruikt, zonder dat daarbij wijzigingen worden aangebracht aan de onderliggende gegevens in de index.

## <a name="sample-application-source-code"></a>Broncode van een voorbeeldtoepassing
U vindt de volledige broncode van de voorbeeldtoepassing die in deze walkthrough wordt gebruikt, op [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om na een voor beeld op te schonen is door de resource groep te verwijderen die de Azure Cognitive Search-service bevat. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. In de portal bevindt de naam van de resource groep zich op de pagina overzicht van Azure Cognitive Search service.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld wordt de functie synoniemen in C# code getoond voor het maken en plaatsen van toewijzings regels en roept u vervolgens de synoniemen toewijzing aan in een query. Aanvullende informatie vindt u in de referentiedocumentatie voor de [.NET-SDK](/dotnet/api/microsoft.azure.search) en [REST-API](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Synoniemen gebruiken in azure Cognitive Search](search-synonyms.md)