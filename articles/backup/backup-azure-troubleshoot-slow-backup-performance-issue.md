---
title: Problemen met langzame back-ups van bestanden en mappen in Azure Backup
description: Biedt richt lijnen voor probleem oplossing waarmee u de oorzaak van Azure Backup prestatie problemen kunt vaststellen
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: c56ddf04b98cc2b38e023714fcb0ffc5452236f2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074915"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Problemen met langzame back-ups van bestanden en mappen in Azure Backup

Dit artikel bevat richt lijnen voor probleem oplossing waarmee u de oorzaak van trage back-upprestaties voor bestanden en mappen kunt vaststellen wanneer u Azure Backup gebruikt. Wanneer u de Azure Backup-agent gebruikt voor het maken van back-ups van bestanden, kan het back-upproces langer duren dan verwacht. Deze vertraging kan een of meer van de volgende oorzaken hebben:

* [Er zijn prestatie knelpunten op de computer waarvan een back-up wordt gemaakt.](#cause1)
* [Een ander proces of antivirus software verstoort het Azure Backup proces.](#cause2)
* [De back-upagent wordt uitgevoerd op een virtuele Azure-machine (VM).](#cause3)  
* [U maakt een back-up van een groot aantal (miljoenen) bestanden.](#cause4)

Voordat u problemen gaat oplossen, wordt u aangeraden de [nieuwste Azure backup-agent](https://aka.ms/azurebackup_agent)te downloaden en te installeren. Er worden regel matig updates voor de back-upagent gemaakt om verschillende problemen op te lossen, functies toe te voegen en de prestaties te verbeteren.

We raden u ook ten zeerste aan de [Veelgestelde vragen over de Azure backup-service](backup-azure-backup-faq.md) te controleren om ervoor te zorgen dat u geen veelvoorkomende configuratie problemen ondervindt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Oorzaak: prestatie knelpunten op de computer

Knel punten op de computer waarvan een back-up wordt gemaakt, kunnen vertragingen veroorzaken. Bijvoorbeeld: de mogelijkheid van de computer om te lezen van of te schrijven naar de schijf of beschik bare band breedte voor het verzenden van gegevens via het netwerk, kan knel punten veroorzaken.

Windows biedt een ingebouwd hulp programma dat [prestatie meter](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (PerfMon) wordt genoemd om deze knel punten te detecteren.

Hier volgen enkele prestatie meter items en bereiken die nuttig kunnen zijn bij het diagnosticeren van knel punten voor optimale back-ups.

| Item | Status |
| --- | --- |
| Logische schijf (fysieke schijf)--% inactief |• 100% niet-actief tot 50% inactief = in orde</br>• 49% niet actief tot 20% inactief = waarschuwing of monitor</br>• 19% niet-actief tot 0% inactief = kritiek of van specificatie |
| Logische schijf (fysieke schijf)--% gem. Lees-of schrijf tijd schijf |• 0,001 MS tot 0,015 MS = in orde</br>• 0,015 MS tot 0,025 MS = waarschuwing of monitor</br>• 0,026 MS of langer = kritiek of van de specificatie |
| Logische schijf (fysieke schijf)--huidige wachtrij lengte voor de schijf (voor alle exemplaren) |80 aanvragen langer dan 6 minuten |
| Geheugen--groep niet-wisselbaar bytes |• Minder dan 60% van verbruikte groep = in orde<br>• 61% tot 80% van verbruikte pool = waarschuwing of monitor</br>• Groter dan 80% groep verbruikt = kritiek of van specificatie |
| Geheugen--groeps wisselbaar bytes |• Minder dan 60% van verbruikte groep = in orde</br>• 61% tot 80% van verbruikte pool = waarschuwing of monitor</br>• Groter dan 80% groep verbruikt = kritiek of van specificatie |
| Geheugen-beschik bare mega bytes |• 50% vrije geheugen beschikbaar of meer = in orde</br>• 25% vrije beschik bare geheugen = monitor</br>• 10% van het beschik bare geheugen beschikbaar = waarschuwing</br>• Minder dan 100 MB of 5% van het beschik bare geheugen = kritiek of van de specificatie |
| Processor--\%processor tijd (alle exemplaren) |• Minder dan 60% verbruikt = in orde</br>• 61% tot 90% verbruikt = monitor of waarschuwing</br>• 91% tot 100% verbruikt = kritiek |

> [!NOTE]
> Als u vaststelt dat de infra structuur de culprit is, raden wij u aan de schijven regel matig te defragmenteren voor betere prestaties.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Oorzaak: een ander proces of antivirus software die met Azure Backup verstoort

We hebben verschillende instanties gezien waarbij andere processen in het Windows-systeem een negatieve invloed hebben op de prestaties van het proces van de Azure Backup Agent. Als u bijvoorbeeld zowel de Azure Backup Agent als een ander programma gebruikt voor het maken van een back-up van gegevens, of als de antivirus software wordt uitgevoerd en er een vergren deling is ingesteld op bestanden waarvan een back-up moet worden gemaakt, kunnen de meerdere vergren delingen op bestanden leiden tot conflicten. In dit geval kan het maken van de back-up mislukken of kan de taak langer duren dan verwacht.

De aanbevolen aanbeveling in dit scenario is om het andere back-upprogramma uit te scha kelen om te zien of de back-uptijd voor de Azure Backup Agent wordt gewijzigd. Normaal gesp roken zorgt ervoor dat meerdere back-uptaken op hetzelfde moment voldoende zijn om te voor komen dat ze elkaar beïnvloeden.

Voor antivirus Programma's wordt u aangeraden de volgende bestanden en locaties uit te sluiten:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe als een proces
* C:\Program Files\Microsoft Azure Recovery Services agent \ mappen
* Scratch locatie (als u geen gebruik maakt van de standaard locatie)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Oorzaak: de back-upagent die wordt uitgevoerd op een virtuele machine van Azure

Als u de back-upagent op een virtuele machine uitvoert, zijn de prestaties langzamer dan wanneer u deze op een fysieke computer uitvoert. Dit wordt verwacht als gevolg van IOPS-beperkingen.  U kunt de prestaties echter optimaliseren door te scha kelen tussen de gegevens stations waarvan een back-up wordt gemaakt naar Azure Premium Storage. Er wordt gewerkt aan het oplossen van dit probleem en de oplossing is beschikbaar in een toekomstige release.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Oorzaak: een back-up maken van een groot aantal bestanden (miljoenen)

Het verplaatsen van een grote hoeveelheid gegevens duurt langer dan het verplaatsen van een kleiner gegevens volume. In sommige gevallen is de back-uptijd gerelateerd aan niet alleen de grootte van de gegevens, maar ook het aantal bestanden of mappen. Dit geldt met name wanneer een back-up wordt gemaakt van miljoenen kleine bestanden (enkele bytes van een paar kilo bytes).

Dit probleem treedt op omdat u een back-up maakt van de gegevens en deze verplaatst naar Azure, maar Azure uw bestanden tegelijkertijd catalogiseert. In sommige zeldzame scenario's kan de catalogus bewerking langer duren dan verwacht.

De volgende indica toren kunnen u helpen inzicht te krijgen in het knel punt en u kunt de volgende stappen uitvoeren:

* **De gebruikers interface toont de voortgang van de gegevens overdracht**. De gegevens worden nog overgedragen. De netwerk bandbreedte of de omvang van gegevens kan vertragingen veroorzaken.
* **De gebruikers interface geeft geen voortgang weer voor de gegevens overdracht**. Open de logboeken die zich bevinden in C:\Program Files\Microsoft Azure Recovery Services Agent\Temp en controleer vervolgens het item FileProvider:: EndData in de logboeken. Dit item geeft aan dat de gegevens overdracht is voltooid en dat de catalogus bewerking wordt uitgevoerd. Annuleer de back-uptaken niet. Wacht in plaats daarvan iets langer om de catalogus bewerking te volt ooien. Als het probleem zich blijft voordoen, neemt u contact op met de [ondersteuning van Azure](https://portal.azure.com/#create/Microsoft.Support).
