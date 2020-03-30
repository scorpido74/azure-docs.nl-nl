---
title: Begingebeurtenis Azure Batch-taak
description: Referentiegegevens voor batchtaakstartgebeurtenis. Deze gebeurtenis wordt uitgezonden zodra een taak is gepland om te starten op een compute node door de planner.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022814"
---
# <a name="task-start-event"></a>Gebeurtenis taak starten

 Deze gebeurtenis wordt uitgezonden zodra een taak is gepland om te starten op een compute node door de planner. Houd er rekening mee dat als de taak opnieuw wordt geprobeerd of opnieuw in de wachtrij wordt geplaatst, deze gebeurtenis opnieuw wordt uitgezonden voor dezelfde taak, maar dat de versie van het aantal opnieuw proberen en de versie van de systeemtaak dienovereenkomstig worden bijgewerkt.


 In het volgende voorbeeld wordt de hoofdtekst van een gebeurtenis voor het starten van taken weergegeven.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`jobId`|Tekenreeks|De id van de taak die de taak bevat.|
|`id`|Tekenreeks|De id van de taak.|
|`taskType`|Tekenreeks|Het type taak. Dit kan 'JobManager' zijn die aangeeft dat het een taak voor jobmanager is of 'Gebruiker' die aangeeft dat het geen taak voor jobmanager is.|
|`systemTaskVersion`|Int32|Dit is de interne retry teller op een taak. Intern kan de Batch-service een taak opnieuw proberen om tijdelijke problemen te verwerken. Deze problemen kunnen interne planningsfouten of pogingen omvatten om te herstellen van compute nodes in een slechte staat.|
|[`nodeInfo`](#nodeInfo)|Complex Type|Bevat informatie over het rekenknooppunt waarop de taak is uitgevoerd.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Complex Type|Hiermee geeft u op dat de taak multi-instancetaak is waarvoor meerdere rekenknooppunten nodig zijn.  Zie [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) voor meer informatie.|
|[`constraints`](#constraints)|Complex Type|De uitvoeringsbeperkingen die op deze taak van toepassing zijn.|
|[`executionInfo`](#executionInfo)|Complex Type|Bevat informatie over de uitvoering van de taak.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>knooppuntInfo

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`poolId`|Tekenreeks|De ID van de pool waarop de taak is uitgevoerd.|
|`nodeId`|Tekenreeks|De id van het knooppunt waarop de taak is uitgevoerd.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceInstellingen

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`numberOfInstances`|Int|Het aantal compute nodes dat vereist is voor de taak.|

###  <a name="constraints"></a><a name="constraints"></a>Beperkingen

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Het maximum aantal keren dat de taak opnieuw wordt geprobeerd. De batchservice probeert een taak opnieuw als de exitcode niet-nul is.<br /><br /> Houd er rekening mee dat deze waarde specifiek het aantal nieuwe pogingen regelt. De batchservice probeert de taak één keer uit en kan deze limiet vervolgens opnieuw proberen. Als het maximale aantal pogingen opnieuw proberen 3 is, probeert Batch een taak tot 4 keer (één eerste poging en 3 nieuwe pogingen).<br /><br /> Als het maximale aantal nieuwe try's 0 is, probeert de batchservice taken niet opnieuw.<br /><br /> Als het maximale aantal nieuwe pogingen -1 is, worden taken zonder limiet opnieuw geprobeerd door de batchservice.<br /><br /> De standaardwaarde is 0 (geen nieuwe pogingen).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>uitvoeringInfo

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`retryCount`|Int32|Het aantal keren dat de taak opnieuw is geprobeerd door de batchservice. De taak wordt opnieuw geprobeerd als deze wordt afgesloten met een niet-nulexitcode, tot het opgegeven MaxTaskRetryCount|
