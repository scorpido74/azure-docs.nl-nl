---
title: Taken end-to-end uitvoeren met sjablonen - Azure Batch
description: Met alleen CLI-opdrachten u een groep maken, invoergegevens uploaden, taken en bijbehorende taken maken en de resulterende uitvoergegevens downloaden.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c7459c4dc700f034feafbf133b831a52b9233d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020162"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch CLI-sjablonen en bestandsoverdracht gebruiken

Met behulp van een Azure Batch-extensie voor de Azure CLI is het mogelijk om Batch-taken uit te voeren zonder code te schrijven.

Json-sjabloonbestanden maken en gebruiken met de Azure CLI om batchgroepen, taken en taken te maken. Gebruik de opdrachten voor CLI-extensie om eenvoudig taakinvoerbestanden te uploaden naar het opslagaccount dat is gekoppeld aan het Batch-account en taakuitvoerbestanden te downloaden.

## <a name="overview"></a>Overzicht

Met een extensie voor de Azure CLI kan Batch end-to-end worden gebruikt door gebruikers die geen ontwikkelaars zijn. Met alleen CLI-opdrachten u een groep maken, invoergegevens uploaden, taken en bijbehorende taken maken en de resulterende uitvoergegevens downloaden. Er is geen extra code vereist. Voer de CLI-opdrachten rechtstreeks uit of integreer ze in scripts.

Batchsjablonen bouwen voort op de [bestaande Batch-ondersteuning in de Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) voor JSON-bestanden om eigenschapswaarden op te geven bij het maken van pools, taken, taken en andere items. Batchsjablonen voegen de volgende mogelijkheden toe:

-   Parameters kunnen worden gedefinieerd. Wanneer de sjabloon wordt gebruikt, worden alleen de parameterwaarden opgegeven om het item te maken, waarbij andere eigenschapswaarden van het item zijn opgegeven in de sjabloontekst. Een gebruiker die Batch en de toepassingen begrijpt die door Batch moeten worden uitgevoerd, kan sjablonen maken, waarbij de waarden van de groep, taak en taakeigenschap worden opgegeven. Een gebruiker die minder bekend is met Batch en/of de toepassingen hoeft alleen de waarden voor de gedefinieerde parameters op te geven.

-   Taakfabrieken maken een of meer taken die aan een taak zijn gekoppeld, waardoor veel taakdefinities niet hoeven te worden gemaakt en het indienen van taken aanzienlijk wordt vereenvoudigd.


Taken gebruiken doorgaans invoergegevensbestanden en produceren uitvoergegevensbestanden. Een opslagaccount is standaard gekoppeld aan elk Batch-account. Bestanden van en naar dit opslagaccount overbrengen met behulp van de CLI, zonder codering en zonder opslagreferenties.

