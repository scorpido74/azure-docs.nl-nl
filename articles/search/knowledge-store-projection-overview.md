---
title: Projecties in een kenniswinkel (preview)
titleSuffix: Azure Cognitive Search
description: Sla uw verrijkte gegevens uit de AI-verrijkingsindexeringspijplijn op en vorm deze vorm in een kennisarchief voor gebruik in andere scenario's dan zoeken in volledige tekst. Knowledge store is momenteel in openbare preview.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942973"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projecties in een kenniswinkel in Azure Cognitive Search

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

Azure Cognitive Search maakt contentverrijking mogelijk door ingebouwde cognitieve vaardigheden en aangepaste vaardigheden als onderdeel van indexering. Verrijkingen creëren nieuwe informatie waar voorheen geen bestond: het extraheren van informatie uit afbeeldingen, het detecteren van sentiment, sleutelzinnen en entiteiten uit tekst, om er maar een paar te noemen. Verrijkingen voegen ook structuur toe aan ongedifferentieerde tekst. Al deze processen resulteren in documenten die full text search effectiever maken. In veel gevallen zijn verrijkte documenten handig voor andere scenario's dan zoeken, zoals voor kennismining.

Projecties, een onderdeel van [kennisarchief,](knowledge-store-concept-intro.md)zijn weergaven van verrijkte documenten die kunnen worden opgeslagen in fysieke opslag voor kennismijnbouwdoeleinden. Met een projectie u uw gegevens projecteren in een vorm die aansluit bij uw behoeften, waarbij relaties behouden blijven zodat tools zoals Power BI de gegevens zonder extra inspanning kunnen lezen.

Projecties kunnen tabelvormig zijn, met gegevens die zijn opgeslagen in rijen en kolommen in Azure Table-opslag of JSON-objecten die zijn opgeslagen in Azure Blob-opslag. U meerdere projecties van uw gegevens definiëren terwijl deze worden verrijkt. Meerdere projecties zijn handig als u dezelfde gegevens anders wilt laten vormen voor afzonderlijke use cases.

De kenniswinkel ondersteunt drie soorten projecties:

+ **Tabellen:** Voor gegevens die het best kunnen worden weergegeven als rijen en kolommen, u met tabelprojecties een schematized shape of projectie definiëren in tabelopslag. Alleen geldige JSON-objecten kunnen worden geprojecteerd als tabellen, het verrijkte document kan knooppunten bevatten die geen JSON-objecten worden genoemd en maak bij het projecteren van deze objecten een geldig JSON-object met een shaper-vaardigheid of inline-vormgeving.

+ **Objecten:** Wanneer u een JSON-weergave van uw gegevens en verrijkingen nodig hebt, worden objectprojecties opgeslagen als blobs. Alleen geldige JSON-objecten kunnen worden geprojecteerd als objecten, het verrijkte document kan knooppunten bevatten die geen JSON-objecten worden genoemd en maak bij het projecteren van deze objecten een geldig JSON-object met een shaper-vaardigheid of inline-vormgeving.

+ **Bestanden:** Wanneer u de afbeeldingen die uit de documenten zijn gehaald, u met bestandsprojecties de genormaliseerde afbeeldingen opslaan in blobopslag.

