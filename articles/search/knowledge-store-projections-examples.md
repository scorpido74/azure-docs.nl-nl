---
title: Projecties definiëren in een kennis archief
titleSuffix: Azure Cognitive Search
description: Voor beelden van algemene patronen voor het projecteren van verrijkte documenten in het kennis Archief voor gebruik met Power BI of Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: daaedf346bed78a93e0762a37687b623d25ef753
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441966"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Kennis archief-projecties: verrijkingen vorm geven en exporteren

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

Prognoses zijn de fysieke expressie van verrijkte documenten in een kennis archief. Een effectief gebruik van uw verrijkte documenten vereist structuur. In dit artikel vindt u meer informatie over de structuur en relaties, leert u hoe u de projectie-eigenschappen kunt samen stellen, en hoe u gegevens kunt relateren in verschillende projectie typen die u maakt. 

Als u een projectie wilt maken, moet u de gegevens vorm geven met behulp van een [shaper-vaardigheid](cognitive-search-skill-shaper.md) om een aangepast object te maken of de syntaxis van de inline-vorm te gebruiken in een projectie definitie. 

Een gegevensshape bevat alle gegevens die u wilt projecteren, gevormd als een hiërarchie van knoop punten. In dit artikel ziet u verschillende technieken voor het vorm geven van gegevens, zodat deze kunnen worden geprojecteerd in fysieke structuren die de verwerking van rapporten, analyses of downstream verzorgen. 

De voor beelden die in dit artikel worden weer gegeven, vindt u in dit REST API-voor [beeld](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), dat u kunt downloaden en uitvoeren in een HTTP-client.

## <a name="introduction-to-the-examples"></a>Inleiding tot de voor beelden

Als u bekend bent met [projecties](knowledge-store-projection-overview.md), herinnert u zich dat er drie typen zijn:

+ Tabellen
+ Objecten
+ Bestanden

Tabel prognoses worden opgeslagen in azure-tabel opslag. Object-en bestands projecties worden geschreven naar de Blob-opslag, waarbij object projecties worden opgeslagen als JSON-bestanden, en kan inhoud bevatten uit het bron document en eventuele vaardigheids uitvoer of verrijkingen. De verrijkings pijplijn kan ook binaire bestanden zoals afbeeldingen extra heren, maar deze binaire bestanden worden geprojecteerd als bestands projecties. Wanneer een binair object wordt geprojecteerd als een object projectie, worden alleen de meta gegevens die eraan zijn gekoppeld, opgeslagen als een JSON-blob. 

Om inzicht te krijgen in het snij punt tussen data shaping en projecties, gebruiken we de volgende vaardig heden als basis voor het verkennen van verschillende configuraties. Deze vaardig heden verwerkt onbewerkte afbeeldings-en tekst inhoud. Prognoses worden gedefinieerd op basis van de inhoud van het document en de uitvoer van de vaardig heden, voor de scenario's die we willen ondersteunen.

> [!IMPORTANT] 
> Bij het experimenteren met projecties is het handig om [de eigenschap van de Indexeer functie](search-howto-incremental-index.md) in te stellen om kosten beheer te garanderen. Als u projecties bewerkt, wordt het hele document opnieuw verrijkt als de Indexeer functie cache niet is ingesteld. Als de cache is ingesteld en alleen de projecties zijn bijgewerkt, worden de vaardig heden-uitvoeringen voor eerder verrijkte documenten niet in rekening gebracht met nieuwe Cognitive Services kosten.

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

Door gebruik te maken van deze vaardig heden, met de null-`knowledgeStore` als basis, wordt in het eerste voor beeld het `knowledgeStore`-object ingevuld, geconfigureerd met projecties waarmee tabellaire gegevens structuren kunnen worden gebruikt in andere scenario's. 

## <a name="projecting-to-tables"></a>Projecteren in tabellen

Projecteren op tabellen in Azure Storage is handig voor rapportage en analyse met behulp van hulpprogram ma's zoals Power BI. Power BI kunnen tabellen lezen en relaties detecteren op basis van de sleutels die tijdens de projectie worden gegenereerd. Als u een dash board probeert te maken, wordt deze taak vereenvoudigd door gerelateerde gegevens. 

