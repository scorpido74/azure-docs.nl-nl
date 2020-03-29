---
title: Projecties definiëren in een kenniswinkel
titleSuffix: Azure Cognitive Search
description: Voorbeelden van veelvoorkomende patronen voor het projecteren van verrijkte documenten in het kennisarchief voor gebruik met Power BI of Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943680"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Kennisarchiefprojecties: Hoe verrijkingen vorm te geven en te exporteren

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

Projecties zijn de fysieke expressie van verrijkte documenten in een kenniswinkel. Effectief gebruik van uw verrijkte documenten vereist structuur. In dit artikel verken je zowel structuur als relaties, leer je hoe projectie-eigenschappen kunnen worden opgebouwd en hoe je gegevens relateren aan projectietypen die je maakt. 

Als u een projectie wilt maken, moet u de gegevens vormgeven met behulp van een [shaper-vaardigheid](cognitive-search-skill-shaper.md) om een aangepast object te maken of de syntaxis voor inlinevormgeving te gebruiken in een projectiedefinitie. 

Een gegevensvorm bevat alle gegevens die u wilt projecteren, gevormd als een hiërarchie van knooppunten. Dit artikel toont u verschillende technieken voor het vormgeven van gegevens, zodat het kan worden geprojecteerd in fysieke structuren die bevorderlijk zijn voor rapportage, analyse of downstream verwerking. 

De voorbeelden in dit artikel zijn te vinden in dit [REST API-voorbeeld](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), dat u downloaden en uitvoeren in een HTTP-client.

## <a name="introduction-to-the-examples"></a>Inleiding tot de voorbeelden

Als u bekend bent met [projecties,](knowledge-store-projection-overview.md)zult u zich herinneren dat er drie typen zijn:

+ Tabellen
+ Objecten
+ Bestanden

Tabelprojecties worden opgeslagen in Azure Table-opslag. Object- en bestandsprojecties worden naar blob-opslag geschreven, waarbij objectprojecties worden opgeslagen als JSON-bestanden en inhoud uit het brondocument kunnen bevatten, evenals eventuele vaardigheidsuitvoer of verrijkingen. De verrijkingspijplijn kan ook binaries zoals afbeeldingen extraheren, deze binaire bestanden worden geprojecteerd als bestandsprojecties. Wanneer een binair object wordt geprojecteerd als een objectprojectie, worden alleen de metagegevens die eraan zijn gekoppeld, opgeslagen als een JSON-blob. 

Om het snijpunt tussen gegevensvormgeving en projecties te begrijpen, gebruiken we de volgende vaardigheden als basis voor het verkennen van verschillende configuraties. Deze skillset verwerkt ruwe afbeeldings- en tekstinhoud. Projecties worden gedefinieerd op basis van de inhoud van het document en de output van de vaardigheden, voor de scenario's die we willen ondersteunen.

> [!IMPORTANT] 
> Wanneer u experimenteert met projecties, is het handig om [de eigenschap indexercache in](search-howto-incremental-index.md) te stellen om kostenbeheersing te garanderen. Als de indexerende cache niet is ingesteld, worden bewerkingsprognoses opnieuw verrijkt. Wanneer de cache is ingesteld en alleen de projecties zijn bijgewerkt, leiden skillset-uitvoeringen voor eerder verrijkte documenten niet tot nieuwe kosten voor Cognitive Services.

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

Met behulp van deze `knowledgeStore` skillset, met zijn null `knowledgeStore` als basis, vult ons eerste voorbeeld het object in, geconfigureerd met projecties die tabelvormige gegevensstructuren maken die we in andere scenario's kunnen gebruiken. 

## <a name="projecting-to-tables"></a>Projecteren aan tabellen

Projecteren op tabellen in Azure Storage is handig voor rapportage en analyse met behulp van hulpprogramma's zoals Power BI. Power BI kan lezen uit tabellen en relaties ontdekken op basis van de sleutels die worden gegenereerd tijdens projectie. Als u een dashboard probeert te maken, wordt het hebben van gerelateerde gegevens die taak vereenvoudigen. 

Stel dat we proberen een dashboard te bouwen waar we de belangrijkste zinnen uit documenten kunnen visualiseren als een woordwolk. Als u de juiste gegevensstructuur wilt maken, kunnen we een Shaper-vaardigheid toevoegen aan de skillset om een aangepaste vorm te maken met de documentspecifieke details en sleutelzinnen. De aangepaste vorm `pbiShape` wordt `document` aangeroepen op het wortelknooppunt.

