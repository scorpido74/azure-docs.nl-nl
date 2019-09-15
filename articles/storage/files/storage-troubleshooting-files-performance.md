---
title: Gids voor het oplossen van problemen met Azure Files
description: Bekende prestatie problemen met Azure-bestands shares en bijbehorende tijdelijke oplossingen.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 0e11949804e0c3de52db315424f83905516b4da8
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996605"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemen met Azure Files prestaties oplossen

In dit artikel worden enkele veelvoorkomende problemen met betrekking tot Azure-bestands shares vermeld. Het biedt mogelijke oorzaken en tijdelijke oplossingen wanneer deze problemen optreden.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Problemen met hoge latentie, lage door Voer en algemene prestaties

### <a name="cause-1-share-experiencing-throttling"></a>Oorzaak 1: Delen die de beperking ondervindt

Het standaard quotum voor een Premium-share is 100 GiB. Dit biedt 100 Baseline IOPS (met een kans om Maxi maal 300 voor een uur te burstiseren). Zie de sectie [ingerichte shares](storage-files-planning.md#provisioned-shares) in de plannings handleiding voor meer informatie over het inrichten en de relatie met IOPS.

Als u wilt controleren of uw share wordt beperkt, kunt u gebruikmaken van de metrische gegevens van Azure in de portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services** en zoek vervolgens naar **metrische gegevens**.

1. Selecteer **Metrische gegevens**.

1. Selecteer uw opslag account als de resource.

1. Selecteer **bestand** als metrische naam ruimte.

1. Selecteer **trans acties** als metrische gegevens.

1. Voeg een filter toe voor **ResponseType** en controleer of er aanvragen zijn met een antwoord code van **SUCCESSWITHTHROTTLING** (voor SMB) of **ClientThrottlingError** (voor rest).

![Metrische opties voor Premium-bestands shares](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Oplossing

- Verhoog de ingerichte capaciteit door een hoger quotum op uw share op te geven.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Oorzaak 2: Meta gegevens/werk ruimte zware belasting

Als het meren deel van uw aanvragen meta gegevens georiënteerd is (zoals CreateFile/open file/closefile/queryinfo/querydirectory), is de latentie erger in vergelijking met lees-en schrijf bewerkingen.

Als u wilt controleren of de meeste van uw aanvragen meta gegevens zijn gecentreerd, kunt u dezelfde stappen als hierboven gebruiken. Behalve in plaats van een filter voor **ResponseType**toe te voegen, voegt u een filter toe voor de **API-naam**.

![Filter voor de naam van de API in uw metrische gegevens](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Tijdelijke oplossing

- Controleer of de toepassing kan worden aangepast om het aantal bewerkingen voor meta gegevens te verminderen.

### <a name="cause-3-single-threaded-application"></a>Oorzaak 3: Toepassing met één thread

Als de toepassing die door de klant wordt gebruikt, één thread heeft, kan dit leiden tot een aanzienlijk lagere IOPS/door voer dan het maximum aantal dat is toegestaan op basis van uw ingerichte share grootte.

### <a name="solution"></a>Oplossing

- Verg root de parallellisatie van de toepassing door het aantal threads te verhogen.
- Schakel over naar toepassingen waarbij parallellisme mogelijk is. Bijvoorbeeld: voor kopieer bewerkingen kunnen klanten AzCopy of RoboCopy van Windows-clients of de **parallelle** opdracht op Linux-clients gebruiken.

## <a name="very-high-latency-for-requests"></a>Zeer hoge latentie voor aanvragen

### <a name="cause"></a>Oorzaak

De client-VM bevindt zich in een andere regio dan de bestands share.

### <a name="solution"></a>Oplossing

- Voer de toepassing uit vanaf een virtuele machine die zich in dezelfde regio bevindt als de bestands share.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>De client kan geen maximale door voer worden gerealiseerd die door het netwerk wordt ondersteund

Een mogelijke oorzaak hiervan is dat er geen SMB-ondersteuning voor meerdere kanalen is. Azure-bestands shares ondersteunen momenteel alleen één kanaal, dus er is slechts één verbinding tussen de client-VM en de server. Deze enkele verbinding wordt vastgelegd op één kern op de client-VM, zodat de maximale door Voer die uit een virtuele machine kan worden behaald, is gebonden aan één kern.

### <a name="workaround"></a>Tijdelijke oplossing

- Het verkrijgen van een virtuele machine met een grotere kern kan helpen de door voer te verbeteren.
- Als de client toepassing vanaf meerdere Vm's wordt uitgevoerd, wordt de door Voer verhoogd.

- Gebruik waar mogelijk REST Api's.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>De door Voer op Linux-clients is aanzienlijk lager in vergelijking met Windows-clients.

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de implementatie van SMB-client op Linux.

### <a name="workaround"></a>Tijdelijke oplossing

- De belasting over meerdere Vm's spreiden.
- Gebruik op dezelfde VM meerdere koppel punten met de optie **nosharesock** en verspreid de belasting over deze koppel punten.
- Probeer in Linux te koppelen met de optie **nostrictsync** om te voor komen dat SMB-leegmaak bewerking wordt afgedwongen bij elke fsync-aanroep. Voor Azure Files is deze optie niet van invloed op de gegevens consistentcy, maar kan de verouderde meta gegevens van het bestand in de mapweergave (**ls-l** opdracht) niet worden veroorzaakt. Het rechtstreeks opvragen van meta gegevens van het**bestand (de** opdracht) retourneert de meest recente meta gegevens van een bestand.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hoge latenties voor zware werk belastingen voor meta gegevens met uitgebreide open/close-bewerkingen.

### <a name="cause"></a>Oorzaak

Geen ondersteuning voor Directory-leases.

### <a name="workaround"></a>Tijdelijke oplossing

- Vermijd zo min mogelijk overmatige opening/afsluit ingang in dezelfde map binnen een korte periode.
- Voor Linux vm's verhoogt u de time-out voor de cachemap door **actimeo\<= sec >** op te geven als een koppelings optie. Het is standaard één seconde, zodat u een grotere waarde als drie of vijf kunt helpen.
- Voor virtuele Linux-machines moet u de kernel upgraden naar 4,20 of hoger.

## <a name="low-iops-on-centosrhel"></a>Lage IOPS op CentOS/RHEL

### <a name="cause"></a>Oorzaak

IO-diepte groter dan één wordt niet ondersteund op CentOS/RHEL.

### <a name="workaround"></a>Tijdelijke oplossing

- Voer een upgrade uit naar CentOS 8/RHEL 8.
- Ga naar Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Trage bestanden kopiëren naar en van Azure Files in Linux

Als het kopiëren van bestanden naar en van Azure Files verloopt, bekijkt u het [bestand langzaam kopiëren naar en van Azure files in Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) in de Linux-probleemoplossings handleiding.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Jitter/zaag-tooth patroon voor IOPS

### <a name="cause"></a>Oorzaak

Client toepassing is consistent langer dan de basis voor IOPS. Op dit moment is er geen service zijde die de belasting van de aanvraag verlaagt, dus als de client de limiet voor de basis lijn overschrijdt, wordt deze door de service beperkt. Deze beperking kan ertoe leiden dat de client een jitter/zaag-het IOPS-patroon ondervindt. In dit geval kan het gemiddelde aantal IOPS dat door de client wordt behaald, lager zijn dan de basis lijn voor IOPS.

### <a name="workaround"></a>Tijdelijke oplossing

- Verminder het laden van aanvragen van de client toepassing, zodat de share niet wordt beperkt.
- Verhoog het quotum van de share zodat de share niet wordt beperkt.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Buitensporige DirectoryOpen/DirectoryClose-aanroepen

### <a name="cause"></a>Oorzaak

Als het aantal DirectoryOpen/DirectoryClose-aanroepen zich bevindt in de top API-aanroepen en u niet verwacht dat de client dat veel aanroepen heeft, kan het zijn dat er een probleem is met de anti virus dat is geïnstalleerd op de Azure-client-VM.

### <a name="workaround"></a>Tijdelijke oplossing

- Er is een oplossing voor dit probleem beschikbaar in de [Update van april platform voor Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Het maken van bestanden verloopt langzamer dan verwacht

### <a name="cause"></a>Oorzaak

Werk belastingen die afhankelijk zijn van het maken van een groot aantal bestanden, zien geen aanzienlijk verschil tussen de prestaties van Premium-bestands shares en standaard bestands shares.

### <a name="workaround"></a>Tijdelijke oplossing

- Geen.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Trage prestaties van Windows 8,1 of server 2012 R2

### <a name="cause"></a>Oorzaak

Hoger dan de verwachte latentie die toegang heeft tot Azure Files voor intensieve IO-workloads.

### <a name="workaround"></a>Tijdelijke oplossing

- Installeer de beschik bare [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).
