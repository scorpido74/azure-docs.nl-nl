---
title: Werken met projecties in een kennis archief (preview)-Azure Search
description: Uw verrijkte gegevens van de AI-indexerings pijplijn opslaan en vorm geven voor gebruik in andere scenario's dan zoeken
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265189"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Werken met projecties in een Knowledge Store in Azure Search

> [!Note]
> Het kennis archief is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

Azure Search maakt inhouds verrijking mogelijk door middel van AI cognitieve vaardig heden en aangepaste vaardig heden als onderdeel van het indexeren. Verrijkingen voegen structuur toe aan uw documenten en maakt effectiever zoeken. In veel gevallen zijn de verrijkte documenten handig voor andere scenario's dan zoeken, zoals voor kennis analyse.

Projecties, een onderdeel van het [kennis archief](knowledge-store-concept-intro.md), zijn weer gaven van verrijkte documenten die kunnen worden opgeslagen in fysieke opslag voor kennis analyse. Met een projectie kunt u uw gegevens projecteren in een vorm die wordt afgestemd op uw behoeften, waardoor de relaties behouden blijven, zodat de hulpprogram ma's zoals Power BI de gegevens kunnen lezen zonder extra inspanning. 

Projecties kunnen in tabel vorm worden opgeslagen, met gegevens die in rijen en kolommen in azure Table-opslag zijn bewaard, of JSON-objecten die zijn opgeslagen in Azure Blob Storage. U kunt meerdere projecties van uw gegevens definiëren, terwijl deze worden uitgebreid. Dit is handig als u wilt dat dezelfde gegevens anders worden geshaped voor afzonderlijke gebruiks gevallen. 

Het kennis archief ondersteunt twee soorten projecties:

+ **Tabellen**: Voor gegevens die het beste als rijen en kolommen worden weer gegeven, kunt u met tabel prognoses een geschematiseerde vorm of projectie definiëren in tabel opslag. 

+ **Objecten**: Wanneer u een JSON-weer gave van uw gegevens en verrijkingen nodig hebt, worden object projecties opgeslagen als blobs.

Als u de prognoses wilt weer geven die in de context zijn gedefinieerd, kunt u stapsgewijs aan de [slag met kennis opslag](knowledge-store-howto.md).

## <a name="projection-groups"></a>Projectie groepen

In sommige gevallen moet u uw verrijkte gegevens in verschillende vormen projecteren om te voldoen aan verschillende doel stellingen. In het kennis archief kunt u meerdere groepen projecties definiëren. Projectie groepen hebben de volgende belang rijke kenmerken van wederzijds exclusiviteit en gerelateerde relatie.

### <a name="mutually-exclusivity"></a>Wederzijds exclusiviteit

Alle inhoud die in één groep is geprojecteerd, is onafhankelijk van de gegevens die in andere projectie groepen zijn geprojecteerd. Dit betekent dat u dezelfde gegevens anders hebt geshaped en nog steeds in elke projectie groep hebt herhaald. 

Een beperking die in projectie groepen wordt afgedwongen, is de wederzijdse exclusiviteit van projectie typen met een projectie groep. U kunt tabel projecties of object projecties alleen definiëren binnen één groep. Als u zowel tabellen als objecten wilt, definieert u één projectie groep voor tabellen en een tweede projectie groep voor-objecten.

### <a name="relatedness"></a>Gerelateerd aan

Alle inhoud die is geprojecteerd binnen één projectie groep, behoudt de relaties in de gegevens. Relaties zijn gebaseerd op een gegenereerde sleutel en elk onderliggend knoop punt behoudt een verwijzing naar het bovenliggende knoop punt. Relaties zijn niet van het bereik van projectie groepen, en tabellen of objecten die zijn gemaakt in een projectie groep, hebben geen relatie met gegevens die in andere projectie groepen zijn gegenereerd.

## <a name="input-shaping"></a>Invoer vorming
Het ophalen van uw gegevens in de juiste vorm of structuur is essentieel voor een effectief gebruik, zijn IT-tabellen of-objecten. De mogelijkheid om uw gegevens te vorm geven of te structureren op basis van de manier waarop u toegang wilt krijgen en deze te gebruiken, is een belang rijke functie die wordt weer gegeven als de **shaper** -vaardigheid binnen de vaardig heden.  

Projecties zijn gemakkelijker te definiëren wanneer u een object in de verrijkings structuur hebt dat overeenkomt met het schema van de projectie. Met de bijgewerkte [shaper-vaardigheid](cognitive-search-skill-shaper.md) kunt u een object uit verschillende knoop punten van de verrijkings structuur samen stellen en dit onder een nieuw knoop punt bevinden. Met de **shaper** -vaardigheid kunt u complexe typen met geneste objecten definiëren.

Wanneer u een nieuwe vorm hebt gedefinieerd die alle elementen bevat die u wilt uitprojecteren, kunt u deze vorm nu gebruiken als de bron voor de projecties of als invoer voor een andere vaardigheid.

## <a name="table-projections"></a>Tabel prognoses

Omdat het importeren eenvoudiger wordt, raden we aan om tabel prognoses te maken voor het verkennen van gegevens met Power BI. Daarnaast kunnen tabel projecties het wijzigen van de kardinaliteit tussen de tabel relatie toestaan. 