[Ffmpeg](https://ffmpeg.org/) is bijvoorbeeld een populaire applicatie die audio- en videobestanden verwerkt. Hier volgen stappen met de Azure Batch CLI om ffmpeg aan te roepen om bronvideobestanden naar verschillende resoluties te transcoderen.

-   Maak een poolsjabloon. De gebruiker die de sjabloon maakt, weet hoe hij de ffmpeg-toepassing en de vereisten ervan moet aanroepen; ze specificeren het juiste besturingssysteem, vm-grootte, hoe ffmpeg is geïnstalleerd (bijvoorbeeld vanuit een toepassingspakket of met behulp van een package manager) en andere waarden van de pooleigenschap. Parameters worden gemaakt, zodat wanneer de sjabloon wordt gebruikt, alleen de pool-ID en het aantal VM's moeten worden opgegeven.

-   Een taaksjabloon maken. De gebruiker die de sjabloon maakt, weet hoe ffmpeg moet worden aangeroepen om bronvideo naar een andere resolutie te transcoderen en geeft de taakopdrachtregel op; ze weten ook dat er een map is met de bronvideobestanden, met een taak die per invoerbestand vereist is.

-   Een eindgebruiker met een set videobestanden die eerst moet transcoderen, maakt eerst een groep met de poolsjabloon, waarbij alleen de groep-id en het aantal vereiste VM's worden opgegeven. Ze kunnen vervolgens de bronbestanden uploaden naar transcode. Een taak kan vervolgens worden ingediend met behulp van de taaksjabloon, waarbij alleen de pool-ID en de locatie van de geüploade bronbestanden worden opgegeven. De batchtaak wordt gemaakt en er wordt één taak per invoerbestand gegenereerd. Ten slotte kunnen de transgecodeerde uitvoerbestanden worden gedownload.

## <a name="installation"></a>Installeren

Als u de AZURE Batch CLI-extensie wilt installeren, [installeert u eerst de Azure CLI 2.0](/cli/azure/install-azure-cli)of voert u de Azure CLI uit in [Azure Cloud Shell](../cloud-shell/overview.md).

Installeer de nieuwste versie van de Batch-extensie met de volgende opdracht Azure CLI:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Zie de [GitHub repo](https://github.com/Azure/azure-batch-cli-extensions)voor meer informatie over de Batch CLI-extensie en extra installatieopties.


Als u de functies van de CLI-extensie wilt gebruiken, hebt u een Azure Batch-account nodig en voor de opdrachten die bestanden van en naar opslag overbrengen, een gekoppeld opslagaccount.

Zie [Batchresources beheren met Azure CLI](batch-cli-get-started.md)als u zich wilt aanmelden bij een Batch-account met de Azure CLI.

## <a name="templates"></a>Sjablonen

Azure Batch-sjablonen zijn vergelijkbaar met Azure Resource Manager-sjablonen, in functionaliteit en syntaxis. Het zijn JSON-bestanden die eigenschappen namen en waarden van items bevatten, maar de volgende hoofdconcepten toevoegen:

-   **Parameters**

    -   Toestaan dat eigenschapswaarden worden opgegeven in een hoofdsectie, waarbij alleen parameterwaarden moeten worden opgegeven wanneer de sjabloon wordt gebruikt. De volledige definitie voor een pool kan bijvoorbeeld in het lichaam worden geplaatst en slechts één parameter die is gedefinieerd voor pool-id; alleen een pool-ID-tekenreeks hoeft daarom te worden geleverd om een pool te maken.
        
    -   De sjabloonbody kan worden geschreven door iemand met kennis van Batch en de toepassingen die door Batch moeten worden uitgevoerd; alleen waarden voor de door de auteur gedefinieerde parameters moeten worden opgegeven wanneer de sjabloon wordt gebruikt. Een gebruiker zonder de diepgaande batch- en/of applicatiekennis kan daarom de sjablonen gebruiken.

-   **Variabelen**

    -   Toestaan dat eenvoudige of complexe parameterwaarden op één plaats worden opgegeven en op een of meer plaatsen in de sjabloontekst worden gebruikt. Variabelen kunnen de grootte van de sjabloon vereenvoudigen en verkleinen en deze beter onderhouden door één locatie te hebben om eigenschappen te wijzigen.

-   **Constructies op hoger niveau**

    -   Sommige constructies op een hoger niveau zijn beschikbaar in de sjabloon die nog niet beschikbaar zijn in de batch-API's. Een taakfabriek kan bijvoorbeeld worden gedefinieerd in een taaksjabloon die meerdere taken voor de taak maakt, met behulp van een algemene taakdefinitie. Deze constructies voorkomen dat u moet coderen om dynamisch meerdere JSON-bestanden te maken, zoals één bestand per taak, en scriptbestanden maken om toepassingen te installeren via een package manager.

    -   Op een gegeven moment kunnen deze constructies worden toegevoegd aan de Batch-service en beschikbaar zijn in de Batch-API's, UI's, enz.

### <a name="pool-templates"></a>Sjablonen bijeenbrengen

Poolsjablonen ondersteunen de standaardsjabloonmogelijkheden van parameters en variabelen. Ze ondersteunen ook de volgende constructie op een hoger niveau:

-   **Pakketreferenties**

    -   Optioneel kan software worden gekopieerd naar knooppunten te bundelen met behulp van package managers. De package manager en package ID zijn opgegeven. Door een of meer pakketten aan te geven, voorkomt u dat u een script maakt dat de vereiste pakketten krijgt, het script installeert en het script op elk poolknooppunt uitvoert.

Het volgende is een voorbeeld van een sjabloon die een pool van Linux VM's maakt met ffmpeg geïnstalleerd. Als u deze wilt gebruiken, levert u alleen een pool-ID-tekenreeks en het aantal VM's in de groep:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Als het sjabloonbestand de naam _pool-ffmpeg.json_heeft gekregen, roept u de sjabloon als volgt aan:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

De CLI vraagt u om `poolId` waarden `nodeCount` voor de parameters en parameters op te geven. U de parameters ook in een JSON-bestand opgeven. Bijvoorbeeld:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Als het JSON-bestand parameters de naam *pool-parameters.json*heeft gekregen, roept u de sjabloon als volgt aan:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Taaksjablonen

Taaksjablonen ondersteunen de standaardsjabloonmogelijkheden van parameters en variabelen. Ze ondersteunen ook de volgende constructie op een hoger niveau:

-   **Taakfabriek**

    -   Hiermee maakt u meerdere taken voor een taak vanuit één taakdefinitie. Drie soorten taakfabrieken worden ondersteund: parametrische sweep, taak per bestand en taakverzameling.

Het volgende is een voorbeeld van een sjabloon waarmee een taak wordt gemaakt om MP4-videobestanden met ffmpeg te transcoderen naar een van de twee lagere resoluties. Het maakt één taak per bronvideobestand. Zie [Bestandsgroepen en bestandsoverdracht](#file-groups-and-file-transfer) voor meer informatie over bestandsgroepen voor taakinvoer en -uitvoer.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Als het sjabloonbestand de naam _job-ffmpeg.json_heeft gekregen, roept u de sjabloon als volgt aan:

```azurecli
az batch job create --template job-ffmpeg.json
```

Net als voorheen vraagt de CLI u om waarden voor de parameters op te geven. U de parameters ook in een JSON-bestand opgeven.

### <a name="use-templates-in-batch-explorer"></a>Sjablonen gebruiken in Batch Explorer

U een Batch CLI-sjabloon uploaden naar de [Batch Explorer-bureaubladtoepassing](https://github.com/Azure/BatchExplorer) (voorheen BatchLabs genoemd) om een batchgroep of -taak te maken. U ook kiezen uit vooraf gedefinieerde pool- en taaksjablonen in de Batch Explorer-galerie.

Een sjabloon uploaden:

1. Selecteer in Batch Explorer **galerielokale** > **sjablonen**.

2. Selecteer of sleep en neerzet een lokale pool of taaksjabloon.

3. Selecteer **Deze sjabloon gebruiken**en volg de aanwijzingen op het scherm.

## <a name="file-groups-and-file-transfer"></a>Bestandsgroepen en bestandsoverdracht

Voor de meeste taken en taken zijn invoerbestanden vereist en moeten uitvoerbestanden worden geproduceerd. Meestal worden invoerbestanden en uitvoerbestanden overgedragen, van de client naar het knooppunt, of van het knooppunt naar de client. De Azure Batch CLI-extensie abstrahert bestandsoverdracht en maakt gebruik van het opslagaccount dat u met elk Batch-account koppelen.

Een bestandsgroep komt overeen met een container die is gemaakt in het Azure-opslagaccount. De bestandsgroep kan submappen hebben.

De Batch CLI-extensie biedt opdrachten om bestanden van client naar een opgegeven bestandsgroep te uploaden en bestanden van de opgegeven bestandsgroep naar een client te downloaden.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Met groep- en taaksjablonen kunnen bestanden die zijn opgeslagen in bestandsgroepen worden opgegeven voor kopiëren naar poolknooppunten of knooppunten van de groep uit de groep terug naar een bestandsgroep. In de eerder opgegeven taaksjabloon wordt bijvoorbeeld de *ffmpeg-invoer* van de bestandsgroep opgegeven voor de taakfabriek als de locatie van de bronvideobestanden die naar het knooppunt zijn gekopieerd voor transcodering. De *bestandsgroep ffmpeg-uitvoer* is de locatie waar de getranscodeerde uitvoerbestanden worden gekopieerd van het knooppunt dat elke taak uitvoert.

## <a name="summary"></a>Samenvatting

Ondersteuning voor sjabloon en bestandsoverdracht is momenteel alleen toegevoegd aan de Azure CLI. Het doel is om het publiek uit te breiden dat Batch kan gebruiken voor gebruikers die geen code hoeven te ontwikkelen met behulp van de Batch API's, zoals onderzoekers en IT-gebruikers. Zonder codering kunnen gebruikers met kennis van Azure, Batch en de toepassingen die door Batch worden uitgevoerd, sjablonen maken voor het maken van groepen en het maken van banen. Met sjabloonparameters kunnen gebruikers zonder gedetailleerde kennis van Batch en de toepassingen de sjablonen gebruiken.

Probeer de batch-extensie voor de Azure CLI en geef ons feedback of suggesties, hetzij in de opmerkingen voor dit artikel of via de [Batch Community repo](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Volgende stappen

- Gedetailleerde documentatie over installatie en gebruik, voorbeelden en broncode zijn beschikbaar in de [Azure GitHub-repo.](https://github.com/Azure/azure-batch-cli-extensions)

- Meer informatie over het gebruik van [Batch Explorer](https://github.com/Azure/BatchExplorer) om batchbronnen te maken en te beheren.
