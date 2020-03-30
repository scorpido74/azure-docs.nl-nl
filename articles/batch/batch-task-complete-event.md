---
title: Volledige gebeurtenis Azure Batch-taak
description: Referentie voor batchtaak volledige gebeurtenis. Deze gebeurtenis wordt uitgezonden zodra een taak is voltooid, ongeacht de exitcode.
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
ms.openlocfilehash: 0fee5f071d5c7005e466bf4c3d0c1d0a7db24731
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022916"
---
# <a name="task-complete-event"></a>Gebeurtenis taak voltooid

 Deze gebeurtenis wordt uitgezonden zodra een taak is voltooid, ongeacht de exitcode. Deze gebeurtenis kan worden gebruikt om de duur van een taak te bepalen, waar de taak is uitgevoerd en of deze opnieuw is geprobeerd.


 In het volgende voorbeeld wordt de hoofdtekst van een taakgebeurtenis weergegeven.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`jobId`|Tekenreeks|De id van de taak die de taak bevat.|
|`id`|Tekenreeks|De id van de taak.|
|`taskType`|Tekenreeks|Het type taak. Dit kan 'JobManager' zijn die aangeeft dat het een taak voor jobmanager is of 'Gebruiker' die aangeeft dat het geen taak voor jobmanager is. Deze gebeurtenis wordt niet uitgezonden voor taakvoorbereidingstaken, taakreleasetaken of starttaken.|
|`systemTaskVersion`|Int32|Dit is de interne retry teller op een taak. Intern kan de Batch-service een taak opnieuw proberen om tijdelijke problemen te verwerken. Deze problemen kunnen interne planningsfouten of pogingen omvatten om te herstellen van compute nodes in een slechte staat.|
|[`nodeInfo`](#nodeInfo)|Complex Type|Bevat informatie over het rekenknooppunt waarop de taak is uitgevoerd.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Complex Type|Hiermee geeft u op dat de taak een taak met meerdere instanties is waarvoor meerdere rekenknooppunten nodig zijn.  Zie [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) voor meer informatie.|
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
|`numberOfInstances`|Int32|Het aantal compute nodes dat vereist is voor de taak.|

###  <a name="constraints"></a><a name="constraints"></a>Beperkingen

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Het maximum aantal keren dat de taak opnieuw wordt geprobeerd. De batchservice probeert een taak opnieuw als de exitcode niet-nul is.<br /><br /> Houd er rekening mee dat deze waarde specifiek het aantal nieuwe pogingen regelt. De batchservice probeert de taak één keer uit en kan deze limiet vervolgens opnieuw proberen. Als het maximale aantal pogingen opnieuw proberen 3 is, probeert Batch een taak tot 4 keer (één eerste poging en 3 nieuwe pogingen).<br /><br /> Als het maximale aantal nieuwe try's 0 is, probeert de batchservice taken niet opnieuw.<br /><br /> Als het maximale aantal nieuwe pogingen -1 is, worden taken zonder limiet opnieuw geprobeerd door de batchservice.<br /><br /> De standaardwaarde is 0 (geen nieuwe pogingen).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>uitvoeringInfo

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`startTime`|DateTime|Het tijdstip waarop de taak is gestart. 'Running' komt overeen met de **uitvoeringsstatus,** dus als de taak resourcebestanden of toepassingspakketten opgeeft, geeft de begintijd de tijd weer waarop de taak is begonnen met het downloaden of implementeren van deze bestanden.  Als de taak opnieuw is gestart of opnieuw is geprobeerd, is dit het meest recente tijdstip waarop de taak is gestart.|
|`endTime`|DateTime|Het tijdstip waarop de taak is voltooid.|
|`exitCode`|Int32|De exitcode van de taak.|
|`retryCount`|Int32|Het aantal keren dat de taak opnieuw is geprobeerd door de batchservice. De taak wordt opnieuw geprobeerd als deze wordt afgesloten met een niet-nulexitcode, tot de opgegeven MaxTaskRetryCount.|
|`requeueCount`|Int32|Het aantal keren dat de taak opnieuw in de wachtrij is geplaatst door de batchservice als gevolg van een gebruikersverzoek.<br /><br /> Wanneer de gebruiker knooppunten uit een groep verwijdert (door de grootte van de groep te wijzigen of te verkleinen) of wanneer de taak wordt uitgeschakeld, kan de gebruiker opgeven dat het uitvoeren van taken op de knooppunten opnieuw in de wachtrij wordt geplaatst voor uitvoering. Deze telling houdt bij hoe vaak de taak om deze redenen opnieuw in de wachtrij is geplaatst.|