> [!NOTE] 
> Tabelprojecties zijn Azure Storage-tabellen, die worden bepaald door de opslaglimieten die worden opgelegd door Azure Storage. Zie [tabelopslaglimieten voor](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)meer informatie . Het is handig om te weten dat de entiteitsgrootte niet groter mag zijn dan 1 MB en dat één eigenschap niet groter mag zijn dan 64 KB. Deze beperkingen maken tabellen een goede oplossing voor het opslaan van een groot aantal kleine entiteiten.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Een Shaper-vaardigheid gebruiken om een aangepaste vorm te maken

Maak een aangepaste vorm die u projecteren in tabelopslag. Zonder aangepaste vorm kan een projectie slechts naar één knooppunt verwijzen (één projectie per uitvoer). Als u een aangepaste vorm maakt, u verschillende elementen samenvoegen tot een nieuw logisch geheel dat als één tabel kan worden geprojecteerd of kan worden gesneden en verdeeld over een verzameling tabellen. 

In dit voorbeeld combineert de aangepaste shape metagegevens en geïdentificeerde entiteiten en sleutelzinnen. Het object `pbiShape` wordt aangeroepen `/document`en wordt geparenteerd onder . 

> [!IMPORTANT] 
> Een doel van het vormgeven is ervoor te zorgen dat alle verrijkingsknooppunten worden uitgedrukt in goed gevormde JSON, wat nodig is voor het projecteren in kennisopterij. Dit geldt vooral wanneer een verrijkingsboom knooppunten bevat die geen goed gevormde JSON zijn (bijvoorbeeld wanneer een verrijking wordt geparenterd met een primitieve als een tekenreeks).
>
> Let op de `KeyPhrases` laatste `Entities`twee knooppunten, en . Deze zijn verpakt in een geldig `sourceContext`JSON-object met de . Dit is `keyphrases` vereist `entities` als en zijn verrijkingen op primitieven en moeten worden omgezet in geldige JSON voordat ze kunnen worden geprojecteerd.
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

Voeg de bovenstaande Shaper-vaardigheid toe aan de skillset. 

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

Nu we alle gegevens hebben die nodig zijn om naar tabellen te projecteren, werkt u het knowledgeStore-object bij met de tabeldefinities. In dit voorbeeld hebben we drie tabellen, `tableName` `source` gedefinieerd `generatedKeyName` door de eigenschappen en eigenschappen in te stellen.

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

U uw werk verwerken door de volgende stappen te volgen:

1. Stel ```storageConnectionString``` de eigenschap in op een geldige V2-tekenreeks voor opslagaccountvoor algemene doeleinden.  

1. Werk de skillset bij door het PUT-verzoek uit te geven.

1. Voer de indexeren uit nadat u de skillset hebt bijgewerkt. 

Je hebt nu een werkende projectie met drie tafels. Als u deze tabellen importeert in Power BI, moet power bi de relaties automatisch ontdekken.

Voordat u verder gaat met het volgende voorbeeld, u aspecten van de tabelprojectie opnieuw bekijken om de mechanica van het snijden en met betrekking van gegevens te begrijpen.

### <a name="slicing"></a>Snijden 

Snijden is een techniek die een hele geconsolideerde vorm onderverdeelt in samenstellende delen. Het resultaat bestaat uit afzonderlijke maar gerelateerde tabellen waarmee u individueel werken.

In het `pbiShape` voorbeeld is de geconsolideerde vorm (of verrijkingsknooppunt). In de projectiedefinitie `pbiShape` wordt in extra tabellen gesneden, waarmee u delen ```keyPhrases``` ```Entities```van de vorm uittrekken en. In Power BI is dit handig omdat meerdere entiteiten en sleutelzinnen aan elk document zijn gekoppeld en u meer inzichten krijgt als u entiteiten en sleutelzinnen als gecategoriseerde gegevens zien.

Als u hiermee snijdt, wordt impliciet een relatie ```generatedKeyName``` gegenereerd tussen de bovenliggende en onderliggende tabellen, waarbij u de in de bovenliggende tabel gebruikt om een kolom met dezelfde naam in de onderliggende tabel te maken. 

### <a name="naming-relationships"></a>Naamgevingsrelaties

