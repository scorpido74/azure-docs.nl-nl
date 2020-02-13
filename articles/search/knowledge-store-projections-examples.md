---
title: Projecties definiëren in een kennis archief
titleSuffix: Azure Cognitive Search
description: Voor beelden van algemene patronen voor het projecteren van verrijkte documenten in het kennis Archief voor gebruik met Power BI of Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165512"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Kennis archief-projecties: verrijkingen vorm geven en exporteren naar het kennis archief

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

Prognoses zijn de fysieke expressie van verrijkte documenten in een kennis archief. Een effectief gebruik van uw verrijkte documenten vereist structuur. In dit artikel vindt u meer informatie over de structuur en relaties, leert u hoe u de projectie-eigenschappen kunt samen stellen, en hoe u gegevens kunt relateren in verschillende projectie typen die u maakt. 

Als u een projectie wilt maken, moet u de gegevens vorm geven met behulp van een shaper-vaardigheid om een aangepast object te maken of de syntaxis van de inline-vorm te gebruiken. Een gegevensshape bevat alle gegevens die u van plan bent te projecteren. In dit document vindt u een voor beeld van elke optie. u kunt kiezen uit een van de opties voor projecties die u maakt.


Er zijn drie soorten projecties:
+ Tabellen
+ Objecten
+ Bestanden

Tabel prognoses worden opgeslagen in azure-tabel opslag. Object-en bestands projecties worden geschreven naar Blob Storage, object projecties worden opgeslagen als JSON-bestanden en bevatten inhoud uit het document en eventuele vaardigheids uitvoer of verrijkingen. De verrijkings pijplijn kan ook binaire bestanden zoals afbeeldingen extra heren, maar deze binaire bestanden worden geprojecteerd als bestands projecties. Wanneer een binair object wordt geprojecteerd als een object projectie, worden alleen de meta gegevens die eraan zijn gekoppeld, opgeslagen als een JSON-blob. 

Om inzicht te krijgen in het snij punt tussen data shaping en projecties, gebruiken we de volgende vaardig heden als basis voor het verkennen van verschillende configuraties. Deze vaardig heden verwerkt onbewerkte afbeeldings-en tekst inhoud. Prognoses worden gedefinieerd op basis van de inhoud van het document en de uitvoer van de vaardig heden, voor de scenario's die we willen ondersteunen.

U kunt ook een REST API-voor [beeld](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) met alle aanroepen in dit overzicht downloaden en gebruiken.

> [!IMPORTANT] 
> Bij het experimenteren met projecties is het handig om [de eigenschap van de Indexeer functie](search-howto-incremental-index.md) in te stellen om kosten beheer te garanderen. Als u projecties bewerkt, wordt het hele document opnieuw verrijkt als de Indexeer functie cache niet is ingesteld. Als de cache is ingesteld en alleen de projecties zijn bijgewerkt, worden de vaardig heden-uitvoeringen voor eerder verrijkte documenten niet in rekening gebracht met Cognitive Services kosten.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

We kunnen nu het `knowledgeStore`-object toevoegen en de projecties voor elk van de scenario's configureren, indien nodig. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projecteren op tabellen voor scenario's zoals Power BI

> [!NOTE] 
> Omdat het kennis archief een Azure Storage account is, zijn tabel projecties Azure Storage tabellen en gelden de opslag limieten voor tabellen. Zie [limieten voor tabel opslag](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)voor meer informatie. Het is handig om te weten dat de grootte van de entiteit niet groter mag zijn dan 1 MB en dat één eigenschap niet groter mag zijn dan 64 KB. Deze beperkingen maken tabellen een goede oplossing voor het opslaan van een groot aantal kleine entiteiten.

Power BI kunnen tabellen lezen en relaties detecteren op basis van de sleutels die de projecties van het kennis archief maken, waardoor tabellen een goede optie hebben om gegevens te projecteren wanneer u een dash board op basis van uw verrijkte gegevens probeert te maken. Ervan uitgaande dat we een dash board proberen te maken waarin we de belangrijkste woord groepen kunnen visualiseren die zijn geëxtraheerd uit documenten als een Word-Cloud, kunnen we een shaper-vaardigheid toevoegen aan de vaardig heden om een aangepaste vorm te maken die de Documentspecifieke Details en sleutel zinnen heeft. Voeg de shaper-vaardigheid toe aan de vaardig heden om een nieuwe verrijking te maken met de naam ```pbiShape``` op de ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Een aangepaste vorm maken met behulp van een shaper-vaardigheid

Een aangepaste vorm maken die u in tabel opslag kunt projecteren. Zonder aangepaste vorm kan een projectie alleen verwijzen naar één knoop punt (één projectie per uitvoer). Door een aangepaste vorm te maken, kunt u verschillende elementen samen voegen in een nieuwe logische geheel die kan worden geprojecteerd als één tabel of worden gesegmenteerd en gedistribueerd in een verzameling tabellen. In dit voor beeld worden meta gegevens en geïdentificeerde entiteiten en sleutel zinnen gecombineerd met de aangepaste vorm. Het object wordt pbiShape genoemd en is onder `/document`. 