Stel dat we een dash board willen maken waarin we de belangrijkste woord groepen kunnen visualiseren die zijn geëxtraheerd uit documenten als een Word-Cloud. Als u de juiste gegevens structuur wilt maken, kunnen we een shaper-vaardigheid toevoegen aan de vaardig heden om een aangepaste vorm te maken die de Documentspecifieke Details en sleutel zinnen heeft. De aangepaste vorm wordt `pbiShape` genoemd in het hoofd knooppunt `document`.

> [!NOTE] 
> Tabel prognoses zijn Azure Storage tabellen, bepaald door de opslag limieten die zijn opgelegd door Azure Storage. Zie [limieten voor tabel opslag](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)voor meer informatie. Het is handig om te weten dat de grootte van de entiteit niet groter mag zijn dan 1 MB en dat één eigenschap niet groter mag zijn dan 64 KB. Deze beperkingen maken tabellen een goede oplossing voor het opslaan van een groot aantal kleine entiteiten.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Een aangepaste vorm maken met behulp van een shaper-vaardigheid

Een aangepaste vorm maken die u in tabel opslag kunt projecteren. Zonder aangepaste vorm kan een projectie alleen verwijzen naar één knoop punt (één projectie per uitvoer). Door een aangepaste vorm te maken, kunt u verschillende elementen samen voegen in een nieuwe logische geheel die kan worden geprojecteerd als één tabel of worden gesegmenteerd en gedistribueerd in een verzameling tabellen. 

In dit voor beeld worden meta gegevens en geïdentificeerde entiteiten en sleutel zinnen gecombineerd met de aangepaste vorm. Het object wordt `pbiShape` genoemd en is onder `/document`het bovenliggende item. 

> [!IMPORTANT] 
> Eén doel van het maken van een vorm is om ervoor te zorgen dat alle verrijkings knooppunten worden uitgedrukt in een goed opgemaakte JSON, wat vereist is voor het projecteren in het kennis archief. Dit geldt met name wanneer een verrijkings structuur knoop punten bevat die geen goed gevormde JSON zijn (bijvoorbeeld wanneer een verrijking is geparenteerd naar een primitieve als een teken reeks).
>
> Let op de laatste twee knoop punten `KeyPhrases` en `Entities`. Deze worden verpakt in een geldig JSON-object met de `sourceContext`. Dit is vereist als `keyphrases` en `entities` verrijkingen op primitieven zijn en moeten worden geconverteerd naar een geldige JSON voordat ze kunnen worden geprojecteerd.
>


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

Voeg de bovenstaande shaper-vaardigheid toe aan de vaardig heden. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Nu we alle gegevens hebben die nodig zijn om tabellen te projecteren, werkt u het knowledgeStore-object bij met de tabel definities. In dit voor beeld hebben we drie tabellen, die zijn gedefinieerd door de eigenschappen `tableName`, `source` en `generatedKeyName` in te stellen.

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

U kunt uw werk verwerken door de volgende stappen uit te voeren:

1. Stel de eigenschap ```storageConnectionString``` in op een geldig v2-opslag account voor algemeen gebruik connection string.  

1. Werk de vaardig heden bij door de PUT-aanvraag uit te geven.

1. Nadat u de vaardig heden hebt bijgewerkt, voert u de Indexeer functie uit. 

U hebt nu een werkende projectie met drie tabellen. Het importeren van deze tabellen in Power BI moet leiden tot het Power BI automatisch detecteren van de relaties.

Voordat u verdergaat met het volgende voor beeld, kunt u aspecten van de tabel projectie opnieuw bezoeken om inzicht te krijgen in de mechanismen van segmenteren en gerelateerde gegevens.

### <a name="slicing"></a>Segmenterings 

Segmenteren is een techniek waarbij een hele geconsolideerde vorm wordt onderverdeeld in samenstellende delen. De uitkomst bestaat uit afzonderlijke, maar gerelateerde tabellen die u afzonderlijk kunt gebruiken.

In het voor beeld is `pbiShape` de geconsolideerde vorm (of het verrijkings knooppunt). In de projectie definitie wordt `pbiShape` gesegmenteerd in extra tabellen, waarmee u onderdelen van de vorm, ```keyPhrases``` en ```Entities```kunt uitpakken. In Power BI is dit handig als er meerdere entiteiten en zinsdelen aan elk document zijn gekoppeld, en u krijgt meer inzicht als u entiteiten en zinsdelen als gecategoriseerde gegevens kunt zien.

