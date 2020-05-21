---
title: Controleren op taakfouten
description: Fouten om taken en taken te controleren en problemen op te lossen
author: mscurrell
ms.topic: how-to
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 368b803ae3e62c0e27da9e52d9df9842037757ff
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726618"
---
# <a name="job-and-task-error-checking"></a>Fout controle taak en taak

Er zijn verschillende fouten die kunnen optreden bij het toevoegen van taken en taken. Het detecteren van fouten voor deze bewerkingen is eenvoudig, omdat eventuele fouten direct door de API, CLI of de gebruikers interface worden geretourneerd.  Er zijn echter storingen die later kunnen optreden wanneer taken en taken worden gepland en uitgevoerd.

In dit artikel worden de fouten behandeld die zich kunnen voordoen nadat taken en taken zijn verzonden. Hierin worden de fouten vermeld en uitgelegd die moeten worden gecontroleerd en verwerkt.

## <a name="jobs"></a>Taken

Een taak is een groepering van een of meer taken, de taken die daad werkelijk de opdracht regels opgeven die moeten worden uitgevoerd.

Bij het toevoegen van een taak kunnen de volgende para meters worden opgegeven die van invloed kunnen zijn op hoe de taak kan mislukken:

- [Taak beperkingen](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - De `maxWallClockTime` eigenschap kan eventueel worden opgegeven om de maximale hoeveelheid tijd in te stellen waarmee een taak actief of actief kan zijn. Als deze wordt overschreden, wordt de taak beëindigd met de `terminateReason` eigenschap die is ingesteld in de [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) voor de taak.
- [Taak voor het voorbereiden van taken](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Indien opgegeven wordt een taak voorbereidings taak uitgevoerd wanneer een taak voor de eerste keer wordt uitgevoerd voor een taak op een knoop punt. De taak voor het voorbereiden van de taak kan mislukken, waardoor de taak niet wordt uitgevoerd en de taak niet wordt voltooid.
- [Taak voor taak release](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Er kan alleen een taak vrijgave taak worden opgegeven als een taak voorbereidings taak is geconfigureerd. Wanneer een taak wordt beëindigd, wordt de taak vrijgave uitgevoerd op elk van de groeps knooppunten waarop een taak voorbereidings taak is uitgevoerd. Een taak vrijgave taak kan mislukken, maar de taak zal nog steeds worden verplaatst naar een `completed` status.

### <a name="job-properties"></a>Taak eigenschappen

De volgende taak eigenschappen moeten worden gecontroleerd op fouten:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - De `terminateReason` eigenschap kan waarden bevatten om aan te geven dat de `maxWallClockTime` , opgegeven in de taak beperkingen, is overschreden en dat de taak is beëindigd. Het kan ook worden ingesteld om aan te geven dat een taak is mislukt als de taak eigenschap op de `onTaskFailure` juiste wijze is ingesteld.
  - De eigenschap [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) wordt ingesteld als er een plannings fout is opgetreden.
 
### <a name="job-preparation-tasks"></a>Taak voorbereidings taken

Als een taak voorbereidings taak voor een taak is opgegeven, wordt een exemplaar van die taak uitgevoerd wanneer een taak voor de taak voor het eerst op een knoop punt wordt uitgevoerd. De taak voor taak voorbereiding die is geconfigureerd voor de taak kan worden beschouwd als een taak sjabloon, waarbij meerdere taak voorbereidings taken worden uitgevoerd, tot het aantal knoop punten in een pool.

De taak exemplaren voor taak voorbereiding moeten worden gecontroleerd om te bepalen of er fouten zijn opgetreden:
- Wanneer een taak voorbereidings taak wordt uitgevoerd, wordt de taak die de taak voorbereidings taken heeft geactiveerd, verplaatst naar een [toestand](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) van `preparing` ; als de taak voorbereidings taken vervolgens mislukken, wordt de activerings taak teruggezet naar de `active` status en wordt deze niet uitgevoerd.  
- Alle exemplaren van de taak voorbereidings taak die zijn uitgevoerd, kunnen worden verkregen van de taak met behulp van de status-API van de [lijst voorbereiding en release taak](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Net als bij elke taak is er [uitvoerings informatie](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) beschikbaar met eigenschappen zoals `failureInfo` , `exitCode` en `result` .
- Als taak voorbereidings taken mislukken, worden de activerings taak taken niet uitgevoerd. de taak wordt niet voltooid en blijft actief. De pool kan worden gebruikt als er geen andere taken zijn met taken die kunnen worden gepland.

### <a name="job-release-tasks"></a>Taak release taken

Als er een taak vrijgave taak is opgegeven voor een taak, wordt een exemplaar van de taak release taak wordt beëindigd op elk van de groeps knooppunten waarop een taak voorbereidings taak is uitgevoerd.  De taak exemplaren van de taak release moeten worden gecontroleerd om te bepalen of er fouten zijn opgetreden:
- Alle exemplaren van de taak die wordt uitgevoerd, kunnen worden opgehaald uit de taak met behulp van de voor bereiding van de API- [lijst en de status van de taak release](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Net als bij elke taak is er [uitvoerings informatie](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) beschikbaar met eigenschappen zoals `failureInfo` , `exitCode` en `result` .
- Als een of meer taken voor de taak release mislukken, wordt de taak nog steeds beëindigd en verplaatst naar een `completed` status.

## <a name="tasks"></a>Taken

Taak taken kunnen om verschillende redenen mislukken:

- De opdracht regel van de taak is mislukt, retour neren met een afsluit code die niet gelijk is aan nul.
- Er zijn `resourceFiles` voor een taak opgegeven, maar er is een fout opgetreden waardoor een of meer bestanden niet zijn gedownload.
- Er zijn `outputFiles` opgegeven voor een taak, maar er is een fout opgetreden waardoor een of meer bestanden niet konden worden geüpload.
- De verstreken tijd voor de taak, opgegeven door de `maxWallClockTime` eigenschap in de taak [beperkingen](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints), is overschreden.

In alle gevallen moeten de volgende eigenschappen worden gecontroleerd op fouten en informatie over de fouten:
- De eigenschap tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) bevat meerdere eigenschappen die informatie geven over een fout. [resultaat](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) geeft aan of de taak om een of andere reden is mislukt, met `exitCode` en `failureInfo` meer informatie over de fout.
- De taak wordt altijd verplaatst naar de `completed` [status](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), ongeacht of deze is geslaagd of mislukt.

De impact van taak fouten op de taak en eventuele taak afhankelijkheden moeten worden overwogen.  De eigenschap [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) kan worden opgegeven voor een taak voor het configureren van een actie voor afhankelijkheden en voor de taak.
- Voor afhankelijkheden bepaalt [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) of de taken die afhankelijk zijn van de mislukte taak worden geblokkeerd of worden uitgevoerd.
- Voor de taak bepaalt [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) of de mislukte taak naar de taak wordt uitgeschakeld, wordt beëindigd of ongewijzigd blijft.

### <a name="task-command-line-failures"></a>Opdracht regel fouten taak

Wanneer de opdracht regel van de taak wordt uitgevoerd, wordt uitvoer geschreven naar `stderr.txt` en `stdout.txt` . Daarnaast kan de toepassing schrijven naar toepassingsspecifieke logboek bestanden.

Als het groeps knooppunt waarop een taak is uitgevoerd nog bestaat, kunnen de logboek bestanden worden verkregen en weer gegeven. De Azure Portal lijsten en kunnen bijvoorbeeld logboek bestanden voor een taak of een groeps knooppunt weer geven. Meerdere Api's staan ook toe dat taak bestanden worden weer gegeven en opgehaald, zoals [ophalen van taak](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Vanwege Pools en groeps knooppunten die regel matig tijdelijk zijn, worden de knoop punten voortdurend toegevoegd en verwijderd. het wordt aanbevolen om de logboek bestanden persistent te maken. [Taak uitvoer bestanden](https://docs.microsoft.com/azure/batch/batch-task-output-files) zijn een handige manier om logboek bestanden op te slaan in azure Storage.

### <a name="output-file-failures"></a>Fouten in het uitvoer bestand
Bij elke upload van het bestand schrijft batch twee logboek bestanden naar het reken knooppunt `fileuploadout.txt` en `fileuploaderr.txt` . U kunt deze logboek bestanden controleren om meer te weten te komen over een specifieke fout. In gevallen waarin de upload van het bestand nooit is geslaagd, bijvoorbeeld omdat de taak zelf niet kan worden uitgevoerd, zijn deze logboek bestanden niet aanwezig.  

## <a name="next-steps"></a>Volgende stappen

Controleer of uw toepassing uitgebreide fout controle implementeert. het kan van cruciaal belang zijn om problemen op te sporen en op te sporen.
