---
title: Tuimelvenstertriggers maken in Azure Data Factory
description: Meer informatie over het maken van een trigger in Azure Data Factory waarop een pijplijn in een tuimelvenster wordt uitgevoerd.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 97c8f8a5bb2111264e9459a7d2128c1ab7c2503d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414433"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Een trigger maken die een pijplijn uitvoert op een tumblingvenster
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel vindt u stappen om een tuimelende venstertrigger te maken, te starten en te controleren. Zie [Pijplijnuitvoering en triggers](concepts-pipeline-execution-triggers.md)voor algemene informatie over triggers en de ondersteunde typen.

Tumblingvenstertriggers zijn triggers die vanaf een opgegeven begintijd worden geactiveerd met een periodiek tijdsinterval en die hun status behouden. Tumblingvensters bestaan uit een reeks niet-overlappende en aaneengesloten tijdsintervallen van vaste duur. Een tuimelende venstertrigger heeft een één-op-één relatie met een pijplijn en kan alleen verwijzen naar een enkelvoudpijplijn.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

1. Als u een tuimelvenstertrigger wilt maken in de gebruikersinterface van de gegevensfabriek, selecteert u het tabblad **Triggers** en selecteert u **Nieuw**. 
1. Nadat het triggerconfiguratievenster is geopend, selecteert u **Tumbling Window**en definieert u vervolgens de eigenschappen van de tuimelvenstertrigger. 
1. Selecteer **Opslaan** als u klaar bent.

