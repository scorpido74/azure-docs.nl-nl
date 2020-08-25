---
title: Gids voor het oplossen van problemen met Azure Files
description: Los problemen met bekende prestaties op met Azure-bestands shares. Ontdek mogelijke oorzaken en bijbehorende tijdelijke oplossingen wanneer deze problemen optreden.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: fe1460d4353addff1b8e3095cfe06c1fcb3b7bd0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782367"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemen met Azure Files prestaties oplossen

In dit artikel worden enkele veelvoorkomende problemen met betrekking tot Azure-bestands shares vermeld. Het biedt mogelijke oorzaken en tijdelijke oplossingen wanneer deze problemen optreden.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Problemen met hoge latentie, lage door Voer en algemene prestaties

### <a name="cause-1-share-was-throttled"></a>Oorzaak 1: de share is beperkt

Aanvragen worden beperkt wanneer de limiet voor IOPS, binnenkomend of uitgaand verkeer voor een bestands share is bereikt. Zie [Bestands share en bestands schaal doelen](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)voor meer informatie over de limieten voor standaard-en Premium-bestands shares.

Als u wilt controleren of uw share wordt beperkt, kunt u gebruikmaken van de metrische gegevens van Azure in de portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services** en zoek vervolgens naar **metrische gegevens**.

1. Selecteer **Metrische gegevens**.

1. Selecteer uw opslag account als de resource.

1. Selecteer **bestand** als metrische naam ruimte.

1. Selecteer **trans acties** als metrische gegevens.

1. Voeg een filter toe voor **ResponseType** en controleer of er aanvragen zijn met een antwoord code van **SUCCESSWITHTHROTTLING** (voor SMB) of **ClientThrottlingError** (voor rest).