De ```generatedKeyName``` ```referenceKeyName``` eigenschappen en eigenschappen worden gebruikt om gegevens tussen tabellen of zelfs projectietypen met elkaar te vergelijken. Elke rij in de onderliggende tabel/projectie heeft een eigenschap die naar de bovenliggende aanwijst. De naam van de kolom of ```referenceKeyName``` eigenschap in het kind is de van de ouder. Wanneer ```referenceKeyName``` de service niet wordt geleverd, ```generatedKeyName``` wordt deze standaard ingesteld op de van de bovenliggende. 

Power BI vertrouwt op deze gegenereerde sleutels om relaties binnen de tabellen te ontdekken. Als u de kolom in de onderliggende ```referenceKeyName``` tabel anders wilt noemen, stelt u de eigenschap in op de bovenliggende tabel. Een voorbeeld hiervan is ```generatedKeyName``` het instellen van de als-id op de pbiDocument-tabel en de ```referenceKeyName``` as DocumentID. Dit zou ertoe leiden dat de kolom in de tabellen pbiEntiteiten en pbiKeyPhrases met de document-id de naam DocumentID bevat.

## <a name="projecting-to-objects"></a>Projecteren op objecten

Objectprojecties hebben niet dezelfde beperkingen als tabelprojecties en zijn beter geschikt voor het projecteren van grote documenten. In dit voorbeeld projecteren we het hele document op een objectprojectie. Objectprojecties zijn beperkt tot één projectie in een container en kunnen niet worden gesneden.

Om een objectprojectie te definiëren, gebruiken we de ```objects``` array in de projecties. U een nieuwe vorm genereren met behulp van de Shaper-vaardigheid of inline-vormgeving van de objectprojectie gebruiken. Terwijl het voorbeeld van de tabellen de benadering van het maken van een vorm en snijden demonstreerde, toont dit voorbeeld het gebruik van inline shaping aan. 

Inline shaping is de mogelijkheid om een nieuwe vorm te creëren in de definitie van de ingangen naar een projectie. Inline shaping creëert een anoniem object dat identiek is aan wat `pbiShape`een Shaper-vaardigheid zou produceren (in ons geval). Inline shaping is handig als u een vorm definieert die u niet opnieuw wilt gebruiken.

De eigenschap projecties is een array. Voor dit voorbeeld voegen we een nieuwe projectie-instantie toe aan de array, waarbij de knowledgeStore-definitie inlineprojecties bevat. Wanneer u inline projecties gebruikt, u de Shaper-vaardigheid weglaten.

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

## <a name="projecting-to-file"></a>Projecteren op bestand

Bestandsprojecties zijn afbeeldingen die zijn geëxtraheerd uit het brondocument of verrijkingsuitvoer die uit het verrijkingsproces kunnen worden geprojecteerd. Bestandsprojecties, vergelijkbaar met objectprojecties, worden geïmplementeerd als blobs in Azure Storage en bevatten de afbeelding. 

Om een bestandsprojectie te `files` genereren, gebruiken we de array in het projectieobject. In dit voorbeeld worden alle afbeeldingen die `samplefile`uit het document zijn geëxtraheerd, toegewezen aan een container met de naam .

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

Voor een complexer scenario moet u mogelijk inhoud projecteren voor alle projectietypen. Als u bijvoorbeeld bepaalde gegevens zoals sleutelzinnen en entiteiten naar tabellen moet projecteren, slaat u de OCR-resultaten van tekst en lay-outtekst op als objecten en projecteert u de afbeeldingen als bestanden. 

In dit voorbeeld bevatten updates van de skillset de volgende wijzigingen:

1. Maak een tabel met een rij voor elk document.
1. Maak een tabel met betrekking tot de documenttabel met elke sleutelzin die is geïdentificeerd als een rij in deze tabel.
1. Maak een tabel met betrekking tot de documenttabel met elke entiteit die is geïdentificeerd als een rij in deze tabel.
1. Maak een objectprojectie met de lay-outtekst voor elke afbeelding.
1. Maak een bestandsprojectie, waarbij elke geëxtraheerde afbeelding wordt geprojecteerd.
1. Maak een kruisverwijzingstabel met verwijzingen naar de documenttabel, objectprojectie met de lay-outtekst en de bestandsprojectie.

Deze veranderingen worden weerspiegeld in de knowledgeStore definitie verder naar beneden. 

