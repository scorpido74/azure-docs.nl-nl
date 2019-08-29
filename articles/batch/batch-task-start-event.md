---
title: Gebeurtenis Azure Batch begin van taak | Microsoft Docs
description: Verwijzing voor de begin gebeurtenis van de batch taak.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: efad9e71b986156c6d8e95208d50ac8d5a4d7e7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094353"
---
# <a name="task-start-event"></a>Gebeurtenis taak starten

 Deze gebeurtenis wordt verzonden zodra een taak is gepland om te starten op een reken knooppunt door de scheduler. Houd er rekening mee dat als de taak opnieuw wordt geprobeerd of opnieuw in de wachtrij wordt geplaatst, deze gebeurtenis opnieuw wordt verzonden voor dezelfde taak, maar de versie van het aantal nieuwe pogingen en de systeem taak wordt dienovereenkomstig bijgewerkt.


 In het volgende voor beeld ziet u de hoofd tekst van een taak begin gebeurtenis.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
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
        "retryCount": 0
    }
}
```

|De naam van element|type|Opmerkingen|
|------------------|----------|-----------|
|jobId|Tekenreeks|De id van de taak die de taak bevat.|
|id|Tekenreeks|De id van de taak.|
|taskType|Tekenreeks|Het type taak. Dit kan ' JobManager ' zijn. Dit geeft aan dat het een taak beheerder of ' gebruiker ' is die aangeeft dat het geen taak manager taak is.|
|systemTaskVersion|Int32|Dit is de interne teller voor een nieuwe poging van een taak. Intern kan de batch-service een taak opnieuw uitvoeren om tijdelijke problemen op te lossen. Deze problemen kunnen interne plannings fouten bevatten of proberen te herstellen van reken knooppunten met een onjuiste status.|
|[nodeInfo](#nodeInfo)|Complex type|Bevat informatie over het reken knooppunt waarop de taak is uitgevoerd.|
|[multiInstanceSettings](#multiInstanceSettings)|Complex type|Hiermee geeft u op dat de taak een taak met meerdere exemplaren waarvoor meerdere reken knooppunten zijn vereist.  Zie [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) voor meer informatie.|
|[standaardwaarde](#constraints)|Complex type|De uitvoerings beperkingen die van toepassing zijn op deze taak.|
|[executionInfo](#executionInfo)|Complex type|Bevat informatie over de uitvoering van de taak.|

###  <a name="nodeInfo"></a>nodeInfo

|De naam van element|type|Opmerkingen|
|------------------|----------|-----------|
|poolId|Tekenreeks|De id van de pool waarvoor de taak is uitgevoerd.|
|nodeId|Tekenreeks|De id van het knoop punt waarop de taak is uitgevoerd.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|De naam van element|type|Opmerkingen|
|------------------|----------|-----------|
|numberOfInstances|Int|Het aantal reken knooppunten dat is vereist voor de taak.|

###  <a name="constraints"></a>standaardwaarde

|De naam van element|type|Opmerkingen|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Het maximum aantal keren dat de taak opnieuw kan worden uitgevoerd. De batch-service probeert een taak opnieuw uit te proberen als de afsluit code niet gelijk is aan nul.<br /><br /> Houd er rekening mee dat deze waarde specifiek het aantal nieuwe pogingen bepaalt. De batch-service probeert de taak één keer uit te voeren en kan vervolgens de limiet opnieuw proberen. Als het maximum aantal nieuwe pogingen bijvoorbeeld 3 is, probeert batch een taak Maxi maal 4 keer uit te voeren (één eerste poging en 3 nieuwe pogingen).<br /><br /> Als het maximum aantal nieuwe pogingen 0 is, worden taken niet opnieuw geprobeerd met de batch-service.<br /><br /> Als het maximum aantal nieuwe pogingen-1 is, probeert de batch-service zonder limiet taken uit te voeren.<br /><br /> De standaard waarde is 0 (geen nieuwe pogingen).|

###  <a name="executionInfo"></a>executionInfo

|De naam van element|type|Opmerkingen|
|------------------|----------|-----------|
|retryCount|Int32|Het aantal keren dat de batch-service opnieuw is geprobeerd om de taak uit te proberen. De taak wordt opnieuw uitgevoerd als deze wordt afgesloten met een afsluit code die niet gelijk is aan nul, tot de opgegeven MaxTaskRetryCount|