Bij het segmenteren wordt impliciet een relatie tussen de bovenliggende en onderliggende tabellen gegenereerd, met behulp van de ```generatedKeyName``` in de bovenliggende tabel om een kolom met dezelfde naam in de onderliggende tabel te maken. 

### <a name="naming-relationships"></a>Namen van relaties

De eigenschappen ```generatedKeyName``` en ```referenceKeyName``` worden gebruikt voor het koppelen van gegevens in tabellen of zelfs in projectie typen. Elke rij in de onderliggende tabel/projectie heeft een eigenschap die terugkeert naar het bovenliggende item. De naam van de kolom of eigenschap in het onderliggende element is het ```referenceKeyName``` van het bovenliggende item. Wanneer de ```referenceKeyName``` niet is ingesteld, wordt deze door de service standaard ingesteld op de ```generatedKeyName``` van het bovenliggende item. 

Power BI is afhankelijk van deze gegenereerde sleutels om relaties in de tabellen te detecteren. Als u de kolom in de onderliggende tabel anders wilt hebben, stelt u de eigenschap ```referenceKeyName``` van de bovenliggende tabel in. Een voor beeld hiervan is het instellen van de ```generatedKeyName``` als ID in de tabel pbiDocument en de ```referenceKeyName``` als DocumentID. Dit resulteert in de kolom in de pbiEntities-en pbiKeyPhrases-tabellen met de document-id met de naam DocumentID.

## <a name="projecting-to-objects"></a>Projecteren op objecten

Object projecties hebben niet dezelfde beperkingen als tabel projecties en zijn beter geschikt voor het projecteren van grote documenten. In dit voor beeld projecteert u het hele document naar een object projectie. Object projecties zijn beperkt tot één projectie in een container en kunnen niet worden gesegmenteerd.

Als u een object projectie wilt definiëren, zullen we de ```objects``` matrix in de projecties gebruiken. U kunt een nieuwe vorm genereren met behulp van de shaper-vaardigheid of de inline-vorm van de object projectie gebruiken. In het voor beeld van tabellen is de benadering van het maken van een vorm en het segmenteren gedemonstreerd, maar in dit voor beeld wordt het gebruik van inline-vorm gegeven. 

Inline-vormgeving is de mogelijkheid om een nieuwe vorm te maken in de definitie van de invoer voor een projectie. Inline-vormgeving maakt een anoniem object dat identiek is aan wat een Shapere vaardigheid produceert (in ons geval `pbiShape`). Inline-vormgeving is handig als u een vorm definieert die u niet wilt hergebruiken.

De eigenschap projecties is een matrix. In dit voor beeld voegen we een nieuw projectie-exemplaar toe aan de matrix, waar de knowledgeStore-definitie inline-projecties bevat. Wanneer u inline-projecties gebruikt, kunt u de shaper-vaardigheid weglaten.

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

## <a name="projecting-to-file"></a>Projecteren naar bestand

Bestands projecties zijn afbeeldingen die zijn geëxtraheerd uit het bron document of uitvoer van verrijking die uit het verrijkings proces kunnen worden geprojecteerd. Bestands projecties, vergelijkbaar met object projecties, worden geïmplementeerd als blobs in Azure Storage en bevatten de installatie kopie. 

Als u een bestands projectie wilt genereren, gebruiken we de `files` matrix in het projectie object. In dit voor beeld worden alle afbeeldingen uit het document geëxtraheerd naar een container met de naam `samplefile`.

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

Voor een complexere scenario moet u mogelijk inhoud in verschillende projectie typen projecteren. Als u bijvoorbeeld een aantal gegevens zoals sleutel zinnen en entiteiten naar tabellen wilt projecteren, slaat u de OCR-resultaten van tekst en indelings tekst op als objecten en projecteert u de afbeeldingen als bestanden. 

In dit voor beeld bevatten updates voor de vaardig heden de volgende wijzigingen:

1. Maak een tabel met een rij voor elk document.
1. Maak een tabel met betrekking tot de document tabel waarbij elke sleutel zin is geïdentificeerd als een rij in deze tabel.
1. Maak een tabel met betrekking tot de document tabel waarbij elke entiteit is geïdentificeerd als een rij in deze tabel.
1. Maak een object projectie met de indelings tekst voor elke afbeelding.
1. Een bestands projectie maken, waarbij elke uitgepakte afbeelding wordt geprojecteerd.
1. Maak een kruis verwijzings tabel die verwijzingen bevat naar de document tabel, object projectie met de indelings tekst en de bestands projectie.