![Een tuimelende venstertrigger maken in de Azure-portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Eigenschappen van het type van tumbling window trigger

Een tuimelvenster heeft de volgende triggertype-eigenschappen:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

De volgende tabel geeft een overzicht op hoog niveau van de belangrijkste JSON-elementen die verband houden met herhaling en het plannen van een tuimelende venstertrigger:

| JSON-element | Beschrijving | Type | Toegestane waarden | Vereist |
|:--- |:--- |:--- |:--- |:--- |
| **Type** | Het type van de trekker. Het type is de vaste waarde "TumblingWindowTrigger". | Tekenreeks | "TumblingWindowTrigger" | Ja |
| **runtimeState** | De huidige status van de triggerruntijd.<br/>**Opmerking:** Dit \<element wordt gelezenAlleen>. | Tekenreeks | 'Gestart', 'Gestopt', 'Uitgeschakeld' | Ja |
| **Frequentie** | Een tekenreeks die de frequentie-eenheid (minuten of uren) vertegenwoordigt waarop de trigger terugkeert. Als de **waarden van de begindatum** gedetailleerder zijn dan de **frequentiewaarde,** worden de **begindatums** in aanmerking genomen wanneer de venstergrenzen worden berekend. Als de **frequentiewaarde** bijvoorbeeld per uur is en de **waarde startTime** 2017-09-01T10:10:10Z, is het eerste venster (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Tekenreeks | "minuut," "uur"  | Ja |
| **Interval** | Een positief geheel getal dat het interval voor de waarde **frequency** aangeeft. Het bepaalt hoe vaak de trigger wordt uitgevoerd. Als het **interval** bijvoorbeeld 3 is en de **frequentie** 'uur' is, komt de trigger elke 3 uur terug. <br/>**Opmerking:** Het minimale vensterinterval is 5 minuten. | Geheel getal | Een positief geheel getal. | Ja |
| **startTime**| Het eerste voorkomen, dat in het verleden kan zijn. Het eerste triggerinterval is (**startTime**, **startTime** + **interval**). | DateTime | Een datumtijdwaarde. | Ja |
| **endTime**| Het laatste voorval, dat in het verleden kan zijn. | DateTime | Een datumtijdwaarde. | Ja |
| **Vertraging** | De hoeveelheid tijd om de start van de gegevensverwerking voor het venster uit te stellen. De pijplijnrun wordt gestart na de verwachte uitvoeringstijd plus de hoeveelheid **vertraging**. De **vertraging** bepaalt hoe lang de trigger voorbij de vervaldatum wacht voordat een nieuwe run wordt geactiveerd. De **vertraging** verandert niets aan de **starttijd van**het venster . Een **vertragingswaarde** van 00:10:00 betekent bijvoorbeeld een vertraging van 10 minuten. | Periode<br/>(hh:mm:ss)  | Een tijdspannewaarde waarbij de standaardwaarde 00:00:00 is. | Nee |
| **maxConcurrency max.** | Het aantal gelijktijdige triggerruns dat wordt geactiveerd voor vensters die klaar zijn. Bijvoorbeeld, om terug te vullen per uur loopt voor gisteren resulteert in 24 ramen. Als **maxConcurrency** = 10, trigger gebeurtenissen worden alleen afgevuurd voor de eerste 10 ramen (00:00-01:00 - 09:00-10:00). Nadat de eerste 10 geactiveerde pijplijnruns zijn voltooid, worden triggerruns geactiveerd voor de volgende 10 vensters (10:00-11:00 - 19:00-20:00). Doorgaan met dit voorbeeld van **maxConcurrency** = 10, als er 10 vensters klaar zijn, zijn er 10 totale pijplijnruns. Als er slechts 1 venster klaar is, is er slechts 1 pijplijn run. | Geheel getal | Een geheel getal tussen 1 en 50. | Ja |
| **retryPolicy: Aantal** | Het aantal nieuwe pogingen vóór de pijplijnwordt gemarkeerd als 'Mislukt'.  | Geheel getal | Een geheel getal, waarbij de standaardinstelling 0 is (geen nieuwe pogingen). | Nee |
| **retryPolicy: intervalInSeconds** | De vertraging tussen pogingen opnieuw proberen die in seconden worden opgegeven. | Geheel getal | Het aantal seconden, waarbij de standaardwaarde 30 is. | Nee |
| **dependsOn: type** | Het type TumblingWindowTriggerReference. Vereist als een afhankelijkheid is ingesteld. | Tekenreeks |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nee |
| **dependsOn: grootte** | De grootte van het tumbling venster van de afhankelijkheid. | Periode<br/>(hh:mm:ss)  | Een positieve tijdsperiodewaarde waarbij de standaardwaarde de venstergrootte van de onderliggende trigger is  | Nee |
| **dependsOn: offset** | De verschuiving van de afhankelijkheidstrigger. | Periode<br/>(hh:mm:ss) |  Een tijdspannewaarde die negatief moet zijn in een zelfafhankelijkheid. Als er geen waarde is opgegeven, is het venster hetzelfde als de trigger zelf. | Zelfafhankelijkheid: Ja<br/>Overig: Nee  |

### <a name="windowstart-and-windowend-system-variables"></a>WindowsStart- en WindowEnd-systeemvariabelen

U de **windowse** en **WindowEnd-systeemvariabelen** van de tuimelvenstertrigger gebruiken in de **pijplijndefinitie** (dat wil zeggen voor een deel van een query). Geef de systeemvariabelen als parameters door aan uw pijplijn in de **triggerdefinitie.** In het volgende voorbeeld ziet u hoe u deze variabelen als parameters doorgeven:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Als u de variabele waarden van het **WindowsStart-** en **WindowEnd-systeem** wilt gebruiken in de pijplijndefinitie, gebruikt u de parameters 'MyWindowStart' en 'MyWindowEnd' dienovereenkomstig.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Uitvoeringsvolgorde van vensters in een backfillscenario
Wanneer er meerdere vensters zijn voor uitvoering (vooral in een backfill-scenario), is de uitvoeringsvolgorde voor vensters deterministisch, van oudste tot nieuwste intervallen. Dit gedrag kan op dit moment niet worden aangepast.

### <a name="existing-triggerresource-elements"></a>Bestaande TriggerResource-elementen
De volgende punten zijn van toepassing op bestaande **TriggerResource-elementen:**

* Als de waarde voor het **frequentieelement** (of venstergrootte) van de trigger verandert, wordt de status van de vensters die al zijn *verwerkt, niet* opnieuw ingesteld. De trigger blijft branden voor de ramen van het laatste venster dat het uitgevoerd met behulp van de nieuwe venstergrootte.
* Als de waarde voor het **endTime-element** van de trigger verandert (toegevoegd of bijgewerkt), wordt de status van de vensters die al zijn *verwerkt, niet* opnieuw ingesteld. De trigger eert de nieuwe **endTime-waarde.** Als de nieuwe **endTime-waarde** vóór de vensters is die al zijn uitgevoerd, wordt de trigger gestopt. Anders stopt de trigger wanneer de nieuwe **endTime-waarde** wordt aangetroffen.

### <a name="tumbling-window-trigger-dependency"></a>Tumbling venster trigger afhankelijkheid

Als u ervoor wilt zorgen dat een tuimelende venstertrigger pas wordt uitgevoerd na de succesvolle uitvoering van een andere tuimelende venstertrigger in de gegevensfabriek, [maakt u een tuimelende venstertriggerafhankelijkheid.](tumbling-window-trigger-dependency.md) 

## <a name="sample-for-azure-powershell"></a>Voorbeeld voor Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze sectie ziet u hoe u Azure PowerShell gebruiken om een trigger te maken, te starten en te controleren.

1. Maak een JSON-bestand met de naam **MyTrigger.json** in de map C:\ADFv2QuickStartPSH\ met de volgende inhoud:

    > [!IMPORTANT]
    > Voordat u het JSON-bestand opslaat, stelt u de waarde van het **element startTime** in op de huidige UTC-tijd. Stel de waarde van het **endTime-element** in op een uur voorbij de huidige UTC-tijd.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Maak een trigger met de cmdlet **Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Controleer of de status van de trigger wordt **gestopt** met de cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Start de trigger met de **cmdlet Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Controleer of de status van de trigger wordt **gestart** met de cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Download de triggerruns in Azure PowerShell met de **cmdlet Get-AzDataFactoryV2TriggerRun.** Voer de volgende opdracht periodiek uit om informatie over de trigger-uitvoering en uitvoer. Update de **TriggerRunStartedAfter** en **TriggerRunStartedVoordat** waarden overeenkomen met de waarden in de triggerdefinitie:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Zie [Monitorpijplijn wordt uitgevoerd](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)als u triggerruns en pijplijnuitvoeringen in de Azure-portal wilt controleren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Pijplijnuitvoering en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor gedetailleerde informatie over triggers.
* [Een afhankelijkheid voor een tumblingvenstertrigger maken](tumbling-window-trigger-dependency.md)
