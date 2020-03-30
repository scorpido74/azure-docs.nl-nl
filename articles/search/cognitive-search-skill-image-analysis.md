---
title: Beeldanalyse cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Extraer semantische tekst door middel van beeldanalyse met behulp van de cognitieve vaardigheid Image Analysis in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6972e2f7ea219a1c8c8dbabbf9fe12a8fa59e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369480"
---
# <a name="image-analysis-cognitive-skill"></a>Beeldanalyse cognitieve vaardigheid

De **vaardigheid Image Analysis** haalt een uitgebreide set visuele functies uit op basis van de afbeeldingsinhoud. U bijvoorbeeld een bijschrift genereren uit een afbeelding, tags genereren of beroemdheden en oriëntatiepunten identificeren. Deze vaardigheid maakt gebruik van de machine learning modellen die door [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Cognitive Services. 

> [!NOTE]
> Kleine volumes (minder dan 20 transacties) kunnen gratis worden uitgevoerd in Azure Cognitive Search, maar grotere workloads vereisen [het koppelen van een factureerbare cognitive services-bron.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| standaardLanguageCode   |  Een tekenreeks die aangeeft welke taal moet terugkeren. De herkenningsresultaten van de service worden geretourneerd in een bepaalde taal. Als deze parameter niet is opgegeven, is de standaardwaarde 'nl'. <br/><br/>Ondersteunde talen zijn: <br/>*nl* - Engels (standaard) <br/> *es* - Spaans <br/> *ja* - Japans <br/> *pt* - Portugees <br/> *zh* - Vereenvoudigd Chinees|
| visualFeatures |  Een array met tekenreeksen die aangeven welke visuele functietypen moeten terugkeren. Geldige typen visuele functies zijn:  <ul><li>*volwassen* - detecteert of het beeld pornografisch van aard is (toont naaktheid of een seksuele daad), of is bloederig (toont extreem geweld of bloed). Seksueel suggestieve inhoud (ook bekend als pikante inhoud) wordt ook gedetecteerd.</li><li>*merken* - detecteert verschillende merken binnen een afbeelding, inclusief de geschatte locatie. De *merken* visuele functie is alleen beschikbaar in het Engels.</li><li> *categorieën* - categoriseert beeldinhoud op basis van een taxonomie gedefinieerd in de Cognitive Services [Computer Vision documentatie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy). </li><li>*description* - beschrijft de inhoud van de afbeelding met een volledige zin in ondersteunde talen.</li><li>*gezichten* - detecteert of er gezichten aanwezig zijn. Indien aanwezig, genereert coördinaten, geslacht en leeftijd.</li><li> *objecten* - detecteert verschillende objecten in een afbeelding, inclusief de geschatte locatie. De visuele functie *voor objecten* is alleen beschikbaar in het Engels.</li><li> *tags* - tags de afbeelding met een gedetailleerde lijst van woorden met betrekking tot de inhoud van de afbeelding.</li></ul> Namen van visuele kenmerken zijn hoofdlettergevoelig. Houd er rekening mee dat de visuele functies *kleur* en *imageType* zijn afgeschaft, maar deze functionaliteit kan nog steeds worden geopend via een [aangepaste vaardigheid.](https://go.microsoft.com/fwlink/?linkid=2121117)|
| Details   | Een array met tekenreeksen die aangeven welke domeinspecifieke details moeten worden retournerd. Geldige typen visuele functies zijn: <ul><li>*beroemdheden* - identificeert beroemdheden als gedetecteerd in het beeld.</li><li>*oriëntatiepunten* - identificeert oriëntatiepunten als deze in de afbeelding worden gedetecteerd. </li></ul> |

## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                                          |
|---------------|------------------------------------------------------|
| installatiekopie         | Complex Type. Werkt momenteel alleen met het veld '/document/normalized_images', ```imageAction``` geproduceerd door de Azure ```none```Blob-indexer wanneer deze is ingesteld op een andere waarde dan . Zie het [voorbeeld](#sample-output) voor meer informatie.|



##  <a name="sample-skill-definition"></a>Voorbeeldvaardigheidsdefinitie

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Voorbeeldindex (alleen voor de categorieën, beschrijvingen, gezichten en tagsvelden)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Veldtoewijzing van voorbeelduitvoervelden (voor de bovenstaande index)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variatie op toewijzingen van uitvoervelden (geneste eigenschappen)

U uitvoerveldtoewijzingen definiëren voor eigenschappen op een lager niveau, zoals alleen oriëntatiepunten of beroemdheden. Zorg er in dit geval voor dat uw indexschema een veld heeft om specifiek oriëntatiepunten te bevatten.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Foutgevallen
In de volgende foutgevallen worden geen elementen geëxtraheerd.

| Foutcode | Beschrijving |
|------------|-------------|
| Niet ondersteunde taal | De verstrekte taal wordt niet ondersteund. |
| Ongeldig beeldurl | De URL van de afbeelding is slecht geformatteerd of niet toegankelijk.|
| Ongeldige afbeeldingsopmaak | Invoergegevens zijn geen geldige afbeelding. |
| Ongeldigeafbeeldingsgrootte | De invoerafbeelding is te groot. |
| NotSupportedVisualFeature NotSupportedVisualFeature NotSupportedVisualFeature NotSupport  | Opgegeven functietype is niet geldig. |
| NotSupportedImage NotSupportedImage | Niet-ondersteunde afbeelding, bijvoorbeeld kinderpornografie. |
| OngeldigDetails | Niet-ondersteund domeinspecifiek model. |

Als u de fout `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`vergelijkbaar met, controleer het pad. Zowel beroemdheden als bezienswaardigheden `detail`zijn eigenschappen onder .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Indexeerfunctie maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