Deze wijzigingen worden verder doorgevoerd in de knowledgeStore definitie. 

### <a name="shape-data-for-cross-projection"></a>Vorm gegevens voor kruis projectie

Als u de shapes wilt ophalen die we nodig hebben voor deze projecties, begint u met het toevoegen van een nieuwe shaper-vaardigheid die een object met de naam `crossProjection`maakt. 

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

### <a name="define-table-object-and-file-projections"></a>Tabel-, object-en bestands prognoses definiëren

Vanuit het geconsolideerde crossProjection-object kunnen we het object in meerdere tabellen verdelen, de OCR-uitvoer als blobs vastleggen en de installatie kopie vervolgens opslaan als bestanden (ook in Blob Storage).

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

### <a name="relationships-among-table-object-and-file-projections"></a>Relaties tussen tabel-, object-en bestands projectie

In dit voor beeld wordt ook een andere functie van projecties gemarkeerd. Als u meerdere typen projecties in hetzelfde projectie object definieert, is er een relatie binnen en over de verschillende typen (tabellen, objecten, bestanden), zodat u kunt beginnen met een tabelrij voor een document en alle OCR-tekst voor de afbeeldingen zoekt. in dat document in de object projectie. 

Als u niet wilt dat de gegevens zijn gerelateerd, definieert u de projecties in verschillende projectie-objecten. Het volgende code fragment resulteert bijvoorbeeld in de tabellen die aan elkaar zijn gerelateerd, maar zonder relaties tussen de tabellen en de projecties (OCR-tekst). 

Projectie groepen zijn handig wanneer u dezelfde gegevens in verschillende vormen wilt projecteren voor verschillende behoeften. Bijvoorbeeld een projectie groep voor het dash board Power BI en een andere projectie groep voor het vastleggen van gegevens die worden gebruikt voor het trainen van een machine learning model dat in een aangepaste vaardigheid is ingepakt.

Bij het opbouwen van projecties van verschillende typen worden eerst bestands-en object projecties gegenereerd en worden de paden aan de tabellen toegevoegd.

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

## <a name="common-issues"></a>Algemene problemen

Bij het definiëren van een projectie zijn er enkele veelvoorkomende problemen die kunnen leiden tot onverwachte resultaten. Controleer deze problemen als de uitvoer in het kennis archief niet de verwachte resultaten heeft.

+ Teken reeks verrijkingen worden niet omgezet in een geldige JSON. Wanneer teken reeksen worden uitgebreid, bijvoorbeeld `merged_content` verrijkt met sleutel zinnen, wordt de verrijkte eigenschap weer gegeven als een onderliggend element van `merged_content` binnen de verrijkings structuur. De standaard weergave is geen juist opgemaakte JSON. Op de projectie tijd moet u de verrijking transformeren naar een geldig JSON-object met een naam en een waarde.

+ De ```/*``` aan het einde van een bronpad weglaten. Als de bron van een projectie is `/document/pbiShape/keyPhrases`, wordt de matrix met sleutel zinnen geprojecteerd als één object/rij. Stel in plaats daarvan het bronpad in op `/document/pbiShape/keyPhrases/*` om één rij of een object voor elk van de sleutel zinnen te leveren.

+ Syntaxis fouten van pad. Paden selecteren zijn hoofdletter gevoelig en kunnen leiden tot ontbrekende invoer waarschuwingen als u niet de exacte Case voor de selector gebruikt.

## <a name="next-steps"></a>Volgende stappen

In de voor beelden in dit artikel worden algemene patronen gedemonstreerd voor het maken van projecties. Nu u een goed inzicht hebt in de concepten, bent u beter uitgerust om projecties te maken voor uw specifieke scenario.

Als u de definities van Knowledge Store doorloopt, overweeg dan incrementele verrijking als uw volgende stap. Incrementele verrijking is gebaseerd op caching, waarmee u verrijkingen die niet anderszins worden beïnvloed door een vaardig heden-wijziging, opnieuw kunt gebruiken. Dit is vooral handig voor pijp lijnen met OCR-en afbeeldings analyse.

> [!div class="nextstepaction"]
> [Inleiding tot incrementele verrijking en caching](cognitive-search-incremental-indexing-conceptual.md)