> [!IMPORTANT] 
> Bron paden voor verrijkingen zijn vereist voor goed gevormde JSON-objecten voordat ze kunnen worden geprojecteerd. De verrijkings structuur kan verrijkingen vertegenwoordigen die geen goed gevormde JSON zijn, bijvoorbeeld wanneer een verrijking is geparenteerd naar een primitve zoals een teken reeks. Houd er rekening mee dat `KeyPhrases` en `Entities` zijn verpakt in een geldig JSON-object met de `sourceContext`. Dit is vereist als `keyphrases` en `entities` zijn verrijkingen op primitieven en moeten worden geconverteerd naar een geldige JSON voordat ze kunnen worden geprojecteerd.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Voeg de shaper-vaardigheid toe die u zojuist hebt gedefinieerd voor de lijst met vaardig heden in de vaardig heden. 

Nu we alle gegevens hebben die nodig zijn om tabellen te projecteren, werkt u het knowledgeStore-object bij met de tabel definities. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Stel de eigenschap ```storageConnectionString``` in op een geldig v2-opslag account voor algemeen gebruik connection string. In dit scenario definiëren we drie tabellen in het projectie object door de eigenschappen ```tableName```, ```source``` en ```generatedKeyName``` in te stellen. U kunt de vaardig heden nu bijwerken door de PUT-aanvraag uit te geven.

### <a name="slicing"></a>Segmenterings 

Wanneer u begint met een geconsolideerde shape waarbij alle inhoud die moet worden geprojecteerd, zich in één vorm (of een verrijkend knoop punt) bevindt, kunt u met segmentering één knoop punt in meerdere tabellen of objecten segmenteren. Hier wordt het ```pbiShape```-object gesegmenteerd in meerdere tabellen. Met de functie voor het segmenteren kunt u delen van de vorm, ```keyPhrases``` en ```Entities``` uit verschillende tabellen halen. Dit is handig als er meerdere entiteiten en zinsdelen aan elk document zijn gekoppeld. Met de impliciete segmentering wordt een relatie tussen de bovenliggende en onderliggende tabellen gegenereerd, waarbij de ```generatedKeyName``` in de bovenliggende tabel wordt gebruikt om een kolom met dezelfde naam in de onderliggende tabel te maken. 

### <a name="naming-relationships"></a>Namen van relaties
De eigenschappen ```generatedKeyName``` en ```referenceKeyName``` worden gebruikt voor het koppelen van gegevens in tabellen of zelfs in projectie typen. Elke rij in de onderliggende tabel/projectie heeft een eigenschap die terugkeert naar het bovenliggende item. De naam van de kolom of eigenschap in het onderliggende element is het ```referenceKeyName``` van het bovenliggende item. Wanneer de ```referenceKeyName``` niet is ingesteld, wordt deze door de service standaard ingesteld op de ```generatedKeyName``` van het bovenliggende item. Power bi is afhankelijk van deze gegenereerde sleutels om relaties binnen de tabellen te ontdekken. Als u de kolom in de onderliggende tabel anders wilt hebben, stelt u de eigenschap ```referenceKeyName``` van de bovenliggende tabel in. Een voor beeld hiervan is het instellen van de ```generatedKeyName``` als ID in de tabel pbiDocument en de ```referenceKeyName``` als DocumentID. Dit resulteert in de kolom in de pbiEntities-en pbiKeyPhrases-tabellen met de document-id met de naam DocumentID.

Sla de bijgewerkte vaardig heden op en voer de Indexeer functie uit. u hebt nu een werkende projectie met drie tabellen. Als u deze tabellen in Power BI importeert, moet u ervoor zorgen dat de relaties automatisch worden gedetecteerd Power BI.

## <a name="projecting-to-objects"></a>Projecteren op objecten

Object projecties hebben niet dezelfde beperkingen als tabel projecties en zijn beter geschikt voor het projecteren van grote documenten. In dit voor beeld projecteert u het hele document naar een object projectie. Object projecties zijn beperkt tot één projectie in een container.
Als u een object projectie wilt definiëren, zullen we de ```objects``` matrix in de projecties gebruiken. U kunt een nieuwe vorm genereren met behulp van de shaper-vaardigheid of de inline-vorm van de object projectie gebruiken. In het voor beeld van tabellen is de benadering van het maken van een vorm en het segmenteren gedemonstreerd, maar in dit voor beeld wordt het gebruik van inline-vorm gegeven. Inline-vormgeving is de mogelijkheid om een nieuwe vorm te maken in de definitie van de invoer voor een projectie. Inline-vormgeving maakt een anoniem object dat identiek is aan wat een vergelijk bare shaper zou opleveren. Inline-vormgeving is handig als u een vorm definieert die u niet wilt hergebruiken.
De eigenschap projecties is een matrix, voor dit voor beeld wordt er een nieuw projectie-exemplaar aan de matrix toegevoegd. De knowledgeStore-definitie bijwerken met de projecties gedefinieerde inline, u hebt geen shaper-vaardigheid nodig bij het gebruik van inline-projecties.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Bestands projecties

