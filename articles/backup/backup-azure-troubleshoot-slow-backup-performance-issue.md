---
title: Problemen met trage back-up van bestanden en mappen oplossen
description: Biedt richtlijnen voor het oplossen van problemen om de oorzaak van azure backup-prestatieproblemen te diagnosticeren
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408688"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Problemen met langzame back-ups van bestanden en mappen in Azure Backup

In dit artikel vindt u richtlijnen voor het oplossen van problemen om de oorzaak van trage back-upprestaties voor bestanden en mappen vast te stellen wanneer u Azure Backup gebruikt. Wanneer u de Azure Backup-agent gebruikt om een back-up van bestanden te maken, kan het back-upproces langer duren dan verwacht. Deze vertraging kan worden veroorzaakt door een of meer van de volgende opties:

* [Er zijn prestatieknelpunten op de computer waarvan een back-up wordt opgenomen.](#cause1)
* [Een ander proces of antivirussoftware verstoort het Azure Backup-proces.](#cause2)
* [De back-upagent wordt uitgevoerd op een virtuele Azure-machine (VM).](#cause3)  
* [U maakt een back-up van een groot aantal (miljoenen) bestanden.](#cause4)

Voordat u problemen oplost, raden we u aan de [nieuwste Azure Backup-agent te](https://aka.ms/azurebackup_agent)downloaden en te installeren. We maken regelmatig updates voor de back-upagent om verschillende problemen op te lossen, functies toe te voegen en de prestaties te verbeteren.

We raden u ook ten zeerste aan de veelgestelde vragen over [azure backup-service](backup-azure-backup-faq.md) te controleren om ervoor te zorgen dat u geen van de veelvoorkomende configuratieproblemen ondervindt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Oorzaak: Back-uptaak wordt uitgevoerd in de niet-geoptimaliseerde modus

* De MARS-agent kan de back-uptaak in **de geoptimaliseerde modus** uitvoeren met USN (updatereeksnummer) wijzigen van het dagboek of **de niet-geoptimaliseerde modus** door te controleren op wijzigingen in mappen of bestanden door het hele volume te scannen.
* De niet-geoptimaliseerde modus is traag omdat de agent elk bestand op het volume moet scannen en vergelijken met de metagegevens om de gewijzigde bestanden te bepalen.
* Als u dit wilt verifiëren, opent u **Taakgegevens** van de MARS-agentconsole en controleert u de status om te zien of **er gegevens over overbrengen (niet-geoptimaliseerd, kan meer tijd in beslag nemen)** zoals hieronder wordt weergegeven:

    ![Uitvoeren in de niet-geoptimaliseerde modus](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* De volgende voorwaarden kunnen ertoe leiden dat de back-uptaak in de niet-geoptimaliseerde modus wordt uitgevoerd:
  * Eerste back-up (ook wel initialreplicatie genoemd) wordt altijd uitgevoerd in de niet-geoptimaliseerde modus
  * Als de vorige back-uptaak mislukt, wordt de volgende geplande back-uptaak uitgevoerd als niet-geoptimaliseerd.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Oorzaak: prestatieknelpunten op de computer

Knelpunten op de computer die wordt geback-upt, kunnen vertragingen veroorzaken. De mogelijkheid van de computer om te lezen of te schrijven naar de schijf, of beschikbare bandbreedte om gegevens via het netwerk te verzenden, kan bijvoorbeeld knelpunten veroorzaken.

Windows biedt een ingebouwde tool genaamd [Performance Monitor](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) om deze knelpunten op te sporen.

Hier volgen enkele prestatiemeteritems en -bereiken die nuttig kunnen zijn bij het diagnosticeren van knelpunten voor optimale back-ups.

| Prestatiemeteritem | Status |
| --- | --- |
| Logische schijf(fysieke schijf)--%idle |* 100% idle tot 50% idle = Gezond</br>* 49% idle tot 20% idle = Waarschuwing of Monitor</br>* 19% idle tot 0% idle = Kritiek of Out of Spec |
| Logische schijf(fysieke schijf)--%Avg. Schijf sec lezen of schrijven |* 0,001 ms tot 0,015 ms = Gezond</br>* 0,015 ms tot 0,025 ms = Waarschuwing of monitor</br>* 0,026 ms of langer = Kritiek of Out of Spec |
| Logische schijf(fysieke schijf)--Huidige schijfwachtrijlengte (voor alle instanties) |80 aanvragen voor meer dan 6 minuten |
| Geheugen: niet-paginabytes bundelen |* Minder dan 60% van de geconsumeerde pool = Gezond<br>* 61% tot 80% van de geconsumeerde pool = Waarschuwing of Monitor</br>* Meer dan 80% zwembad verbruikt = Kritisch of Out of Spec |
| Geheugen: op piepen de pagina's oppiepen |* Minder dan 60% van de geconsumeerde pool = Gezond</br>* 61% tot 80% van de geconsumeerde pool = Waarschuwing of Monitor</br>* Meer dan 80% zwembad verbruikt = Kritisch of Out of Spec |
| Geheugen - Beschikbaar Megabytes |* 50% van het gratis geheugen beschikbaar of meer = Gezond</br>* 25% van het gratis geheugen beschikbaar = Monitor</br>* 10% van het gratis geheugen beschikbaar = Waarschuwing</br>* Minder dan 100 MB of 5% van het beschikbare gratis geheugen = Kritiek of Out of Out of Spec |
| Processortijd\%(alle instanties) |* Minder dan 60% verbruikt = Gezond</br>* 61% tot 90% verbruikt = Monitor of Voorzichtigheid</br>* 91% tot 100% verbruikt = Kritiek |

> [!NOTE]
> Als u vaststelt dat de infrastructuur de boosdoener is, raden we u aan de schijven regelmatig te defragmenteren voor betere prestaties.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Oorzaak: een ander proces of antivirussoftware die Azure Backup verstoort

We hebben verschillende gevallen gezien waarin andere processen in het Windows-systeem de prestaties van het Azure Backup-agentproces negatief hebben beïnvloed. Als u bijvoorbeeld zowel de Azure Backup-agent als een ander programma gebruikt om een back-up van gegevens te maken, of als antivirussoftware wordt uitgevoerd en er een back-up van bestanden moet worden gemaakt, kunnen de meerdere vergrendelingen in bestanden tot onenigheid leiden. In deze situatie kan de back-up mislukken of kan de taak langer duren dan verwacht.

De beste aanbeveling in dit scenario is om het andere back-upprogramma uit te schakelen om te zien of de back-uptijd voor de Azure Backup-agent verandert. Meestal is het voldoende om ervoor te zorgen dat meerdere back-uptaken niet tegelijkertijd worden uitgevoerd, zodat ze elkaar niet kunnen beïnvloeden.

Voor antivirusprogramma's raden we u aan de volgende bestanden en locaties uit te sluiten:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe als proces
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappen
* Kraslocatie (als u de standaardlocatie niet gebruikt)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Oorzaak: back-upagent die wordt uitgevoerd op een virtuele Azure-machine

Als u de back-upagent op een vm uitvoert, zijn de prestaties trager dan wanneer u deze uitvoert op een fysieke machine. Dit wordt verwacht als gevolg van IOPS beperkingen.  U de prestaties echter optimaliseren door de gegevensstations die worden geback-upt te schakelen naar Azure Premium Storage. We werken aan het oplossen van dit probleem, en de oplossing zal beschikbaar zijn in een toekomstige release.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Oorzaak: een back-up maken van een groot aantal (miljoenen) bestanden

Het verplaatsen van een groot volume van gegevens duurt langer dan het verplaatsen van een kleiner volume van gegevens. In sommige gevallen is de back-uptijd niet alleen gerelateerd aan de grootte van de gegevens, maar ook met het aantal bestanden of mappen. Dit geldt vooral wanneer miljoenen kleine bestanden (een paar bytes tot een paar kilobytes) worden ondersteund.

Dit gedrag treedt op omdat Azure tegelijkertijd uw bestanden catalogiseert terwijl u een back-up maakt van de gegevens en deze verplaatst naar Azure. In sommige zeldzame scenario's kan de catalogusbewerking langer duren dan verwacht.

De volgende indicatoren kunnen u helpen inzicht te krijgen in de knelpunten en dus aan de volgende stappen te werken:

* **UI toont de voortgang voor de gegevensoverdracht**. De gegevens worden nog steeds overgedragen. De netwerkbandbreedte of de grootte van gegevens kan vertragingen veroorzaken.
* **UI geeft geen voortgang weer voor de gegevensoverdracht**. Open de logboeken op C:\Program Files\Microsoft Azure Recovery Services Agent\Temp en controleer op de FileProvider::EndData-vermelding in de logboeken. Dit item betekent dat de gegevensoverdracht is voltooid en dat de catalogusbewerking plaatsvindt. Annuleer de back-uptaken niet. Wacht in plaats daarvan iets langer tot de catalogusbewerking is voltooid. Als het probleem zich blijft voordoen, neemt u contact op met [Azure-ondersteuning](https://portal.azure.com/#create/Microsoft.Support).

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md)