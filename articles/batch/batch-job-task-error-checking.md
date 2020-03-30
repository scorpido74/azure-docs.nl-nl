---
title: Controleren op taak- en taakfouten - Azure Batch | Microsoft Documenten
description: Fouten om taken en taken te zoeken en problemen op te lossen
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087004"
---
# <a name="job-and-task-error-checking"></a>Taak- en taakfoutcontrole

Er zijn verschillende fouten die kunnen optreden bij het toevoegen van taken en taken. Het detecteren van fouten voor deze bewerkingen is eenvoudig omdat eventuele fouten onmiddellijk worden geretourneerd door de API, CLI of UI.  Er zijn echter fouten die later kunnen optreden wanneer taken en taken worden gepland en uitgevoerd.

Dit artikel behandelt de fouten die kunnen optreden nadat taken en taken zijn ingediend. Het geeft een lijst en legt de fouten die moeten worden gecontroleerd en behandeld.

## <a name="jobs"></a>Taken

Een taak is een groepering van een of meer taken, waarbij de taken daadwerkelijk de opdrachtregels opgeven die moeten worden uitgevoerd.

Bij het toevoegen van een taak kunnen de volgende parameters worden opgegeven die van invloed kunnen zijn op de manier waarop de taak kan mislukken:

- [Functiebeperkingen](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - De `maxWallClockTime` eigenschap kan optioneel worden opgegeven om de maximale tijd in te stellen die een taak actief of uitgevoerd kan hebben. Als deze wordt overschreden, wordt `terminateReason` de taak beëindigd met de eigenschap die is ingesteld in de [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) voor de taak.
- [Taak voor het voorbereiden van werk](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Als dit is opgegeven, wordt een taakvoorbereidingstaak uitgevoerd wanneer een taak voor een taak op een knooppunt wordt uitgevoerd. De taakvoorbereidingstaak kan mislukken, waardoor de taak niet wordt uitgevoerd en de taak niet wordt voltooid.
- [Taak taak taak voor het vrijgeven van taken](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Een taakreleasetaak kan alleen worden opgegeven als een taakvoorbereidingstaak is geconfigureerd. Wanneer een taak wordt beëindigd, wordt de taak voor taakvrijgeven uitgevoerd op elk van de poolknooppunten waar een taakvoorbereidingstaak is uitgevoerd. Een taak voor het vrijgeven van taken kan `completed` mislukken, maar de taak wordt nog steeds verplaatst naar een status.

### <a name="job-properties"></a>Functie-eigenschappen

De volgende taakeigenschappen moeten worden gecontroleerd op fouten:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - De `terminateReason` eigenschap kan waarden hebben `maxWallClockTime`om aan te geven dat de taakbeperkingen zijn overschreden en dat de taak daarom is beëindigd. Het kan ook worden ingesteld om aan `onTaskFailure` te geven dat een taak is mislukt als de taakeigenschap op de juiste manier is ingesteld.
  - De eigenschap [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) is ingesteld als er een planningsfout is opgetreden.
 
### <a name="job-preparation-tasks"></a>Taken voor het voorbereiden van werk

Als een taakvoorbereidingstaak is opgegeven voor een taak, wordt een instantie van die taak uitgevoerd wanneer een taak voor de taak voor het eerst op een knooppunt wordt uitgevoerd. De taakvoorbereidingstaak die is geconfigureerd voor de taak, kan worden beschouwd als een taaksjabloon, waarbij meerdere taakvoorbereidingstaakexemplaren worden uitgevoerd, tot het aantal knooppunten in een groep.

De taakvoorbereidingsinstanties moeten worden gecontroleerd om te bepalen of er fouten zijn gemaakt:
- Wanneer een taakvoorbereidingstaak wordt uitgevoerd, wordt de taak die [state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) de `preparing`taakvoorbereidingstaak heeft geactiveerd, verplaatst naar een status van ; Als de taakvoorbereidingstaak vervolgens mislukt, wordt `active` de triggertaak teruggezet naar de status en wordt deze niet uitgevoerd.  
- Alle exemplaren van de taakvoorbereidingstaak die zijn uitgevoerd, kunnen worden verkregen uit de taak met behulp van de API [voor lijstvoorbereiding en releasetaakstatus.](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) Zoals bij elke taak is er [uitvoeringsinformatie](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) `exitCode`beschikbaar `result`met eigenschappen zoals `failureInfo`, en .
- Als taakvoorbereidingstaken mislukken, worden de triggertaken niet uitgevoerd, wordt de taak niet voltooid en blijft deze vastlopen. De groep kan onbenut blijven als er geen andere taken zijn met taken die kunnen worden gepland.

### <a name="job-release-tasks"></a>Taken voor het vrijgeven van taken

Als een taakreleasetaak is opgegeven voor een taak, wordt een instantie van de taakreleasetaak uitgevoerd wanneer een taak wordt beëindigd op elk van de poolknooppunten waar een taakvoorbereidingstaak is uitgevoerd.  De taaktaakinstanties moeten worden gecontroleerd om te bepalen of er fouten zijn geweest:
- Alle exemplaren van de taakreleasetaak die wordt uitgevoerd, kunnen worden verkregen uit de taak met behulp van de [status voor voorbereiding en releasetaak van API-lijst](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Zoals bij elke taak is er [uitvoeringsinformatie](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) `exitCode`beschikbaar `result`met eigenschappen zoals `failureInfo`, en .
- Als een of meer taakreleasetaken mislukken, wordt de `completed` taak nog steeds beëindigd en wordt deze naar een status verplaatst.

## <a name="tasks"></a>Taken

Taaktaken kunnen om meerdere redenen mislukken:

- De taakopdrachtregel mislukt en keert terug met een niet-nulexitcode.
- Er `resourceFiles` zijn opgegeven voor een taak, maar er was een fout die betekende dat een of meer bestanden niet downloaden.
- Er `outputFiles` zijn opgegeven voor een taak, maar er was een fout waardoor een of meer bestanden niet werden geüpload.
- De verstreken tijd voor de taak, opgegeven door de `maxWallClockTime` eigenschap in de [taakbeperkingen,](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)is overschreden.

In alle gevallen moeten de volgende eigenschappen worden gecontroleerd op fouten en informatie over de fouten:
- De eigenschap taken [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) bevat meerdere eigenschappen die informatie geven over een fout. [resultaat](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) geeft aan of de taak `exitCode` `failureInfo` om welke reden dan ook is mislukt, met en meer informatie over de fout.
- De taak wordt altijd `completed` verplaatst naar de [staat,](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)onafhankelijk van of het is gelukt of mislukt.

Er moet rekening worden gehouden met de impact van taakfouten op de taak en eventuele taakafhankelijkheden.  De eigenschap [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) kan worden opgegeven voor een taak om een actie te configureren voor afhankelijkheden en voor de taak.
- Voor afhankelijkheden bepaalt [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) of de taken die afhankelijk zijn van de mislukte taak worden geblokkeerd of worden uitgevoerd.
- Voor de taak bepaalt [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) of de mislukte taak ertoe leidt dat de taak wordt uitgeschakeld, beëindigd of ongewijzigd wordt gelaten.

### <a name="task-command-line-failures"></a>Taakopdrachtregelfouten

Wanneer de taakopdrachtregel wordt uitgevoerd, `stderr.txt` `stdout.txt`wordt de uitvoer naar en . Bovendien kan de toepassing schrijven naar toepassingsspecifieke logbestanden.

Als het poolknooppunt waarop een taak is uitgevoerd nog steeds bestaat, kunnen de logboekbestanden worden verkregen en bekeken. De Azure-portal bevat bijvoorbeeld logbestanden voor een taak of een poolknooppunt. Meerdere API's maken het ook mogelijk taakbestanden op te sommen en te verkrijgen, zoals [Get From Task](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Omdat pools en poolknooppunten vaak vluchtig zijn, waarbij knooppunten continu worden toegevoegd en verwijderd, wordt aanbevolen dat logboekbestanden blijven bestaan. [Taakuitvoerbestanden](https://docs.microsoft.com/azure/batch/batch-task-output-files) zijn een handige manier om logboekbestanden op te slaan in Azure Storage.

### <a name="output-file-failures"></a>Fouten in uitvoerbestanden
Op elke bestandsupload schrijft Batch twee logbestanden `fileuploadout.txt` naar `fileuploaderr.txt`het compute-knooppunt en . U deze logboekbestanden onderzoeken om meer te weten te komen over een specifieke fout. In gevallen waarin het uploaden van het bestand nooit is geprobeerd, bijvoorbeeld omdat de taak zelf niet kon worden uitgevoerd, zullen deze logboekbestanden niet bestaan.  

## <a name="next-steps"></a>Volgende stappen

Controleer of uw toepassing uitgebreide foutcontrole implementeert; het kan van cruciaal belang zijn om problemen onmiddellijk op te sporen en te diagnosticeren.
