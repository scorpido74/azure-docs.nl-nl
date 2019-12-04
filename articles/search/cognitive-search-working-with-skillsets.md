---
title: Vaardig heden en werk stroom van vakkennisset
titleSuffix: Azure Cognitive Search
description: Met vaardig heden kunt u een AI-verrijkings pijplijn ontwerpen in azure Cognitive Search. Meer informatie over belang rijke concepten en informatie over het samen stellen van vaardig heden.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 340e6d3feaf0265597a70229fd2658f009c01f64
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790895"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Concepten en samen stelling van vaardig heden in azure Cognitive Search

Dit artikel is voor ontwikkel aars die een beter begrip moeten hebben van de werking van de verrijkings pijplijn en ervan uitgaan dat er een conceptueel inzicht is in het proces van de AI-verrijking. Als u geen ervaring hebt met dit concept, begint u met:
+ [AI-verrijking in azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Kennis archief (preview-versie)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>De vaardig heden opgeven
Een vaardig heden is een herbruikbare resource in azure Cognitive Search waarmee een verzameling cognitieve vaardig heden wordt opgegeven die worden gebruikt voor het analyseren, transformeren en verrijken van de inhoud van tekst of afbeeldingen tijdens het indexeren. Door een vaardig heden te maken, kunt u tekst-en afbeeldings verrijkingen koppelen in de fase van gegevens opname, en nieuwe informatie en structuren uit onbewerkte inhoud ophalen en maken.

Een vaardighedenset heeft drie eigenschappen:

+   ```skills```, een niet-geordende verzameling vaardig heden waarvoor het platform de volg orde van de uitvoering bepaalt op basis van de vereiste invoer voor elke vaardigheid
+   ```cognitiveServices```, de cognitieve Services sleutel die is vereist voor de facturering van de cognitieve vaardig heden
+   ```knowledgeStore```, het opslag account waarin uw verrijkte documenten worden geprojecteerd



Vaardig heden zijn geschreven in JSON. U kunt complexe vaardig heden maken met lussen en [vertakkingen](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) met behulp van de [expressie taal](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). De expressie taal maakt gebruik van de notatie van het [JSON-wijzer](https://tools.ietf.org/html/rfc6901) pad met enkele wijzigingen om knoop punten in de verrijkings structuur te identificeren. Een ```"/"``` doorloopt een niveau lager in de structuur en ```"*"``` fungeert als een for-each-operator in de context. Deze concepten worden het beste beschreven met een voor beeld. Ter illustratie van een aantal concepten en mogelijkheden worden de voor beelden van de voor keuren voor de [Hotel beoordelingen](knowledge-store-connect-powerbi.md) door lopen. Als u de vaardig heden wilt bekijken nadat u de werk stroom gegevens importeren hebt gevolgd, moet u een REST API-client gebruiken om [de vaardig heden](https://docs.microsoft.com/rest/api/searchservice/get-skillset)op te halen.

### <a name="enrichment-tree"></a>Verrijkings structuur

Als u wilt bepalen hoe een vaardig heden uw document progressief verrijkt, gaan we beginnen met de manier waarop het document eruitziet voor elke verrijking. De uitvoer van het kraken van documenten is afhankelijk van de gegevens bron en de geselecteerde geparseerde modus. Dit is ook de status van het document waarvan de [veld Toewijzingen](search-indexer-field-mappings.md) bron inhoud kunnen hebben wanneer er gegevens aan de zoek index worden toegevoegd.
![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge-archief in pijplijn diagram ")

Zodra een document zich in de verrijkings pijplijn bevindt, wordt het weer gegeven als een structuur van inhoud en bijbehorende verrijkingen. Deze boom structuur wordt geïnstantieerd als de uitvoer van het kraken van documenten. De indeling van de verrijkings structuur maakt het mogelijk om meta gegevens te koppelen aan zelfs primitieve gegevens typen, maar dit is geen geldig JSON-object, maar kan worden geprojecteerd naar een geldige JSON-indeling. In de volgende tabel ziet u de status van een document dat in de verrijkings pijplijn wordt ingevoerd:

|Modus data Source\Parsing|Standaard|JSON, JSON-lijnen & CSV|
|---|---|---|
|Blobopslag|/document/content<br>/document/normalized_images/*<br>...|/document/{key1}<br>/document/{key2}<br>...|
|SQL|/document/{column1}<br>/document/{column2}<br>...|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>...|N/A|

 Wanneer vaardig heden worden uitgevoerd, voegen ze nieuwe knoop punten toe aan de verrijkings structuur. Deze nieuwe knoop punten kunnen vervolgens worden gebruikt als invoer voor downstream-vaardig heden, projecteren naar het kennis archief of toewijzing aan index velden. Verrijkingen zijn niet onveranderbaar: als u deze eenmaal hebt gemaakt, kunnen knoop punten niet worden bewerkt. Naarmate uw vaardig heden ingewik kelder wordt, is uw verrijkings structuur, maar niet alle knoop punten in de verrijkings structuur nodig om deze naar de index of het kennis archief te brengen. U kunt slechts een subset van de verrijkingen van de index of het kennis archief selectief blijven.

U kunt slechts een subset van de verrijkingen van de index of het kennis archief selectief blijven.
Voor de rest van dit document gaan we ervan uit dat we een [voor beeld van een hotel beoordeling](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)gebruiken, maar dezelfde concepten zijn van toepassing op het verrijken van documenten uit alle andere gegevens bronnen.

### <a name="context"></a>Context
Elke vaardigheid vereist een context. Een context bepaalt het volgende:
+   Het aantal keren dat de vaardigheid wordt uitgevoerd, op basis van de geselecteerde knoop punten. Bij context waarden van het type verzameling wordt een ```/*``` aan het einde toegevoegd, waardoor de vaardigheid één keer wordt aangeroepen voor elke instantie in de verzameling. 
+   In de verrijkings structuur worden de vaardigheids uitvoer toegevoegd. Uitvoer wordt altijd aan de structuur toegevoegd als onderliggende elementen van het context knooppunt. 
+   Vorm van de invoer. Voor verzamelingen op meerdere niveaus is het instellen van de context voor de bovenliggende verzameling van invloed op de vorm van de invoer van de vaardigheid. Als u bijvoorbeeld een verrijkings structuur hebt met een lijst met landen, wordt elk verrijkt met een lijst met statussen die een lijst met ZIPcodes bevatten.

|Context|Invoer|Vorm van invoer|Aanroepen van vaardig heden|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Een lijst met alle ZIPcodes in het land |Eenmaal per land |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Een lijst met ZIPcodes in de status | Eenmaal per combi natie van land en provincie|

### <a name="sourcecontext"></a>SourceContext

De `sourceContext` wordt alleen gebruikt voor invoer van vaardig heden en [projecties](knowledge-store-projection-overview.md). Dit wordt gebruikt om geneste multi-level-objecten te bouwen. Mogelijk moet u een nieuwe oject maken om deze te geven als invoer voor een vaardigheid of project in het kennis archief. Omdat verrijkings knooppunten mogelijk geen geldig JSON-object in de verrijkings structuur zijn en refrencing een knoop punt in de structuur retourneert alleen die status van het knoop punt wanneer het werd gemaakt, met behulp van de verrijkingen als vaardigheids invoer of projecties moet u een goed gevormd JSON-object maken. Met de `sourceContext` kunt u een hiërarchisch, anoniem type object maken dat meerdere vaardig heden zou moeten hebben als u de context alleen gebruikt. Het gebruik van `sourceContext` wordt weer gegeven in de volgende sectie. Bekijk de vaardigheids uitvoer die een verrijking heeft gegenereerd om te bepalen of het een geldig JSON-object is en niet een primitief type.

### <a name="projections"></a>Projecties

Projectie is het proces van het selecteren van de knoop punten uit de verrijkings structuur die in het kennis archief moeten worden opgeslagen. Projecties zijn aangepaste vormen van het document (inhoud en verrijkingen) dat kan worden uitgevoerd als tabel-of object projecties. Zie [werken met projecties](knowledge-store-projection-overview.md)voor meer informatie over het werken met projecties.

![Opties voor veld toewijzing](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opties voor veld toewijzing voor verrijkings pijplijn")

In het bovenstaande diagram wordt de selector beschreven waarmee u werkt, op basis van waar u zich bevindt in de verrijkings pijplijn.

## <a name="generate-enriched-data"></a>Verrijkte gegevens genereren 

Nu gaan we de vaardig heden van de Hotel beoordelingen door lopen, kunt u de [zelf studie](knowledge-store-connect-powerbi.md) volgen om de vaardig heden te maken of de vaardig heden [weer te geven](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) . We kijken naar:

* hoe de verrijkings structuur zich ontwikkelt met de uitvoering van elke vaardigheid 
* de manier waarop de context en invoer bepalen hoe vaak een vaardigheid wordt uitgevoerd 
* de vorm van de invoer op basis van de context. 

Omdat we de modus voor het parseren van tekst met scheidings tekens voor de Indexeer functie gebruiken, vertegenwoordigt een document binnen het verrijkings proces één rij in het CSV-bestand.

### <a name="skill-1-split-skill"></a>Vaardigheids #1: vaardigheid splitsen 

![verrijkings structuur na het kraken van documenten](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Verrijkings structuur na het kraken van documenten en voordat de kwalificatie wordt uitgevoerd")

Met de kwalificatie context van ```"/document/reviews_text"```wordt deze vaardigheid één keer uitgevoerd voor de `reviews_text`. De vaardigheids uitvoer is een lijst waarin de `reviews_text` wordt gesegmenteerd in 5000 teken segmenten. De uitvoer van de gesplitste vaardigheid heet `pages` en wordt toegevoegd aan de verrijkings structuur. Met de functie `targetName` kunt u de naam van een vaardigheids uitvoer wijzigen voordat u deze aan de verrijkings structuur toevoegt.

De verrijkings structuur heeft nu een nieuw knoop punt dat is geplaatst in de context van de vaardigheid. Dit knoop punt is beschikbaar voor elke vaardigheid, projectie of uitvoer veld toewijzing.


Het hoofd knooppunt voor alle verrijkingen is `"/document"`. Bij het werken met Blob-Indexeer functies heeft het knoop punt `"/document"` onderliggende knoop punten van `"/document/content"` en `"/document/normalized_images"`. Als we in dit voor beeld werken met CSV-gegevens, worden de kolom namen toegewezen aan de knoop punten onder `"/document"`. Voor toegang tot een van de verrijkingen die aan een knoop punt worden toegevoegd door een vaardigheid, is het volledige pad voor de verrijking nodig. Als u de tekst van het ```pages``` knoop punt als invoer voor een andere vaardigheid wilt gebruiken, moet u deze als ```"/document/reviews_text/pages/*"```opgeven.
 
 ![verrijkings structuur na vaardigheid #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Verrijkings structuur nadat Kwalificatie #1 uitgevoerd")

### <a name="skill-2-language-detection"></a>Taal detectie van vaardigheids #2
 Hoewel de vaardigheid van taal detectie de derde vaardigheid (vaardigheids #3) is gedefinieerd in de vaardig heden, is dit de volgende vaardigheid die moet worden uitgevoerd. Omdat het niet wordt geblokkeerd door invoer te vereisen, wordt deze parallel uitgevoerd met de vorige vaardigheid. Net als de gesplitste vaardigheid die erop wordt toegepast, wordt de vaardigheid van de taal detectie ook één keer voor elk document aangeroepen. De verrijkings structuur heeft nu een nieuw knoop punt voor taal.
 ![verrijkings structuur na vaardigheid #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Enrijkere structuur nadat Kwalificatie #2 uitgevoerd.)
 
 ### <a name="skill-3-key-phrases-skill"></a>Vaardig heden #3: vaardigheid van sleutel zinnen 

Gezien de context van ```/document/reviews_text/pages/*``` wordt de vaardigheid van de sleutel frase één keer geactiveerd voor elk van de items in de `pages` verzameling. De uitvoer van de vaardigheid is een knoop punt onder het bijbehorende pagina-element. 

 Nu kunt u de rest van de vaardig heden in de vakkennisset bekijken en visualiseren hoe de structuur van verrijkingen blijft groeien met de uitvoering van elke vaardigheid. Sommige vaardig heden, zoals het samen voegen van vaardig heden en de shaper-vaardigheid, maken ook nieuwe knoop punten, maar gebruiken alleen gegevens van bestaande knoop punten en maken geen nieuwe verrijkingen.

![verrijkings structuur na alle vaardig heden](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Verrijkings structuur na alle vaardig heden")

De kleuren van de connectors in de bovenstaande structuur geven aan dat de verrijkingen zijn gemaakt door verschillende vaardig heden en dat de knoop punten afzonderlijk moeten worden behandeld en geen deel uitmaken van het object dat wordt geretourneerd bij het selecteren van het bovenliggende knoop punt.

## <a name="save-enrichments-in-a-knowledge-store"></a>Verrijkingen opslaan in een kennis archief 

Vaardig heden definieert ook een kennis archief waar uw verrijkte documenten kunnen worden geprojecteerd als tabellen of objecten. Als u uw verrijkte gegevens in het kennis archief wilt opslaan, definieert u een aantal projecties van uw verrijkte document. Zie [Knowledge Store Overview](knowledge-store-concept-intro.md) (Engelstalig) voor meer informatie over het kennis archief

### <a name="slicing-projections"></a>Segmenteer projecties

Bij het definiëren van een tabel projectie groep kan een enkel knoop punt in de verrijkings structuur worden gesegmenteerd in meerdere gerelateerde tabellen. Als u een tabel met een bronpad toevoegt die een onderliggend element van een bestaande tabel projectie is, is het resulterende onderliggende knoop punt geen onderliggend element van de bestaande tabel projectie, maar wordt er in plaats daarvan geprojecteerd in de nieuwe, gerelateerde tabel. Met deze segmenting techniek kunt u één knoop punt in een shaper-vaardigheid definiëren dat de bron voor alle tabel projecties kan zijn. 

### <a name="shaping-projections"></a>Vorm projectie

Er zijn twee manieren om een projectie te definiëren. U kunt een shaper-vaardigheid gebruiken om een nieuw knoop punt te maken dat het hoofd knooppunt is voor alle verrijkingen die u projecteren. In uw projecties zou u alleen verwijzen naar de uitvoer van de shaper-vaardigheid. U kunt ook de vorm van een projectie binnen de projectie definitie zelf inline.

De shaper-benadering is uitgebreider dan in line vorm, maar zorgt ervoor dat alle mutaties van de verrijkings structuur in de vaardig heden zijn opgenomen en dat de uitvoer een object is dat opnieuw kan worden gebruikt. Met inline-vormgeving kunt u de vorm maken die u nodig hebt, maar dit is een anoniem object dat alleen beschikbaar is voor de projectie waarvoor het is gedefinieerd. De benaderingen kunnen samen of afzonderlijk worden gebruikt. De vaardig heden die u voor u hebt gemaakt in de werk stroom van de portal bevat beide. Er wordt gebruikgemaakt van een shaper-vaardigheid voor de tabel projecties, maar maakt ook gebruik van inline-vorm om de tabel met sleutel zinnen te projecteren.

Om het voor beeld uit te breiden, kunt u ervoor kiezen om de inline-vorm te verwijderen en een shaper-vaardigheid te gebruiken om een nieuw knoop punt voor de sleutel zinnen te maken. Als u een vorm wilt maken die in drie tabellen is geprojecteerd, namelijk `hotelReviewsDocument`, `hotelReviewsPages`en `hotelReviewsKeyPhrases`, worden de twee opties beschreven in de volgende secties.


#### <a name="shaper-skill-and-projection"></a>Shaper vaardig heden en projectie 

> [!Note]
> Enkele van de kolommen uit de document tabel zijn uit dit voor beeld verwijderd voor de boog.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Met het knoop punt `tableprojection` dat is gedefinieerd in de sectie `outputs` hierboven, kunnen we nu de segmenting-functie gebruiken om delen van het `tableprojection` knooppunt te projecteren in verschillende tabellen:

> [!Note]
> Dit is slechts een fragment van de projectie binnen de configuratie van het kennis archief.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inline-projectie prognoses

Voor de inline-vorm benadering is geen shaper-vaardigheid vereist, omdat alle shapes die nodig zijn voor de projecties, worden gemaakt op het moment dat ze nodig zijn. Als u dezelfde gegevens wilt projecteren als in het vorige voor beeld, ziet de optie inline-projectie er als volgt uit:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Een waarneming van beide benaderingen is hoe waarden van `"Keyphrases"` worden geprojecteerd met behulp van de `"sourceContext"`. Het knoop punt `"Keyphrases"`, dat een verzameling teken reeksen bevat, is zelf een onderliggend element van de pagina tekst. Omdat projecties echter een JSON-object vereisen en de pagina een primitieve (teken reeks) is, wordt de `"sourceContext"` gebruikt om de sleutel woorden in een object met een benoemde eigenschap op te slaan. Met deze techniek kunnen zelfs primitieven worden geprojecteerd.

## <a name="next-steps"></a>Volgende stappen

Als volgende stap maakt u uw eerste vaardig heden met cognitieve vaardig heden.

> [!div class="nextstepaction"]
> [Maak uw eerste vaardig heden](cognitive-search-defining-skillset.md)-set.
