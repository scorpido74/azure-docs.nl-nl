---
title: Skillset concepten en workflow
titleSuffix: Azure Cognitive Search
description: Skillsets zijn waar u een AI-verrijkingspijplijn maakt in Azure Cognitive Search. Leer belangrijke concepten en details over de samenstelling van vaardigheden.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191030"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Skillset-concepten en -compositie in Azure Cognitive Search

Dit artikel is voor ontwikkelaars die een dieper begrip nodig hebben van hoe de verrijkingspijplijn werkt en ervan uitgaat dat u een conceptueel begrip hebt van het AI-verrijkingsproces. Als u nieuw bent dit concept, beginnen met:
+ [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Knowledge Store (preview)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>De skillset opgeven
Een skillset is een herbruikbare bron in Azure Cognitive Search die een verzameling cognitieve vaardigheden opgeeft die worden gebruikt voor het analyseren, transformeren en verrijken van tekst- of afbeeldingsinhoud tijdens het indexeren. Met het maken van een skillset u tekst- en afbeeldingsverrijkingen toevoegen in de gegevensopnamefase, waarbij nieuwe informatie en structuren uit ruwe inhoud worden geëxtraheerd en gemaakt.

Een skillset heeft drie eigenschappen:

+   ```skills```, een ongeordende verzameling van vaardigheden waarvoor het platform de volgorde van uitvoering bepaalt op basis van de input die nodig is voor elke vaardigheid
+   ```cognitiveServices```, de cognitieve servicessleutel die nodig is voor het factureren van de aangeroepen cognitieve vaardigheden
+   ```knowledgeStore```, het opslagaccount waar uw verrijkte documenten worden geprojecteerd



Skillsets zijn geschreven in JSON. U complexe skillsets maken met lussen en [vertakken](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) met behulp van de [expressietaal.](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) De expressietaal gebruikt de [JSON](https://tools.ietf.org/html/rfc6901) Pointer-padnotatie met een paar wijzigingen om knooppunten in de verrijkingsstructuur te identificeren. Een ```"/"``` doorkruist een niveau lager ```"*"``` in de boom en fungeert als een voor-elke operator in de context. Deze concepten kunnen het best worden beschreven met een voorbeeld. Om enkele concepten en mogelijkheden te illustreren, doorlopen we de voorbeeldvaardigheden van de [hotelrecensies.](knowledge-store-connect-powerbi.md) Als u de skillset wilt weergeven nadat u de werkstroom voor importgegevens hebt gevolgd, moet u een REST API-client gebruiken om [de skillset](https://docs.microsoft.com/rest/api/searchservice/get-skillset)te krijgen.

### <a name="enrichment-tree"></a>Verrijkingsboom

Als u wilt bedenken hoe een skillset uw document geleidelijk verrijkt, beginnen we met hoe het document eruit ziet voordat een verrijking wordt. De uitvoer van het kraken van documenten is afhankelijk van de gegevensbron en de geselecteerde specifieke parsingmodus. Dit is ook de status van het document waarvan de [veldtoewijzingen](search-indexer-field-mappings.md) inhoud kunnen bron van wanneer gegevens worden toegevoegd aan de zoekindex.
![Kennisarchief in pijplijndiagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Kennisarchief in pijplijndiagram")

Zodra een document zich in de verrijkingspijplijn bevindt, wordt het weergegeven als een structuur met inhoud en bijbehorende verrijkingen. Deze boom wordt geinstantieerd als de uitvoer van documentkraken. Met de verrijkingsstructuurkan de verrijkingspijplijn metagegevens koppelen aan zelfs primitieve gegevenstypen, het is geen geldig JSON-object, maar kan worden geprojecteerd in een geldige JSON-indeling. In de volgende tabel ziet u de status van een document dat de verrijkingspijplijn binnengaat:

|Gegevensbron\Parsing-modus|Standaard|JSON, JSON Lines & CSV|
|---|---|---|
|Blob Storage|/document/inhoud<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{kolom1}<br>/document/{kolom2}<br>…|N.v.t. |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N.v.t.|

 Terwijl vaardigheden worden uitgevoerd, voegen ze nieuwe knooppunten toe aan de verrijkingsstructuur. Deze nieuwe knooppunten kunnen vervolgens worden gebruikt als ingangen voor downstreamvaardigheden, projecteren naar de kenniswinkel of in kaart brengen om velden te indexeren. Verrijkingen zijn niet mutable: eenmaal gemaakt, kunnen knooppunten niet worden bewerkt. Naarmate uw skillsets complexer worden, zal uw verrijkingsboom dat ook doen, maar niet alle knooppunten in de verrijkingsboom moeten de index of de kenniswinkel halen. 

U selectief slechts een subset van de verrijkingen aan de index of de kennisopslag voortduren.
Voor de rest van dit document gaan we ervan uit dat we werken met [voorbeeld van hotelbeoordelingen,](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)maar dezelfde concepten zijn van toepassing op verrijkende documenten uit alle andere gegevensbronnen.

### <a name="context"></a>Context
Elke vaardigheid vereist een context. Een context bepaalt:
+   Het aantal keren dat de vaardigheid wordt uitgevoerd, op basis van de geselecteerde knooppunten. Voor contextwaarden van typeverzameling ```/*``` resulteert het toevoegen van een aan het einde van de vaardigheid ertoe dat de vaardigheid eenmaal wordt aangeroepen voor elke instantie in de verzameling. 
+   Waar in de verrijkingsboom de vaardigheidsoutputs worden toegevoegd. Uitgangen worden altijd toegevoegd aan de boom als kinderen van het contextknooppunt. 
+   Vorm van de ingangen. Voor verzamelingen op meerdere niveaus heeft het instellen van de context op de bovenliggende verzameling invloed op de vorm van de invoer voor de vaardigheid. Bijvoorbeeld als u een verrijkingsboom hebt met een lijst met landen, elk verrijkt met een lijst met staten met een lijst met postcodes.

|Context|Invoer|Vorm van invoer|Skill Aanroep|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Een lijst van alle postcodes in het land |Eén keer per land |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Een lijst met postcodes in de staat | Eenmaal per combinatie van land en staat|

### <a name="sourcecontext"></a>SourceContext

Het `sourceContext` wordt alleen gebruikt in vaardigheid [ingangen](knowledge-store-projection-overview.md)en projecties . Het wordt gebruikt voor het bouwen van multi-level, geneste objecten. Mogelijk moet u een nieuw object maken om het als input voor een vaardigheid of project door te geven aan de kenniswinkel. Aangezien verrijkingsknooppunten mogelijk geen geldig JSON-object in de verrijkingsstructuur zijn en een knooppunt in de boom verwijst, wordt die status van het knooppunt alleen geretourneerd wanneer deze is gemaakt, waarbij u de verrijkingen als vaardigheidsingangen of projecties gebruikt, dat u een goed gevormd JSON-object maakt. Hiermee `sourceContext` u een hiërarchisch, anoniem tekstobject maken, waarvoor meerdere vaardigheden nodig zijn als u alleen de context gebruikt. Gebruiken `sourceContext` wordt weergegeven in de volgende sectie. Kijk naar de vaardigheidsuitvoer die een verrijking heeft gegenereerd om te bepalen of het een geldig JSON-object is en geen primitief type.

### <a name="projections"></a>Projecties

Projectie is het proces van het selecteren van de knooppunten uit de verrijkingboom die in de kennisopslag moet worden opgeslagen. Projecties zijn aangepaste vormen van het document (inhoud en verrijkingen) die kunnen worden uitgevoerd als tabel- of objectprojecties. Zie [werken met projecties](knowledge-store-projection-overview.md)voor meer informatie over het werken met projecties.

![Opties voor veldtoewijzing](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opties voor veldtoewijzing voor verrijkingspijplijn")

Het bovenstaande diagram beschrijft de kiezer waarmee u werkt op basis van waar u zich in de verrijkingspijplijn bevindt.

## <a name="generate-enriched-data"></a>Verrijkte gegevens genereren 

Laten we nu eenstap door de vaardigheden van hotelbeoordelingen doorlopen, je de [zelfstudie](knowledge-store-connect-powerbi.md) volgen om de vaardigheden te maken of de vaardigheden [te bekijken.](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) We gaan kijken naar:

* hoe de verrijkingsboom evolueert met de uitvoering van elke vaardigheid 
* hoe de context en ingangen werken om te bepalen hoe vaak een vaardigheid wordt uitgevoerd 
* wat de vorm van de invoer is gebaseerd op de context. 

Aangezien we de afgebakende tekstparsing-modus voor de indexer gebruiken, vertegenwoordigt een document binnen het verrijkingsproces één rij in het CSV-bestand.

### <a name="skill-1-split-skill"></a>Vaardigheid #1: Split skill 

![verrijkingsboom na stuk kraken](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "De boom van de verrijking na document het barsten en vóór vaardigheidsuitvoering")

Met de vaardigheid ```"/document/reviews_text"```context van , zal `reviews_text`deze vaardigheid eenmaal uit te voeren voor de . De vaardigheidsuitvoer is een `reviews_text` lijst waarbij de segmenten in 5000 tekensegmenten zijn verdeeld. De uitvoer van de `pages` gesplitste vaardigheid wordt benoemd en toegevoegd aan de verrijkingsstructuur. Met `targetName` de functie u de naam van een vaardigheidsuitvoer wijzigen voordat u aan de verrijkingsstructuur wordt toegevoegd.

De verrijkingboom heeft nu een nieuw knooppunt geplaatst onder de context van de vaardigheid. Dit knooppunt is beschikbaar voor elke vaardigheid, projectie of toewijzing van uitvoervelden.


Het wortelknooppunt voor alle `"/document"`verrijkingen is . Wanneer u met blob-indexers werkt, heeft het `"/document"` knooppunt onderliggende knooppunten van `"/document/content"` en `"/document/normalized_images"`. Wanneer u met CSV-gegevens werkt, zoals in dit voorbeeld, `"/document"`worden de kolomnamen toegewezen aan knooppunten onder . Om toegang te krijgen tot een van de verrijkingen die door een vaardigheid aan een knooppunt zijn toegevoegd, is het volledige pad voor de verrijking nodig. Als u bijvoorbeeld de tekst van ```pages``` het knooppunt wilt gebruiken als invoer voor een ```"/document/reviews_text/pages/*"```andere vaardigheid, moet u deze opgeven als .
 
 ![verrijking boom na vaardigheid #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Verrijkingsboom na vaardigheid #1 uitvoert")

### <a name="skill-2-language-detection"></a>Detectie van vaardigheid #2 taal
 Hoewel de taaldetectievaardigheid de derde (vaardigheid #3) vaardigheid is die in de skillset is gedefinieerd, is het de volgende vaardigheid die moet worden uitgevoerd. Aangezien het niet wordt geblokkeerd door het vereisen van input, zal het parallel met de vorige vaardigheid uitvoeren. Net als de gesplitste vaardigheid die eraan voorafging, wordt de taaldetectievaardigheid ook één keer voor elk document aangeroepen. De verrijkingsboom heeft nu een nieuw knooppunt voor taal.
 ![verrijking boom na vaardigheid #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Verrijkingsboom na vaardigheid die #2 uitvoert")
 
 ### <a name="skill-3-key-phrases-skill"></a>Vaardigheid #3: Vaardigheden vaardigheid 

Gezien de ```/document/reviews_text/pages/*``` context van de belangrijkste zinnen vaardigheid zal eenmaal worden `pages` aangeroepen voor elk van de items in de collectie. De uitvoer van de vaardigheid is een knooppunt onder het bijbehorende pagina-element. 

 Je moet nu in staat zijn om te kijken naar de rest van de vaardigheden in de skillset en visualiseren hoe de boom van verrijkingen zal blijven groeien met de uitvoering van elke vaardigheid. Sommige vaardigheden, zoals de samenvoegvaardigheid en de shaper-vaardigheid, maken ook nieuwe knooppunten, maar gebruiken alleen gegevens van bestaande knooppunten en maken geen netto nieuwe verrijkingen.

![verrijking boom na alle vaardigheden](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Verrijking boom na alle vaardigheden")

De kleuren van de connectors in de structuur hierboven geven aan dat de verrijkingen zijn gemaakt door verschillende vaardigheden en dat de knooppunten afzonderlijk moeten worden aangepakt en geen deel uitmaken van het object dat wordt geretourneerd bij het selecteren van het bovenliggende knooppunt.

## <a name="save-enrichments-in-a-knowledge-store"></a>Bewaar verrijkingen in een kenniswinkel 

Skillsets definiëren ook een kennisarchief waar uw verrijkte documenten kunnen worden geprojecteerd als tabellen of objecten. Als u uw verrijkte gegevens wilt opslaan in de kenniswinkel, definieert u een reeks projecties voor uw verrijkte document. Zie het overzicht van de kenniswinkel voor meer informatie over de [kenniswinkel](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projecties snijden

Bij het definiëren van een tabelprojectiegroep kan één knooppunt in de verrijkingsstructuur in meerdere gerelateerde tabellen worden gesneden. Als u een tabel toevoegt met een bronpad dat een onderliggend kind is van een bestaande tabelprojectie, is het resulterende onderliggende knooppunt geen onderliggend kind van de bestaande tabelprojectie, maar wordt het in plaats daarvan geprojecteerd in de nieuwe, verwante tabel. Met deze snijtechniek u één knooppunt definiëren in een shaper-vaardigheid die de bron kan zijn voor al uw tabelprojecties. 

### <a name="shaping-projections"></a>Projecties vormgeven

Er zijn twee manieren om een projectie te definiëren. U een shaper-vaardigheid gebruiken om een nieuw knooppunt te maken dat het hoofdknooppunt is voor alle verrijkingen die u projecteert. Vervolgens, in uw projecties, zou je alleen verwijzen naar de output van de shaper vaardigheid. U ook een projectie binnen de projectiedefinitie zelf vormgeven.

De shaper benadering is meer verbose dan inline vormgeving, maar zorgt ervoor dat alle mutaties van de verrijking boom zijn opgenomen in de vaardigheden en dat de output is een object dat kan worden hergebruikt. Met Inline shaping u de vorm maken die u nodig hebt, maar is een anoniem object en is alleen beschikbaar voor de projectie waarvoor deze is gedefinieerd. De benaderingen kunnen samen of afzonderlijk worden gebruikt. De skillset die voor u is gemaakt in de portalworkflow bevat beide. Het maakt gebruik van een shaper vaardigheid voor de tabel projecties, maar maakt ook gebruik van inline vormgeving om de tabel met sleutelzinnen project.

Als u het voorbeeld wilt uitbreiden, u ervoor kiezen om de inline vormgeving te verwijderen en een shaper-vaardigheid te gebruiken om een nieuw knooppunt voor de sleutelzinnen te maken. Om een vorm te maken die `hotelReviewsDocument` `hotelReviewsPages`in `hotelReviewsKeyPhrases`drie tabellen wordt geprojecteerd, namelijk, , en , worden de twee opties beschreven in de volgende secties.


#### <a name="shaper-skill-and-projection"></a>Shaper vaardigheid en projectie 

> [!Note]
> Sommige kolommen uit de documenttabel zijn uit dit voorbeeld verwijderd voor beknoptheid.
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

Met `tableprojection` het knooppunt dat `outputs` in de bovenstaande sectie is gedefinieerd, kunnen we `tableprojection` de snijfunctie nu gebruiken om delen van het knooppunt in verschillende tabellen te projecteren:

> [!Note]
> Dit is slechts een fragment van de projectie binnen de configuratie van de kenniswinkel.
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

#### <a name="inline-shaping-projections"></a>Inline vormgevende projecties

De inline shaping benadering vereist geen shaper vaardigheid als alle vormen die nodig zijn voor de projecties worden gemaakt op het moment dat ze nodig zijn. Als u dezelfde gegevens wilt projecteren als in het vorige voorbeeld, ziet de inline projectieoptie er als volgt uit:

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
  
Een opmerking van beide benaderingen `"Keyphrases"` is hoe `"sourceContext"`waarden van worden geprojecteerd met behulp van de . Het `"Keyphrases"` knooppunt, dat een verzameling tekenreeksen bevat, is zelf een kind van de paginatekst. Omdat projecties echter een JSON-object vereisen en de pagina `"sourceContext"` een primitief (tekenreeks) is, wordt de toetswoord gebruikt om de sleutelzin in een object met een benoemde eigenschap te wikkelen. Deze techniek maakt het mogelijk om zelfs primitieven onafhankelijk te laten geprojecteerd.

## <a name="next-steps"></a>Volgende stappen

Als volgende stap, maak je eerste vaardigheden met cognitieve vaardigheden.

> [!div class="nextstepaction"]
> [Maak je eerste skillset.](cognitive-search-defining-skillset.md)