### <a name="shape-data-for-cross-projection"></a>Vormgegevens voor kruisprojectie

Om de vormen te krijgen die we nodig hebben voor deze projecties, `crossProjection`begin je met het toevoegen van een nieuwe Shaper-vaardigheid die een gevormd object maakt dat wordt genoemd. 

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

### <a name="define-table-object-and-file-projections"></a>Tabel-, object- en bestandsprojecties definiëren

Vanuit het geconsolideerde object crossProjection kunnen we het object in meerdere tabellen snijden, de OCR-uitvoer vastleggen als blobs en de afbeelding vervolgens opslaan als bestanden (ook in Blob-opslag).

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

Objectprojecties vereisen een containernaam voor elke projectie, objectprojecties of bestandsprojecties die een container niet kunnen delen. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relaties tussen tabel-, object- en bestandsprojecties

In dit voorbeeld wordt ook een ander kenmerk van projecties belicht. Door meerdere typen projecties binnen hetzelfde projectieobject te definiëren, is er een relatie uitgedrukt in en tussen de verschillende typen (tabellen, objecten, bestanden), zodat u beginnen met een tabelrij voor een document en alle OCR-tekst voor de afbeeldingen vinden binnen dat document in de objectprojectie. 

Als u de gegevens niet wilt hebben, definieert u de projecties in verschillende projectieobjecten. Het volgende fragment resulteert er bijvoorbeeld in dat de tabellen gerelateerd zijn, maar zonder relaties tussen de tabellen en de OCR-tekstprojecties (objecttekst). 

Projectiegroepen zijn handig als u dezelfde gegevens in verschillende vormen wilt projecteren voor verschillende behoeften. Bijvoorbeeld een projectiegroep voor het Power BI-dashboard en een andere projectiegroep voor het vastleggen van gegevens die worden gebruikt om een machine learning-model te trainen dat is verpakt in een aangepaste vaardigheid.

Bij het bouwen van projecties van verschillende typen worden eerst bestands- en objectprojecties gegenereerd en worden de paden aan de tabellen toegevoegd.

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

Bij het definiëren van een projectie zijn er een paar veelvoorkomende problemen die onverwachte resultaten kunnen veroorzaken. Controleer op deze problemen als de uitvoer in de kenniswinkel niet is wat u verwacht.

+ Het niet vormgeven van snaarverrijkingen tot geldige JSON. Wanneer tekenreeksen worden `merged_content` verrijkt, bijvoorbeeld verrijkt met sleutelzinnen, wordt de `merged_content` verrijkte eigenschap weergegeven als een onderliggend kind van binnen de verrijkingsboom. De standaardrepresentatie is niet goed gevormd JSON. Dus op projectietijd, zorg ervoor dat de verrijking om te zetten in een geldig JSON object met een naam en een waarde.

+ Het weglaten ```/*``` van het aan het einde van een bronpad. Als de bron van `/document/pbiShape/keyPhrases`een projectie is, wordt de array met sleutelzinnen geprojecteerd als één object/rij. Stel in plaats daarvan `/document/pbiShape/keyPhrases/*` het bronpad in op een enkele rij of object voor elk van de sleutelzinnen.

+ Fouten in de syntaxis van het pad. Padselectors zijn hoofdlettergevoelig en kunnen leiden tot ontbrekende invoerwaarschuwingen als u de exacte aanvraag niet voor de kiezer gebruikt.

## <a name="next-steps"></a>Volgende stappen

De voorbeelden in dit artikel tonen gemeenschappelijke patronen over het maken van projecties. Nu je een goed begrip hebt van de concepten, ben je beter uitgerust om projecties te bouwen voor je specifieke scenario.

Terwijl u nieuwe functies verkent, u incrementele verrijking als uw volgende stap beschouwen. Incrementele verrijking is gebaseerd op caching, waarmee u eventuele verrijkingen die op een andere manier niet worden beïnvloed door een aanpassing van de skillset opnieuw gebruiken. Dit is vooral handig voor pijplijnen met OCR- en beeldanalyse.

> [!div class="nextstepaction"]
> [Inleiding tot incrementele verrijking en caching](cognitive-search-incremental-indexing-conceptual.md)

Voor een overzicht van projecties vindt u meer informatie over mogelijkheden zoals groepen en snijden en hoe u [ze definieert in een vaardighedenset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projecties in een kenniswinkel](knowledge-store-projection-overview.md)