Bestands projecties zijn installatie kopieën die zijn geëxtraheerd uit het bron document of uitvoer van verrijkingen die uit het verrijkings proces kunnen worden geprojecteerd. Bestands projecties, vergelijkbaar met object projecties, worden geïmplementeerd als blobs en bevatten de installatie kopie. Als u een bestands projectie wilt genereren, gebruiken we de ```files``` matrix in het projectie object. In dit voor beeld worden alle afbeeldingen uit het document geëxtraheerd naar een container met de naam `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projecteren op meerdere typen

Voor een complexere scenario moet u mogelijk inhoud in verschillende projectie typen projecteren. Als u bijvoorbeeld een aantal gegevens zoals sleutel zinnen en entiteiten naar tabellen wilt projecteren, slaat u de OCR-resultaten van tekst en indeling tekst op als objecten en projecteer u de afbeeldingen als bestanden. Deze update voor de vaardig heden gaat als volgt:

1. Maak een tabel met een rij voor elk document.
2. Maak een tabel met betrekking tot de document tabel waarbij elke sleutel zin is geïdentificeerd als een rij in deze tabel.
3. Maak een tabel met betrekking tot de document tabel waarbij elke entiteit is geïdentificeerd als een rij in deze tabel.
4. Maak een object projectie met de indelings tekst voor elke afbeelding.
5. Een bestands projectie maken, waarbij elke uitgepakte afbeelding wordt geprojecteerd.
6. Maak een kruis verwijzings tabel die verwijzingen bevat naar de document tabel, object projectie met de indelings tekst en de bestands projectie.

Begin door een nieuwe shaper-vaardigheid toe te voegen aan de kwalificatie matrix waarmee een object vorm wordt gemaakt. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Voor object projecties is een container naam vereist voor elke projectie, object projecties of bestands projecties kunnen geen container delen. 

### <a name="relationships"></a>Relaties

Dit voor beeld markeert ook een andere functie van projecties door het definiëren van meerdere typen projecties binnen hetzelfde projectie object, maar er is een relatie die binnen en tussen de verschillende typen (tabellen, objecten, bestanden) van projecties wordt weer gegeven, zodat u begint met een tabelrij voor een document en zoekt alle OCR-tekst voor de afbeeldingen in dat document in de object projectie. Als u niet wilt dat de gegevens zijn gerelateerd, definieert u de projecties in verschillende projectie objecten, bijvoorbeeld het volgende fragment resulteert in de tabellen die aan elkaar zijn gerelateerd, maar er zijn geen relaties tussen de tabellen en de OCR-tekst prognoses. Projectie groepen zijn handig wanneer u dezelfde gegevens in verschillende vormen wilt projecteren voor verschillende behoeften. Bijvoorbeeld een projectie groep voor het Power BI dash board en een andere projectie groep voor het gebruik van de gegevens om een AI-model voor een vaardigheid te trainen.
Wanneer u projecties van verschillende typen bouwt, worden de projecties van het bestand en de objecten eerst gegenereerd en worden de paden aan de tabellen toegevoegd.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

In deze voor beelden wordt gebruikgemaakt van de algemene patronen voor het gebruik van projecties. u moet nu ook een goed idee hebben van de concepten over het opbouwen van een projectie voor uw specifieke scenario.

## <a name="common-issues"></a>Algemene problemen

Bij het definiëren van een projectie zijn er enkele veelvoorkomende problemen die kunnen leiden tot onverwachte resultaten.

1. Geen teken reeks verrijkingen. Wanneer teken reeksen worden uitgebreid, bijvoorbeeld ```merged_content``` verrijkt met sleutel zinnen, wordt de verrijkte eigenschap weer gegeven als een onderliggend element van merged_content binnen de verrijkings structuur. Maar op de projectie moet deze worden omgezet naar een geldig JSON-object met een naam en een waarde.
2. De ```/*``` aan het einde van een bronpad weglaten. Als bijvoorbeeld de bron van een projectie is ```/document/pbiShape/keyPhrases``` wordt de matrix met sleutel zinnen geprojecteerd als één object/rij. Als u het bronpad instelt op ```/document/pbiShape/keyPhrases/*```, wordt er een enkele rij of een object voor elk van de sleutel zinnen bepaald.
3. Pad-selecters zijn hoofdletter gevoelig en kunnen leiden tot ontbrekende invoer waarschuwingen als u niet de exacte Case voor de selector gebruikt.