Als u projecties wilt zien die in context zijn gedefinieerd, stapt u door [Een kennisarchief maken in REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Projectiegroepen

In sommige gevallen moet u uw verrijkte gegevens in verschillende vormen projecteren om aan verschillende doelstellingen te voldoen. Met de kenniswinkel u meerdere groepen projecties definiëren. Projectiegroepen hebben de volgende belangrijke kenmerken van wederzijdse exclusiviteit en verwantschap.

### <a name="mutual-exclusivity"></a>Wederzijdse exclusiviteit

Alle inhoud die in één groep wordt geprojecteerd, is onafhankelijk van gegevens die in andere projectiegroepen worden geprojecteerd.
Deze onafhankelijkheid houdt in dat u dezelfde gegevens anders hebben gevormd, maar toch in elke projectiegroep worden herhaald.

### <a name="relatedness"></a>Verwantschap

Met projectiegroepen u uw documenten nu projecteren op projectietypen, terwijl de relaties tussen projectietypen behouden blijven. Alle inhoud die binnen één projectiegroep wordt geprojecteerd, behoudt relaties binnen de gegevens tussen projectietypen. Binnen tabellen zijn relaties gebaseerd op een gegenereerde sleutel en elk onderliggend knooppunt behoudt een verwijzing naar het bovenliggende knooppunt. Voor verschillende typen (tabellen, objecten en bestanden) blijven relaties behouden wanneer één knooppunt wordt geprojecteerd op verschillende typen. Denk bijvoorbeeld aan een scenario waarin u een document hebt met afbeeldingen en tekst. U de tekst projecteren op tabellen of objecten en de afbeeldingen naar bestanden waar de tabellen of objecten een kolom/eigenschap hebben die de URL van het bestand bevat.

## <a name="input-shaping"></a>Invoervormgeving

Het verkrijgen van uw gegevens in de juiste vorm of structuur is de sleutel tot effectief gebruik, of het nu tabellen of objecten. De mogelijkheid om uw gegevens vorm te geven of te structureren op basis van hoe u deze wilt openen en gebruiken, is een belangrijke mogelijkheid die wordt blootgesteld als de **Shaper-vaardigheid** binnen de skillset.  

Projecties zijn gemakkelijker te definiëren wanneer u een object in de verrijkingsstructuur hebt dat overeenkomt met het schema van de projectie. Met de bijgewerkte [Shaper-vaardigheid](cognitive-search-skill-shaper.md) u een object samenstellen uit verschillende knooppunten van de verrijkingsstructuur en deze onder een nieuw knooppunt opmaken. Met **de Shaper-vaardigheid** u complexe typen definiëren met geneste objecten.

Wanneer u een nieuwe vorm hebt gedefinieerd die alle elementen bevat die u moet projecteren, u deze vorm nu gebruiken als bron voor uw projecties of als input voor een andere vaardigheid.

## <a name="projection-slicing"></a>Projectie snijden

Bij het definiëren van een projectiegroep kan één knooppunt in de verrijkingsstructuur worden gesneden in meerdere gerelateerde tabellen of objecten. Als u een projectie toevoegt met een bronpad dat een onderliggend kind van een bestaande projectie is, wordt het onderliggende knooppunt uit het bovenliggende knooppunt gesneden en geprojecteerd in de nieuwe nog verwante tabel of object. Met deze techniek u één knooppunt definiëren in een shaper-vaardigheid die de bron kan zijn voor al uw projecties.

## <a name="table-projections"></a>Tabelprojecties

Omdat het importeren eenvoudiger maakt, raden we tabelprojecties aan voor gegevensverkenning met Power BI. Bovendien maken tabelprojecties het mogelijk om de kardinaliteit tussen tabelrelaties te wijzigen. 

U één document in uw index projecteren in meerdere tabellen, waarbij de relaties behouden worden. Bij het projecteren naar meerdere tabellen wordt de volledige vorm in elke tabel geprojecteerd, tenzij een onderliggend knooppunt de bron is van een andere tabel binnen dezelfde groep.

### <a name="defining-a-table-projection"></a>Een tabelprojectie definiëren

Wanneer u een tabelprojectie `knowledgeStore` definieert in het element van uw vaardigheden, moet u eerst een knooppunt op de verrijkingsboom toewijzen aan de tabelbron. Dit knooppunt is meestal de uitvoer van een **Shaper-vaardigheid** die u hebt toegevoegd aan de lijst met vaardigheden om een specifieke vorm te produceren die u in tabellen moet projecteren. Het knooppunt dat u wilt projecteren, kan worden gesneden om in meerdere tabellen te projecteren. De definitie van tabellen is een lijst met tabellen die u wilt projecteren.

Elke tabel vereist drie eigenschappen:

+ tabelNaam: de naam van de tabel in Azure Storage.

+ generatedKeyName: de kolomnaam voor de sleutel die deze rij op unieke wijze identificeert.

+ bron: Het knooppunt van de verrijkingboom waar u uw verrijkingen vandaan haalt. Dit knooppunt is meestal de output van een shaper, maar kan de output van een van de vaardigheden.

Hier is een voorbeeld van tabelprojecties.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Zoals in dit voorbeeld wordt aangetoond, worden de sleutelzinnen en entiteiten gemodelleerd naar verschillende tabellen en bevatten ze een verwijzing naar de bovenliggende (MainTable) voor elke rij.

## <a name="object-projections"></a>Objectprojecties

Objectprojecties zijn JSON-weergaven van de verrijkingsboom die van elk knooppunt kan worden verkregen. In veel gevallen kan dezelfde **Shaper-vaardigheid** die een tabelprojectie maakt, worden gebruikt om een objectprojectie te genereren. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Het genereren van een objectprojectie vereist een paar objectspecifieke kenmerken:

+ storageContainer: de blobcontainer waar de objecten worden opgeslagen
+ bron: Het pad naar het knooppunt van de verrijkingsboom die de wortel van de projectie is

## <a name="file-projection"></a>Bestandsprojectie

Bestandsprojecties zijn vergelijkbaar met objectprojecties en `normalized_images` werken alleen op de verzameling. Net als bij objectprojecties worden bestandsprojecties opgeslagen in de blobcontainer met mapvoorvoegsel van de basis64-gecodeerde waarde van de document-id. Bestandsprojecties kunnen niet dezelfde container delen als objectprojecties en moeten in een andere container worden geprojecteerd.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Levenscyclus van projectie

Uw projecties hebben een levenscyclus die is gekoppeld aan de brongegevens in uw gegevensbron. Aangezien uw gegevens worden bijgewerkt en opnieuw worden geïndexeerd, worden uw projecties bijgewerkt met de resultaten van de verrijkingen, zodat uw projecties uiteindelijk consistent zijn met de gegevens in uw gegevensbron. De projecties erven het verwijderingsbeleid dat u voor uw index hebt geconfigureerd. Projecties worden niet verwijderd wanneer de indexer of de zoekservice zelf wordt verwijderd.

## <a name="using-projections"></a>Met behulp van projecties

Nadat de indexer is uitgevoerd, u de geprojecteerde gegevens in de containers of tabellen die u hebt opgegeven door middel van projecties lezen.

Voor analyses is verkennen in Power BI net zo eenvoudig als het instellen van Azure Table-opslag als de gegevensbron. U eenvoudig een set visualisaties op uw gegevens maken met behulp van de relaties binnen.

Als u de verrijkte gegevens in een data science-pijplijn moet gebruiken, u [de gegevens van blobs laden in een Pandas DataFrame.](../machine-learning/team-data-science-process/explore-data-blob.md)

Ten slotte, als u uw gegevens uit de kenniswinkel moet exporteren, heeft Azure Data Factory connectoren om de gegevens te exporteren en in de database van uw keuze te landen. 

## <a name="next-steps"></a>Volgende stappen

Als volgende stap maakt u uw eerste kennisarchief met behulp van voorbeeldgegevens en instructies.

> [!div class="nextstepaction"]
> [Maak een kenniswinkel in REST.](knowledge-store-create-rest.md)

Voor een zelfstudie over geavanceerde projecties concepten zoals snijden, inline vormgeving en relaties, beginnen met [projecties definiëren in een kennis op te slaan](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Projecties definiëren in een kenniswinkel](knowledge-store-projections-examples.md)