![Metrische opties voor Premium-bestands shares](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Zie [een waarschuwing maken als een bestands share wordt beperkt](#how-to-create-an-alert-if-a-file-share-is-throttled)als u een waarschuwing wilt ontvangen als een bestands share wordt beperkt.

### <a name="solution"></a>Oplossing

- Als u een standaard bestands share gebruikt, schakelt u [grote bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) in voor uw opslag account. Grote bestands shares bieden ondersteuning voor Maxi maal 10.000 IOPS per share.
- Als u een Premium-bestands share gebruikt, verg root u de grootte van de ingerichte bestands share om de limiet voor IOPS te verhogen. Zie de sectie [over het inrichten van Premium-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) in de Azure files plannings handleiding voor meer informatie.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Oorzaak 2: meta gegevens/naam ruimte zware werk belasting

Als het meren deel van uw aanvragen meta gegevens georiënteerd is (zoals CreateFile/open file/closefile/queryinfo/querydirectory), is de latentie erger in vergelijking met lees-en schrijf bewerkingen.

Als u wilt controleren of de meeste van uw aanvragen meta gegevens zijn gecentreerd, kunt u dezelfde stappen als hierboven gebruiken. Behalve in plaats van een filter voor **ResponseType**toe te voegen, voegt u een filter toe voor de **API-naam**.

![Filter voor de naam van de API in uw metrische gegevens](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Tijdelijke oplossing

- Controleer of de toepassing kan worden aangepast om het aantal bewerkingen voor meta gegevens te verminderen.
- Voeg een VHD toe op de bestands share en koppel VHD via SMB van de client om bestanden te kunnen uitvoeren op basis van de gegevens. Deze aanpak werkt voor één schrijver en meerdere lezers scenario's, en maakt het mogelijk om meta gegevens te localiseren, vergelijkbaar met een lokale direct gekoppelde opslag.

### <a name="cause-3-single-threaded-application"></a>Oorzaak 3: toepassing met één thread

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
- Probeer in Linux te koppelen met de optie **nostrictsync** om te voor komen dat SMB-leegmaak bewerking wordt afgedwongen bij elke **fsync** -aanroep. Voor Azure Files is deze optie niet van invloed op de consistentie van gegevens, maar kan de verouderde meta gegevens van een bestand in een map worden weer gegeven (**ls-l** opdracht). Het rechtstreeks opvragen van meta gegevens van het**bestand (de** opdracht) retourneert de meest recente meta gegevens van een bestand.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hoge latenties voor zware werk belastingen voor meta gegevens met uitgebreide open/close-bewerkingen.

### <a name="cause"></a>Oorzaak

Geen ondersteuning voor Directory-leases.

### <a name="workaround"></a>Tijdelijke oplossing

- Vermijd zo min mogelijk overmatige opening/afsluit ingang in dezelfde map binnen een korte periode.
- Voor Linux Vm's verhoogt u de time-out voor de cachemap door **actimeo \<sec> =** als een koppelings optie op te geven. Het is standaard één seconde, zodat u een grotere waarde als drie of vijf kunt helpen.
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

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Een waarschuwing maken als een bestands share wordt beperkt

1. Ga naar uw **opslag account** in de **Azure Portal**.
2. Klik in de sectie bewaking op **waarschuwingen** en klik vervolgens op **+ nieuwe waarschuwings regel**.
3. Klik op **Resource bewerken**, selecteer het **Bestands bron type** voor het opslag account en klik vervolgens op **gereed**. Als de naam van het opslag account bijvoorbeeld contoso is, selecteert u de resource contoso/file.
4. Klik op **voor waarde selecteren** om een voor waarde toe te voegen.
5. U ziet een lijst met signalen die worden ondersteund voor het opslag account. Selecteer de metrische gegevens van de **trans actie** .
6. Klik op de Blade **signaal logica configureren** op de vervolg keuzelijst **dimensie naam** en selecteer **antwoord type**.
7. Klik op de vervolg keuzelijst **dimensie waarden** en selecteer **SUCCESSWITHTHROTTLING** (voor SMB) of **ClientThrottlingError** (voor rest).

  > [!NOTE]
  > Als de dimensie waarde SuccessWithThrottling of ClientThrottlingError niet wordt weer gegeven, betekent dit dat de resource niet is beperkt. Als u de dimensie waarde wilt toevoegen, klikt u op **aangepaste waarde toevoegen** naast de vervolg keuzelijst **dimensie waarden** , typt u **SuccessWithThrottling** of **ClientThrottlingError**, klikt u op **OK** en herhaalt u stap #7.

8. Klik op de vervolg keuzelijst **dimensie naam** en selecteer **Bestands share**.
9. Klik op de vervolg keuzelijst **dimensie waarden** en selecteer de bestands share (s) waarop u een waarschuwing wilt ontvangen.

  > [!NOTE]
  > Als de bestands share een standaard bestands share is, selecteert u **alle huidige en toekomstige waarden**. De vervolg keuzelijst met dimensie waarden bevat niet de bestands share (s) omdat metrische gegevens per share niet beschikbaar zijn voor standaard bestands shares. Het beperken van waarschuwingen voor standaard bestands shares wordt geactiveerd als een bestands share binnen het opslag account wordt beperkt en de waarschuwing niet kan bepalen welke bestands share is beperkt. Aangezien metrische gegevens per aandeel niet beschikbaar zijn voor standaard bestands shares, is de aanbeveling één bestands share per opslag account.

10. Definieer de **waarschuwings parameters** (drempel waarde, operator, aggregatie granulatie en frequentie van evaluatie) en klik op **gereed**.

  > [!TIP]
  > Als u een statische drempel waarde gebruikt, kan de metrische grafiek helpen bij het bepalen van een redelijke drempelwaarde als de bestands share momenteel wordt beperkt. Als u een dynamische drempel waarde gebruikt, worden in de metrische grafiek de berekende drempel waarden weer gegeven op basis van recente gegevens.

11. Klik op **actie groep selecteren** om een **actie groep** (e-mail, SMS, enzovoort) toe te voegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
12. Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
13. Klik op **waarschuwings regel maken** om de waarschuwing te maken.

Zie [overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in azure monitor.

## <a name="see-also"></a>Zie ook
* [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Lees de veelgestelde vragen (FAQ) over Azure Files](storage-files-faq.md)
