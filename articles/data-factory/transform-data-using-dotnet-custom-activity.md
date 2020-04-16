---
title: Aangepaste activiteiten in een pijplijn gebruiken
description: Meer informatie over het maken van aangepaste activiteiten en het gebruik ervan in een Azure Data Factory-pijplijn.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 74e381a9ad32acdaa8cbb719824d74ca6d339f30
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418946"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-use-custom-activities.md)
> * [Huidige versie](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Er zijn twee soorten activiteiten die u gebruiken in een Azure Data Factory-pijplijn.

- [Activiteiten voor gegevensverplaatsing](copy-activity-overview.md) om gegevens te verplaatsen tussen [ondersteunde bron- en sinkgegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats).
- [Gegevenstransformatieactiviteiten](transform-data.md) om gegevens te transformeren met behulp van compute services zoals Azure HDInsight, Azure Batch en Azure Machine Learning.

Als u gegevens wilt verplaatsen naar/van een gegevensarchief dat Gegevensfabriek niet ondersteunt, of om gegevens te transformeren/verwerken op een manier die niet wordt ondersteund door Data Factory, u een **aangepaste activiteit** maken met uw eigen gegevensverplaatsing of transformatielogica en de activiteit in een pijplijn gebruiken. Met de aangepaste activiteit wordt de aangepaste codelogica uitgevoerd op een **Azure Batch-groep** van virtuele machines.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zie de volgende artikelen als u nieuw bent bij de Azure Batch-service:

* [Basisbeginselen van Azure Batch](../batch/batch-technical-overview.md) voor een overzicht van de Azure Batch-service.
* De cmdlet van [Nieuw-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) om een Azure Batch-account (of) [Azure-portal](../batch/batch-account-create-portal.md) te maken om het Azure Batch-account te maken met Azure-portal. Zie PowerShell gebruiken om het artikel [over Azure Batch-account te beheren](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) voor gedetailleerde instructies over het gebruik van de cmdlet.
* De cmdlet [nieuw-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) om een Azure Batch-pool te maken.

## <a name="azure-batch-linked-service"></a>Gekoppelde Azure Batch-service

In de volgende JSON wordt een voorbeeld van azure batch-gekoppelde service gedefinieerd. Zie [Compute-omgevingen die worden ondersteund door Azure Data Factory voor](compute-linked-services.md) meer informatie

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Zie [Artikel Gekoppelde services berekenen](compute-linked-services.md) voor meer informatie over azure batch-gekoppelde service.

## <a name="custom-activity"></a>Aangepaste activiteit

In het volgende JSON-fragment wordt een pijplijn gedefinieerd met een eenvoudige aangepaste activiteit. De activiteitsdefinitie heeft een verwijzing naar de gekoppelde Azure Batch-service.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

In dit voorbeeld is helloworld.exe een aangepaste toepassing die is opgeslagen in de map customactv2/helloworld van het Azure Storage-account dat wordt gebruikt in de resourceLinkedService. Met de aangepaste activiteit wordt deze aangepaste toepassing ingediend die moet worden uitgevoerd op Azure Batch. U de opdracht vervangen door een voorkeurstoepassing die kan worden uitgevoerd op het doelbewerkingssysteem van de Azure Batch Pool-knooppunten.

In de volgende tabel worden namen en beschrijvingen beschreven van eigenschappen die specifiek zijn voor deze activiteit.

| Eigenschap              | Beschrijving                              | Vereist |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Naam van de activiteit in de pijplijn     | Ja      |
| description           | Tekst waarin wordt beschreven wat de activiteit doet.  | Nee       |
| type                  | Voor aangepaste activiteit is het activiteitstype **Aangepast**. | Ja      |
| linkedServiceName     | Gekoppelde service aan Azure Batch. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service.  | Ja      |
| command               | Opdracht voor de aangepaste toepassing die moet worden uitgevoerd. Als de toepassing al beschikbaar is op het Azure Batch Pool-knooppunt, kunnen de resourceLinkedService en folderPath worden overgeslagen. U bijvoorbeeld de opdracht `cmd /c dir`opgeven die native wordt ondersteund door het Windows Batch Pool-knooppunt. | Ja      |
| resourceLinkedService | Azure Storage Linked Service naar het opslagaccount waar de aangepaste toepassing is opgeslagen | Geen &#42;       |
| folderPath            | Pad naar de map van de aangepaste toepassing en alle afhankelijkheden<br/><br/>Als u afhankelijkheden hebt die zijn opgeslagen in submappen - dat wil zeggen in een hiërarchische mapstructuur onder *folderPath* - wordt de mapstructuur momenteel afgevlakt wanneer de bestanden worden gekopieerd naar Azure Batch. Dat wil zeggen, alle bestanden worden gekopieerd naar een enkele map zonder submappen. Als u dit gedrag wilt omzeilen, u overwegen de bestanden te comprimeren, het gecomprimeerde bestand te kopiëren en het vervolgens uit te pakken met aangepaste code op de gewenste locatie. | Geen &#42;       |
| referentieobjecten      | Een array van bestaande Linked Services en Datasets. De gekoppelde services en gegevenssets worden doorgegeven aan de aangepaste toepassing in JSON-indeling, zodat uw aangepaste code kan verwijzen naar bronnen van de gegevensfabriek | Nee       |
| extendedProperties extendedProperties extendedProperties extended    | Door de gebruiker gedefinieerde eigenschappen die kunnen worden doorgegeven aan de aangepaste toepassing in JSON-indeling, zodat uw aangepaste code kan verwijzen naar extra eigenschappen | Nee       |
| retentionTimeInDays | De bewaartijd voor de bestanden die zijn ingediend voor aangepaste activiteit. De standaardwaarde is 30 dagen. | Nee |

&#42; De `resourceLinkedService` `folderPath` eigenschappen en moeten beide worden opgegeven of beide worden weggelaten.

> [!NOTE]
> Als u gekoppelde services als referentieobjecten in aangepaste activiteit doorgeeft, is het een goede beveiligingspraktijk om een gekoppelde service met Azure Key Vault (omdat deze geen beveiligde tekenreeksen bevat) door te geven en de referenties met behulp van geheime naam rechtstreeks uit Key Vault uit de code op te halen. U [hier](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) een voorbeeld vinden dat verwijst naar de gekoppelde service met AKV, de referenties uit Key Vault ophaalt en vervolgens toegang krijgt tot de opslag in de code.

## <a name="custom-activity-permissions"></a>Aangepaste activiteitsmachtigingen

Met de aangepaste activiteit wordt het Azure Batch-account voor automatische gebruikers ingesteld *op niet-beheerderstoegang met taakbereik* (de standaardspecificatie voor automatische gebruikers). U het machtigingsniveau van het automatisch gebruikersaccount niet wijzigen. Zie Taken uitvoeren onder gebruikersaccounts in Batch voor meer [informatie. Accounts voor automatische gebruikers](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Opdrachten uitvoeren

U een opdracht rechtstreeks uitvoeren met aangepaste activiteit. In het volgende voorbeeld wordt de opdracht 'echo hello world' uitgevoerd op de doelknooppunten van Azure Batch Pool en wordt de uitvoer afgedrukt op stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Objecten en eigenschappen doorgeven

In dit voorbeeld ziet u hoe u de referentieobjecten en extendedProperties gebruiken om gegevensfabrieksobjecten en door de gebruiker gedefinieerde eigenschappen door te geven aan uw aangepaste toepassing.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Wanneer de activiteit wordt uitgevoerd, worden referentieobjecten en extendedProperties opgeslagen in volgende bestanden die zijn geïmplementeerd in dezelfde uitvoeringsmap van de SampleApp.exe:

- `activity.json`

  Slaat extendedProperties en eigenschappen van de aangepaste activiteit op.

- `linkedServices.json`

  Hiermee slaat u een array met Gekoppelde Services op die is gedefinieerd in de eigenschap referenceObjects.

- `datasets.json`

  Hiermee slaat u een array met gegevenssets op die zijn gedefinieerd in de eigenschap referenceObjects.

Onderstaande voorbeeldcode laat zien hoe de SampleApp.exe toegang heeft tot de vereiste informatie uit JSON-bestanden:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Uitvoeringsuitvoer ophalen

U een pijplijnuitvoeren starten met de volgende powershell-opdracht:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Wanneer de pijplijn wordt uitgevoerd, u de uitvoer uitvoeren controleren met behulp van de volgende opdrachten:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

De **stdout** en **stderr** van uw aangepaste toepassing worden opgeslagen in de **adfjobs-container** in de Azure Storage Linked Service die u hebt gedefinieerd bij het maken van Azure Batch Linked Service met een GUID van de taak. U het gedetailleerde pad ophalen uit de uitvoer activiteitsrun zoals weergegeven in het volgende fragment:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Als u de inhoud van stdout.txt wilt gebruiken in downstream-activiteiten, u het pad\@naar het bestand stdout.txt in expressie activiteit('MyCustomActivity')output.outputs[0] krijgen.

> [!IMPORTANT]
> - De activity.json, linkedServices.json en datasets.json worden opgeslagen in de runtime-map van de batchtaak. In dit voorbeeld worden de activity.json, linkedServices.json en datasets.json opgeslagen in `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` pad. Indien nodig moet u ze apart opruimen.
> - Voor Linked Services die gebruikmaken van de Runtime voor zelfgehoste integratie, worden de gevoelige informatie zoals sleutels of wachtwoorden versleuteld door de Runtime voor zelfgehoste integratie om ervoor te zorgen dat de referentie blijft in de door de klant gedefinieerde privénetwerkomgeving. Sommige gevoelige velden kunnen ontbreken wanneer u op deze manier wordt verwezen naar uw aangepaste toepassingscode. Gebruik SecureString in extendedProperties in plaats van indien nodig Linked Service-referentie te gebruiken.

## <a name="pass-outputs-to-another-activity"></a>Uitvoer doorgeven aan een andere activiteit

U aangepaste waarden van uw code in een aangepaste activiteit terugsturen naar Azure Data Factory. U dit doen `outputs.json` door ze te schrijven in van uw aanvraag. Data Factory kopieert `outputs.json` de inhoud van en voegt deze `customOutput` toe aan de activiteitsuitvoer als de waarde van de eigenschap. (De groottelimiet is 2 MB.) Als u de inhoud `outputs.json` van downstream-activiteiten wilt consumeren, `@activity('<MyCustomActivity>').output.customOutput`u de waarde krijgen met behulp van de expressie.

## <a name="retrieve-securestring-outputs"></a>SecureString-uitvoer ophalen

Gevoelige eigenschapswaarden die zijn aangeduid als type *SecureString*, zoals weergegeven in sommige voorbeelden in dit artikel, worden gemaskeerd op het tabblad Controle in de gebruikersinterface van de gegevensfabriek.  Bij daadwerkelijke pijplijnuitvoering wordt een eigenschap *SecureString* echter geserialiseerd als JSON in het `activity.json` bestand als platte tekst. Bijvoorbeeld:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Deze serialisatie is niet echt veilig, en is niet bedoeld om veilig te zijn. De bedoeling is om te hinten naar Data Factory om de waarde te maskeren in het tabblad Controle.

Als u de eigenschappen van type *SecureString* wilt openen vanuit een aangepaste activiteit, leest u het `activity.json` bestand dat in dezelfde map wordt geplaatst als uw . EXE, deserialiseren van de JSON en vervolgens toegang krijgen tot de eigenschap JSON (extendedProperties => [propertyName] => waarde).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>V2 Aangepaste activiteit en punt1 -activiteit (aangepast) vergelijken

In versie 1 van Azure Data Factory implementeert u een (Aangepaste) DotNet-activiteit door `Execute` een `IDotNetActivity` .NET Class Library-project te maken met een klasse die de methode van de interface implementeert. De gekoppelde services, gegevenssets en uitgebreide eigenschappen in de JSON-payload van een (aangepaste) DotNet-activiteit worden doorgegeven aan de uitvoeringsmethode als sterk getypte objecten. Zie [(Aangepast) DotNet in versie 1](v1/data-factory-use-custom-activities.md)voor meer informatie over het gedrag van versie 1. Vanwege deze implementatie moet uw dotnetactiviteitscode van versie 1 zich richten op .NET Framework 4.5.2. De dotnetactiviteit van versie 1 moet ook worden uitgevoerd op Azure Batch Pool-knooppunten in Windows.

In de aangepaste activiteit Azure Data Factory V2 hoeft u geen .NET-interface te implementeren. U nu rechtstreeks opdrachten, scripts en uw eigen aangepaste code uitvoeren, gecompileerd als uitvoerbaar. Om deze implementatie te `Command` configureren, geeft `folderPath` u de eigenschap samen met de eigenschap op. De aangepaste activiteit uploadt de uitvoerbare `folderpath` en de afhankelijkheden ervan naar en voert de opdracht voor u uit.

De gekoppelde services, gegevenssets (gedefinieerd in referentieobjecten) en Uitgebreide eigenschappen die zijn gedefinieerd in de JSON-payload van een gegevensfabriek v2 aangepaste activiteit, kunnen worden geopend door uw uitvoerbare als JSON-bestanden. U hebt toegang tot de vereiste eigenschappen met behulp van een JSON serialisator zoals weergegeven in het vorige sampleapp.exe-codevoorbeeld.

Met de wijzigingen die zijn geïntroduceerd in de aangepaste activiteit van Data Factory V2, u uw aangepaste codelogica in uw voorkeurstaal schrijven en uitvoeren op Windows- en Linux-besturingssystemen die worden ondersteund door Azure Batch.

In de volgende tabel worden de verschillen beschreven tussen de aangepaste activiteit gegevensfabriek V2 en de DotNet-activiteit gegevensfabriek (Aangepast):

|Verschillen      | Aangepaste activiteit      | versie 1 (Aangepaste) DotNet-activiteit      |
| ---- | ---- | ---- |
|Hoe aangepaste logica wordt gedefinieerd      |Door een uitvoerbare      |Door het implementeren van een .NET DLL      |
|Uitvoeringsomgeving van de aangepaste logica      |Windows of Linux      |Windows (.NET Framework 4.5.2)      |
|Scripts uitvoeren      |Ondersteunt het rechtstreeks uitvoeren van scripts (bijvoorbeeld "cmd /c echo hello world" op Windows VM)      |Vereist implementatie in de .NET DLL      |
|Gegevensset vereist      |Optioneel      |Verplicht om activiteiten te ketenen en informatie door te geven      |
|Informatie doorgeven van activiteit naar aangepaste logica      |Via ReferenceObjects (LinkedServices en Datasets) en ExtendedProperties (aangepaste eigenschappen)      |Via ExtendedProperties (aangepaste eigenschappen), Invoer- en uitvoergegevenssets      |
|Informatie ophalen in aangepaste logica      |Parses activity.json, linkedServices.json en datasets.json opgeslagen in dezelfde map van de uitvoerbare      |Via .NET SDK (.NET Frame 4.5.2)      |
|Logboekregistratie      |Schrijft rechtstreeks naar STDOUT      |Logger implementeren in .NET DLL      |

Als u de bestaande .NET-code hebt geschreven voor een dotnetactiviteit van versie 1 (Aangepast), moet u uw code wijzigen om te kunnen werken met de huidige versie van de aangepaste activiteit. Werk uw code bij door deze richtlijnen op hoog niveau te volgen:

  - Wijzig het project van een .NET-klassebibliotheek in een Console-app.
  - Start uw toepassing `Main` met de methode. De `Execute` methode `IDotNetActivity` van de interface is niet langer nodig.
  - Lees en ontleden de Gekoppelde Services, datasets en activiteit met een JSON-serialisator en niet als sterk getypte objecten. Geef de waarden van vereiste eigenschappen door aan uw belangrijkste aangepaste codelogica. Raadpleeg de voorgaande SampleApp.exe-code als voorbeeld.
  - Het loggerobject wordt niet meer ondersteund. Uitvoer van uw uitvoerbare kan worden afgedrukt op de console en wordt opgeslagen op stdout.txt.
  - Het NuGet-pakket microsoft.azure.Management.DataFactories is niet langer vereist.
  - Compileer uw code, upload de uitvoerbare en de afhankelijkheden `folderPath` ervan naar Azure Storage en definieer het pad in de eigenschap.

Zie Voorbeeld van aangepaste [activiteit gegevensfabriek](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)Gebruiken aangepaste activiteiten in een Azure [Data Factory-pijplijn](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) voor een volledig voorbeeld van de manier waarop de end-to-end DLL- en pijplijnvoorbeeld beschreven in het artikel Data Factory-versie 1 gebruiken Aangepaste activiteiten in een Azure Data Factory-pijplijn kunnen worden herschreven als aangepaste activiteit in de gegevensfabriek.

## <a name="auto-scaling-of-azure-batch"></a>Automatisch schalen van Azure Batch

U ook een Azure Batch-groep maken met **functie voor automatisch schalen.** U bijvoorbeeld een azure batchpool maken met 0 speciale VM's en een formule voor automatisch schalen op basis van het aantal lopende taken.

De voorbeeldformule hier bereikt het volgende gedrag: Wanneer de groep in eerste instantie wordt gemaakt, begint deze met 1 VM. $PendingTasks statistiek bepaalt het aantal taken in de status uitvoeren + actief (in de wachtrij). De formule vindt het gemiddelde aantal lopende taken in de laatste 180 seconden en stelt TargetDedicated dienovereenkomstig in. Het zorgt ervoor dat TargetDedicated nooit verder gaat dan 25 VM's. Dus, als nieuwe taken worden ingediend, groep groeit automatisch en als taken worden voltooid, VM's worden gratis een voor een en de autoscaling krimpt die VM's. startNumberOfVm's en maxNumberofVM's kunnen worden aangepast aan uw behoeften.

Formule automatisch schalen:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zie [Compute-knooppunten automatisch schalen in een Azure Batch-groep](../batch/batch-automatic-scaling.md) voor meer informatie.

Als de groep het [standaardautoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)gebruikt, kan het 15-30 minuten duren voordat de batchservice de VM voorbereidt voordat de aangepaste activiteit wordt uitgevoerd. Als de groep een andere autoScaleEvaluationInterval gebruikt, kan de batchservice autoScaleEvaluationInterval + 10 minuten inbeslagnemen.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
