---
title: Problemen oplossen met algemene prestaties voor virtuele Azure-machines met Linux of Windows
description: In dit artikel worden algemene problemen met de prestaties van virtuele machines (VM) beschreven door te controleren en knel punten te observeren en mogelijke problemen op te lossen.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 4e4a2b1df178d884e02ab5029f2e76a0376a7653
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846561"
---
# <a name="generic-performance-troubleshooting-for-azure-virtual-machine-running-linux-or-windows"></a>Problemen met algemene prestaties voor de virtuele machine van Azure in Linux of Windows oplossen

In dit artikel worden algemene problemen met de prestaties van virtuele machines (VM) beschreven door te controleren en knel punten te observeren en mogelijke problemen op te lossen.

## <a name="enabling-monitoring"></a>-Bewaking inschakelen

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS-bewaking van virtuele machines

Als u de gast-VM wilt controleren, gebruikt u de Azure VM-bewaking. Hiermee wordt u gewaarschuwd voor bepaalde resource voorwaarden op hoog niveau. Zie [overzicht van Azure-resource logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#change-settings-for-an-existing-resource)om te controleren of u de diagnostische gegevens van de virtuele machine hebt ingeschakeld. Als u het volgende ziet, hebt u waarschijnlijk niet de diagnostische gegevens ingeschakeld:

![Bewaking is niet ingeschakeld](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Diagnostische gegevens over virtuele machines inschakelen via micro soft Azure Portal

Als u Diagnostische gegevens over de virtuele machine wilt inschakelen, gaat u naar de virtuele machine, klikt u op **instellingen**en vervolgens op **Diagnostische gegevens**.

![Klik op instellingen en vervolgens op diagnostische gegevens](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)
 
### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Diagnostische gegevens over het opslag account inschakelen via Azure Portal

Bepaal eerst welk opslag account (of welke accounts) uw virtuele machine gebruikt door de virtuele machine te selecteren. Klik op **instellingen**en klik vervolgens op **schijven**:

![Klik op instellingen en vervolgens op schijven](media/troubleshoot-performance-virtual-machine-linux-windows/3-storage-disks-disks-selection.png)

Ga in de portal naar het opslag account (of de accounts) voor de virtuele machine en werk de volgende stappen uit:

![Metrische gegevens van BLOB selecteren](media/troubleshoot-performance-virtual-machine-linux-windows/4-select-blob-metrics.png)
 
1. Selecteer **alle instellingen**.
2. Schakel diagnostische gegevens in.
3. Selecteer *metrische gegevens voor *BLOB** * en stel Bewaar periode in op **30** dagen.
4. Sla de wijzigingen op.

## <a name="observing-bottlenecks"></a>Knel punten naachten

### <a name="accessing-the-monitoring"></a>Toegang tot de bewaking

Selecteer de virtuele Azure-machine die u wilt onderzoeken en selecteer **bewaking**.

![Bewaking selecteren](media/troubleshoot-performance-virtual-machine-linux-windows/5-observe-monitoring.png)
 
### <a name="timelines-of-observation"></a>Waarnemings lijnen

Controleer uw gegevens om te bepalen of u knel punten in de resource hebt. Als u merkt dat uw machine goed is uitgevoerd, maar er is gemeld dat de prestaties onlangs zijn verslechterd, controleert u een tijd bereik met gegevens die de metrische gegevens voor de prestaties bevatten voordat de gerapporteerde wijzigingen, tijdens en na het probleem.

### <a name="check-for-cpu-bottleneck"></a>Controleren op CPU-knel punt

![Controleren op CPU-knel punt](media/troubleshoot-performance-virtual-machine-linux-windows/6-cpu-bottleneck-time-range.png)

1. Bewerk de grafiek.
2. Stel het tijds bereik in.
3. U moet vervolgens toevoegen aan de teller: CPU-percentage gast besturingssysteem
4. Sla op.

### <a name="cpu-observe-trends"></a>Trends in de CPU

Houd bij het bekijken van prestatie problemen rekening met de trends en begrijp of ze van invloed zijn op u. In de volgende secties gebruiken we de bewakings grafieken van de portal om trends weer te geven. Ze kunnen ook handig zijn voor het cross-refereren van het bron gedrag van verschillen in dezelfde periode. Als u de grafieken wilt aanpassen, klikt u op [Azure monitor gegevens platform](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking – spiking kan zijn gerelateerd aan een geplande taak/bekende gebeurtenis. Als u de taak kunt identificeren, bepaalt u of de taak wordt uitgevoerd op het vereiste prestatie niveau. Als de prestaties acceptabel zijn, is het wellicht niet nodig om resources te verg Roten.

Pieken en constanten: geeft vaak een nieuwe workload aan. Als het geen herken bare werk belasting is, schakelt u bewaking in de VM in om te ontdekken welk proces (of processen) het gedrag veroorzaakt. Nadat het proces is herkend, bepaalt u of het verhoogde verbruik wordt veroorzaakt door inefficiënte code of normaal verbruik. Als het normaal verbruik is, bepaalt u of het proces op het vereiste prestatie niveau werkt.

Constante: Bepaal of uw virtuele machine altijd op dit niveau wordt uitgevoerd, of dat deze alleen op dat niveau wordt uitgevoerd sinds de diagnostische gegevens zijn ingeschakeld. Als dat het geval is, identificeert u het proces (of de processen) die het probleem veroorzaakt, en kunt u meer van die resource toevoegen.

Gestaag toenemen: een constante toename in het gebruik is vaak een inefficiënte code of een proces dat wordt uitgevoerd op de werk belasting van meer gebruikers.

### <a name="high-cpu-utilization-remediation"></a>Hoog CPU-gebruik herbemiddeling

Als uw toepassing of proces niet wordt uitgevoerd op het juiste prestatie niveau en u ziet een constante van 95% + CPU-gebruik, kunt u een van de volgende taken uitvoeren:

* Voor een onmiddellijke vrijs telling: Verhoog de grootte van de virtuele machine naar een grootte met meer kernen
* Meer informatie over het probleem – Zoek de toepassing/het proces en los deze problemen op.

Als u de virtuele machine hebt verhoogd en de CPU nog 95% wordt uitgevoerd, bepaalt u of deze instelling betere prestaties of een hogere doorvoer snelheid voor toepassingen biedt dan een acceptabel niveau. Als dat niet het geval is, kunt u de afzonderlijke application\process. oplossen

## <a name="check-for-memory-bottleneck"></a>Controleren op geheugen knelpunt

De metrische gegevens weer geven:

1. Voeg een sectie toe.
2. Een tegel toevoegen.
3. Open de galerie.
4. Selecteer het geheugen gebruik en sleep. Wanneer de tegel is gekoppeld, klikt u met de rechter muisknop en selecteert u **6x4**.

### <a name="memory-observe-trends"></a>Trends in geheugen

In het geheugen gebruik ziet u hoeveel geheugen wordt verbruikt met de virtuele machine. Begrijp de trend en of deze is toegewezen aan de tijd waarop u problemen ziet. U moet altijd meer dan 100 MB beschik bare geheugen hebben.

Doorlopend en constant/constant continu gebruik: hoog geheugen gebruik is mogelijk niet de oorzaak van slechte prestaties, omdat sommige toepassingen, zoals relationele data base-engines, een grote hoeveelheid geheugen toewijzen en dit gebruik mogelijk niet significant is. Als er echter meerdere toepassingen met veel geheugen zijn, is het mogelijk dat er slechte prestaties van de geheugen conflicten ontstaan waardoor de schijf wordt verkleind en gewisseld of gewisseld. Deze slechte prestaties zijn vaak een merk bare oorzaak van de prestaties van de toepassing.

Het verbruik wordt gestaag verhoogd, een mogelijke opwarming van de toepassing. dit verbruik is gebruikelijk bij het opstarten van data base-engines. Het kan echter ook worden ondertekend door een geheugenlek in een toepassing. Identificeer de toepassing en begrijp of het probleem wordt verwacht.

Gebruik van pagina of wissel bestand: Controleer of u het Windows-wissel bestand (dat zich op D: \)-of Linux-wissel bestand bevindt (op `/dev/sdb`) gebruikt, intensief wordt gebruikt. Als u niets hebt met deze volumes, behalve deze bestanden, controleert u op deze schijven op hoog lezen/schrijven. Dit probleem duidt op weinig geheugen.

### <a name="high-memory-utilization-remediation"></a>Herstel met hoog geheugen gebruik

Voer een van de volgende taken uit om het hoge geheugen gebruik op te lossen:

* Verg root de VM-grootte met meer geheugen en controleer vervolgens of het gebruik van een onmiddellijke aflossing of wissel bestand is.
* Probleem begrijpen: Zoek toepassingen/proces en los problemen op voor het identificeren van hoog verbruiks geheugen toepassingen.
* Als u de toepassing kent, kunt u nagaan of de geheugen toewijzing kan worden afgetopt.

Als u na een upgrade naar een grotere virtuele machine hebt ontdekt, weet u zeker dat u nog steeds constante stabiele toename hebt tot 100%, de toepassing/het proces identificeren en problemen oplossen.

## <a name="check-for-disk-bottleneck"></a>Controleren op schijf knelpunt

Als u het opslag subsysteem voor de virtuele machine wilt controleren, controleert u de diagnostische gegevens op het niveau van de Azure-VM met behulp van de items in de diagnostische gegevens van de VM en de diagnostische gegevens over het opslag account.

Houd er rekening mee dat er geen items zijn voor zone redundante en Premium Storage accounts. Verhoog een ondersteunings aanvraag voor problemen die betrekking hebben op deze prestatie meter items.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Diagnostische gegevens over opslag accounts weer geven in bewaking

Als u aan de onderstaande items wilt werken, gaat u naar het opslag account voor de virtuele machine in de portal:

![Diagnostische gegevens over opslag accounts weer geven in bewaking](media/troubleshoot-performance-virtual-machine-linux-windows/7-virtual-machine-storage-account.png)

1. Bewerk de bewakings grafiek.
2. Stel het tijds bereik in.
3. Voeg de items toe die worden beschreven in de onderstaande stappen.
4. Sla de wijzigingen op.

### <a name="disk-observe-trends-standard-storage-only"></a>Trends in de schijf (alleen standaard opslag)

Als u problemen met de opslag wilt identificeren, bekijkt u de prestatie gegevens van de diagnostische gegevens van het opslag account en de diagnostische gegevens van de virtuele machine.

Voor elke controle hieronder zoekt u naar de belangrijkste trends wanneer de problemen binnen het tijds bereik van het probleem optreden.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Controleer de beschik baarheid van Azure Storage – de metrische gegevens van het opslag account toevoegen: Beschik baarheid

Als er een drop in Beschik baarheid wordt weer geven, is er mogelijk een probleem met het platform. Controleer de [status van Azure](https://azure.microsoft.com/status/). Als er geen probleem wordt weer gegeven, verhoogt u een nieuwe ondersteunings aanvraag.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Controleren op time-out voor Azure Storage-de metrische gegevens van het opslag account toevoegen:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Waarden in de * TimeOutError metrische gegevens geven aan dat een i/o-bewerking te lang duurde en time-out heeft. Door de volgende stappen te door lopen, kunt u mogelijke oorzaken identificeren.

Averageserverlatency aan neemt toe op hetzelfde moment dat de TimeOutErrors een platform probleem kan zijn. In deze situatie een nieuwe ondersteunings aanvraag genereren.

AverageE2ELatency vertegenwoordigt client latentie. Controleer hoe de IOPS wordt uitgevoerd door de toepassing. Zoek naar een toename of voortdurend hoge TotalRequests-metriek. Deze metriek vertegenwoordigt IOPS. Als u begint met het bereiken van de limieten van het opslag account of één VHD, kan de latentie gerelateerd zijn aan het beperken.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Controleren op Azure Storage Throttle-de metrische gegevens van het opslag account toevoegen: ThrottlingError

Met de waarden voor beperking wordt aangegeven dat u wordt beperkt op het niveau van het opslag account, wat inhoudt dat de limiet voor IOPS van het account wordt bereikt. U kunt bepalen of u de limiet voor IOPs hebt bereikt door de metrische **TotalRequests**te controleren.

Houd er rekening mee dat elke VHD een limiet heeft van 500 IOPS of 60 MBit, maar is gebonden aan de cumulatieve limiet van 20000 IOPS per opslag account.

Met deze metriek kunt u niet zien welke BLOB de beperking veroorzaakt en welke door deze worden beïnvloed. U hebt echter de limieten voor IOPS of binnenkomend/uitgaand verkeer van het opslag account.

Als u wilt weten of u de limiet voor IOPS hebt bereikt, gaat u naar de diagnostische gegevens van het opslag account en controleert 20000 u de TotalRequests. Identificeer een wijziging in het patroon, ongeacht of u de limiet voor het eerst ziet, of of deze limiet op een bepaald moment plaatsvindt.

#### <a name="references"></a>Verwijzingen

* [Schaalbaarheids doelen voor schijven van virtuele machines](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

De band breedte van het opslag account wordt gemeten door de metrische gegevens van het opslag account: TotalIngress en TotalEgress. U hebt verschillende drempel waarden voor band breedte, afhankelijk van het type redundantie en regio's.

* [Schaalbaarheids doelen voor blobs, wacht rijen, tabellen en bestanden](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

Controleer de TotalIngress en de TotalEgress op basis van de limieten voor inkomend en uitgaand verkeer voor het type en de regio van het opslag account.

Controleer de doorvoer limieten van de Vhd's die zijn gekoppeld aan de virtuele machine. De metrische gegevens schijf voor de virtuele machine toevoegen Lees-en schrijf bewerkingen.

Elke VHD kan Maxi maal 60 MB/s ondersteunen (IOPS worden niet per VHD weer gegeven). Bekijk de gegevens om te zien of u de limieten hebt bereikt van gecombineerde door Voer van de VHD (s) op VM-niveau met behulp van de lees-en schrijf bewerking van de virtuele machine. vervolgens optimaliseert u de configuratie van de VM-opslag om eerdere VHD-limieten te schalen.

### <a name="high-disk-utilizationlatency-remediation"></a>Hoog schijf gebruik/herstel van de latentie

De latentie van de client verlagen en de i/o-bewerkingen van de VM optimaliseren

* [IO voor Windows optimaliseren in azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [IO voor Linux optimaliseren in azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Beperking beperken

Als u de hoogste limieten van opslag accounts aanwijst, worden de Vhd's opnieuw gebalanceerd tussen opslag accounts. Raadpleeg [Azure Storage schaal baarheid en prestatie doelen](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>De door Voer verhogen en de latentie verminderen

Als u een latentie gevoelige toepassing hebt en hoge door Voer hebt vereist, moet u uw Vhd's migreren naar Azure Premium Storage met behulp van de DS-en GS-VM.

In deze artikelen worden de specifieke scenario's besproken:

* [Migreren naar Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Azure Premium Storage met SQL Server gebruiken](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Volgende stappen

Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/).

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