U kunt één document in uw index in meerdere tabellen projecteren, waarbij de relaties behouden blijven. Bij het projecteren in meerdere tabellen wordt de volledige vorm geprojecteerd in elke tabel, tenzij een onderliggend knoop punt de bron is van een andere tabel binnen dezelfde groep.

### <a name="defining-a-table-projection"></a>Tabel projectie definiëren

Wanneer u een tabel projectie in het `knowledgeStore` element van uw vaardig heden definieert, begint u met het toewijzen van een knoop punt op de verrijkings structuur met de tabel bron. Dit knoop punt is doorgaans de uitvoer van een **shaper** -vaardigheid die u hebt toegevoegd aan de lijst met vaardig heden voor het produceren van een specifieke vorm die u moet projecteren in tabellen. Het knoop punt dat u hebt gekozen voor project, kan worden gesegmenteerd tot project in meerdere tabellen. De definitie van tabellen is een lijst met tabellen die u wilt projecteren. 

#### <a name="projection-slicing"></a>Projectie segmentering
Bij het definiëren van een tabel projectie groep kan een enkel knoop punt in de verrijkings structuur worden gesegmenteerd in meerdere gerelateerde tabellen. Het toevoegen van een tabel met een bronpad dat een onderliggend element is van een bestaande tabel projectie resulteert in het onderliggende knoop punt dat wordt afgesneden van het bovenliggende knoop punt en wordt geprojecteerd in de nieuwe, nog gerelateerde tabel. Hierdoor kunt u één knoop punt in een shaper-vaardigheid definiëren die de bron voor al uw tabel projecties kan zijn.

Voor elke tabel zijn drie eigenschappen vereist:

+ TableName De naam van de tabel in Azure Storage.

+ generatedKeyName: De kolom naam voor de sleutel waarmee deze rij uniek wordt geïdentificeerd.

+ Bron Het knoop punt uit de verrijkings structuur waaruit u uw verrijkingen opneemt. Dit is doorgaans de uitvoer van een shaper, maar het kan ook de uitvoer van een van de vaardig heden zijn.

Hier volgt een voor beeld van tabel prognoses.

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
Zoals gedemonstreerd in dit voor beeld, worden de sleutel zinnen en entiteiten gemodelleerd in verschillende tabellen en bevatten ze een verwijzing naar het bovenliggende item (MainTable) voor elke rij. 

De volgende afbeelding is een verwijzing naar de Caselaw-oefening in [hoe u aan de slag kunt gaan met kennis opslag](knowledge-store-howto.md). In een scenario waarbij een geval meerdere meningen heeft en elk advies wordt verrijkt door entiteiten te identificeren die erin zijn opgenomen, kunt u de projecties model leren zoals hier wordt weer gegeven.

![Entiteiten en relaties in tabellen](media/knowledge-store-projection-overview/TableRelationships.png "Model relaties in tabel prognoses")

## <a name="object-projections"></a>Object projecties

Object projecties zijn JSON-representaties van de verrijkings structuur die vanuit elk knoop punt kan worden gebrond. In veel gevallen kan dezelfde **shaper** -vaardigheid die een tabel projectie maakt, worden gebruikt voor het genereren van een object projectie. 

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Voor het genereren van een object projectie zijn enkele object-specifieke kenmerken vereist:

+ storageContainer: De container waar de objecten worden opgeslagen
+ Bron Het pad naar het knoop punt van de verrijkings structuur die de basis vormt van de projectie
+ Prestatie Een pad dat een unieke sleutel vertegenwoordigt voor het object dat moet worden opgeslagen. Het wordt gebruikt om de naam van de BLOB in de container te maken.

## <a name="projection-lifecycle"></a>Projectie levenscyclus

De projecties hebben een levens cyclus die is gekoppeld aan de bron gegevens in uw gegevens bron. Naarmate uw gegevens worden bijgewerkt en opnieuw geïndexeerd, worden uw projecties bijgewerkt met de resultaten van de verrijkingen die ervoor zorgen dat uw projecties uiteindelijk consistent zijn met de gegevens in uw gegevens bron. De projecties nemen het verwijderings beleid over dat u voor uw index hebt geconfigureerd. 

## <a name="using-projections"></a>Projecties gebruiken

Nadat de Indexeer functie is uitgevoerd, kunt u de geprojecteerde gegevens in de containers of tabellen die u hebt opgegeven door projecties lezen. 

Voor analyses is het verkennen van Power BI zo eenvoudig als het instellen van Azure Table-opslag als de gegevens bron. U kunt eenvoudig een set visualisaties maken op basis van de relaties in uw gegevens.

Als u echter de verrijkte gegevens in een Data Science-pijp lijn moet gebruiken, kunt u [de gegevens van blobs laden in een Panda data frame](../machine-learning/team-data-science-process/explore-data-blob.md).

Ten slotte, als u uw gegevens uit het kennis archief moet exporteren, Azure Data Factory over connectors beschikt voor het exporteren van de gegevens en deze in de door u gewenste data base. 

## <a name="next-steps"></a>Volgende stappen

Als volgende stap maakt u uw eerste kennis archief met behulp van voorbeeld gegevens en instructies.

> [!div class="nextstepaction"]
> [Een kennis archief maken](knowledge-store-howto.md).
