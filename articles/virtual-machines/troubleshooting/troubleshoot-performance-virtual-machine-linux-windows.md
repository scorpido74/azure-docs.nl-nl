---
title: Problemen met de prestaties van de virtuele machine van Azure oplossen op Linux of Windows
description: In dit artikel worden generieke prestatieproblemen met virtuele machines beschreven door knelpunten te monitoren en te observeren en wordt mogelijke herstel mogelijk gemaakt voor problemen die zich kunnen voordoen.
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772615"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Problemen met de prestaties van de virtuele machine van Azure oplossen op Linux of Windows

In dit artikel worden generieke prestatieproblemen met virtuele machines beschreven door knelpunten te monitoren en te observeren en wordt mogelijke herstel mogelijk gemaakt voor problemen die zich kunnen voordoen. Naast monitoring u ook Perfinsights gebruiken die een rapport kunnen leveren met aanbevelingen voor best practices en belangrijke knelpunten rond IO/CPU/Geheugen. Perfinsights is beschikbaar voor zowel [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) als [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM's in Azure.

Dit artikel loopt door het gebruik van monitoring om prestatieknelpunten te diagnosticeren.

## <a name="enabling-monitoring"></a>Monitoring inschakelen

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS virtuele machinebewaking

Als u de gast-VM wilt controleren, gebruikt u de Azure VM-controle, waarmee u wordt gewaarschuwd voor bepaalde resourcevoorwaarden op hoog niveau. Zie Overzicht van [Azure Resource-logboeken](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)als u wilt controleren of de VM-diagnose is ingeschakeld. Als u het volgende ziet, hebt u de diagnose waarschijnlijk niet ingeschakeld:

![Controle is niet ingeschakeld](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>VM-diagnose inschakelen via microsoft Azure-portal

Ga als u vm-diagnoses in:

1. Naar de VM gaan
2. Klik **op Diagnostische instellingen**
3. Selecteer het opslagaccount en klik op **Bewaking op gastniveau inschakelen**.

   ![Klik op Instellingen en vervolgens op Diagnostische gegevens](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

U het opslagaccount controleren dat wordt gebruikt voor de installatie van Diagnostische gegevens op het tabblad **Agent** onder **Diagnostische instellingen**.

![Opslagaccount controleren](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Diagnose van opslagaccount inschakelen via Azure-portal

Opslag is een zeer belangrijke laag wanneer we van plan zijn om io-prestaties voor een virtuele machine in Azure te analyseren. Voor opslaggerelateerde statistieken moeten we diagnostiek als extra stap inschakelen. Dit kan ook worden ingeschakeld, als we alleen de opslaggerelateerde tellers willen analyseren.

1. Bepaal welke opslagaccount (of accounts) uw VM gebruikt door de VM te selecteren. Klik **op Instellingen**en klik vervolgens op **Schijven:**

   ![Klik op Instellingen en vervolgens op Schijven](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. Ga in de portal naar het opslagaccount (of accounts) voor de virtuele machine en werk de volgende stappen door:

   1. Klik op overzicht voor het opslagaccount dat u met de bovenstaande stap hebt gevonden.
   2. Standaardstatistieken worden weergegeven. 

    ![Standaardstatistieken](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Klik op een van de statistieken, die een ander blad met meer opties om te configureren en statistieken toe te voegen zal laten zien.

   ![Statistieken toevoegen](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Ga als volgende voor het volgende over de volgende opties:

1.  Selecteer **Metrische gegevens**.
2.  Selecteer de **bron** (opslagaccount).
3.  Selecteer de **naamruimte**
4.  Selecteer **Metrische waarde**.
5.  Het type **aggregatie selecteren**
6.  U deze weergave vastmaken op het dashboard.

## <a name="observing-bottlenecks"></a>Knelpunten observeren

Zodra we het eerste installatieproces voor benodigde statistieken doorlopen en de diagnose voor VM- en bijbehorende opslagaccount inschakelen, kunnen we overschakelen naar de analysefase.

### <a name="accessing-the-monitoring"></a>Toegang tot de monitoring

Selecteer de Azure VM die u wilt onderzoeken en selecteer **Controle**.

![Controle selecteren](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Tijdigheid van observatie

Als u wilt vaststellen of er knelpunten in de bron zijn, controleert u uw gegevens. Als uw computer vindt dat uw machine goed heeft gewerkt, maar er is gemeld dat de prestaties onlangs zijn afgebroken, controleert u een tijdsbereik met gegevens dat prestatiemetrische gegevens omvat voordat de gerapporteerde wijziging, tijdens en na het probleem is uitgevoerd.

### <a name="check-for-cpu-bottleneck"></a>Controleren op CPU-knelpunt

![Controleren op CPU-knelpunt](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Bewerk de grafiek.
2. Stel het tijdsbereik in.
3. U moet dan toevoegen in de teller: CPU Percentage Guest OS
4. Sla op.

### <a name="cpu-observe-trends"></a>CPU observeertrends

Wanneer u kijkt naar prestatieproblemen, moet u zich bewust zijn van de trends en begrijpen of ze u beïnvloeden. In de volgende secties gebruiken we de grafieken van Monitoring van het portaal om trends weer te geven. Ze kunnen ook handig zijn voor kruisverwijzingen verschil resource gedrag in dezelfde periode. Als u de grafieken wilt aanpassen, klikt u op [Azure Monitor-gegevensplatform](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking - Spiking kan worden gerelateerd aan een geplande taak / bekende gebeurtenis. Als u de taak identificeren, bepaalt u of de taak op het vereiste prestatieniveau wordt uitgevoerd. Als de prestaties aanvaardbaar zijn, hoeft u de resources mogelijk niet te verhogen.

Spike up en Constant – Geeft vaak een nieuwe werkbelasting aan. Als het geen herkende werkbelasting is, schakelt u de controle in de VM in om te achterhalen welk proces (of processen) het gedrag veroorzaakt. Zodra het proces is erkend, bepalen of het verhoogde verbruik wordt veroorzaakt door inefficiënte code, of normaal verbruik. Als het normale verbruik, beslissen of het proces werkt op het vereiste prestatieniveau.

Constant : bepaal of uw VM altijd op dit niveau is uitgevoerd of dat deze alleen op dat niveau is uitgevoerd sinds de diagnose is ingeschakeld. Als dat het zo is, identificeert u het proces (of de processen) die het probleem veroorzaakt en overweegt u meer van die bron toe te voegen.

Gestaag toenemende - Een constante toename van het verbruik is vaak ofwel inefficiënte code of een proces waarbij meer gebruiker werklast.

### <a name="high-cpu-utilization-remediation"></a>Herstel van een hoog CPU-gebruik

Als uw toepassing of proces niet op het juiste prestatieniveau wordt uitgevoerd en u 95% + CPU-gebruik constant ziet, u een van de volgende taken uitvoeren:

* Voor onmiddellijke verlichting - Verhoog de grootte van de VM tot een grootte met meer cores
* Begrijp het probleem - zoek toepassing/proces en los dienovereenkomstig op.

Als u de VM hebt verhoogd en de CPU nog steeds 95% draait, bepaalt u of deze instelling betere prestaties of een hogere toepassingsdoorvoer tot een aanvaardbaar niveau biedt. Zo niet, los dan die afzonderlijke toepassing op\proces.

U Perfinsights voor [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) of [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) gebruiken om te analyseren welk proces het CPU-verbruik aandrijft. 

## <a name="check-for-memory-bottleneck"></a>Controleren op geheugenknelpunt

Ga als het gaat om het bekijken van de statistieken:

1. Een sectie toevoegen.
2. Voeg een tegel toe.
3. Open de galerij.
4. Selecteer het geheugengebruik en sleep. Wanneer de tegel is gekoppeld, klikt u met de rechtermuisknop en selecteert u **6x4**.

### <a name="memory-observe-trends"></a>Geheugen observeren trends

Het geheugengebruik laat zien hoeveel geheugen wordt verbruikt met de VM. Begrijp de trend en of deze wordt toegewezen aan de tijd waarin u problemen ziet. U moet altijd meer dan 100 MB beschikbaar geheugen hebben.

Spike en Constant /Constant Steady Consumption - Hoog geheugengebruik is mogelijk niet de oorzaak van slechte prestaties, omdat sommige toepassingen, zoals relationele databaseengines, een grote hoeveelheid geheugen toewijzen en dit gebruik mogelijk niet significant is. Als er echter meerdere geheugenhongerige toepassingen zijn, u slechte prestaties zien van geheugengeschil die het bijsnijden en paging/swapping naar schijf veroorzaakt. Deze slechte prestaties zijn vaak een merkbare oorzaak van de impact van de toepassingsprestaties.

Gestaag toenemende consumptie - Een mogelijke toepassing 'warming-up', dit verbruik is gebruikelijk bij database motoren opstarten. Het kan echter ook duiden op een geheugenlek in een toepassing. Identificeer de toepassing en begrijp of het gedrag wordt verwacht.

Pagina- of swapbestandsgebruik : controleer of u het Windows Paging-bestand gebruikt (op D:\) of het Linux Swap-bestand (op) `/dev/sdb`wordt intensief gebruikt. Als u niets over deze volumes, behalve deze bestanden, controleren op hoge lees / schrijft op deze schijven. Dit probleem is indicatief voor lage geheugenomstandigheden.

### <a name="high-memory-utilization-remediation"></a>Herstel van het hoge geheugengebruik

Als u een hoog geheugengebruik wilt oplossen, voert u een van de volgende taken uit:

* Voor onmiddellijke verlichting of pagina- of swapbestandsgebruik - Verhoog de VM-grootte tot een met meer geheugen en controleer vervolgens.
* Probleem begrijpen : zoek toepassingen/processen en los problemen op voor het identificeren van hoogverbruiksgeheugentoepassingen.
* Als u de toepassing kent, kijk of de geheugentoewijzing kan worden afgetopt.

Als u na een upgrade naar een grotere VM ontdekt dat u nog steeds een constante constante toename tot 100% hebt, identificeert u de toepassing/het proces en lost u problemen op.

U Perfinsights voor [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) of [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) gebruiken om te analyseren welk proces het geheugenverbruik aandrijft. 

## <a name="check-for-disk-bottleneck"></a>Controleren op schijfknelpunt

Als u het subsysteem voor opslag voor de VM wilt controleren, controleert u de diagnose op Azure VM-niveau met behulp van de tellers in VM Diagnostics en ook de storageaccountdiagnose.

Voor binnen VM-specifieke probleemoplossing u Perfinsights voor [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) of [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)gebruiken, wat kan helpen om te analyseren welk proces de IO's aandrijft. 

Houd er rekening mee dat we geen tellers hebben voor Zone Redundant- en Premium-opslagaccounts. Voor kwesties met betrekking tot deze tellers, een ondersteuning geval te verhogen.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Diagnose van opslagaccount weergeven in monitoring

Als u aan de onderstaande items wilt werken, gaat u naar het opslagaccount voor de VM in de portal:

![Storage Account Diagnostics in Monitoring weergeven](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Bewerk de bewakingsgrafiek.
2. Stel het tijdsbereik in.
3. Voeg de tellers toe die in de onderstaande stappen worden beschreven.
4. Sla de wijzigingen op.

### <a name="disk-observe-trends-standard-storage-only"></a>Schijf observeertrends (alleen standaardopslag)

Als u problemen met opslag wilt identificeren, bekijkt u de prestatiestatistieken van de storageaccountdiagnose en de VM Diagnostics.

Voor elke controle hieronder, kijk voor de belangrijkste trends wanneer de problemen zich voordoen binnen het tijdsbestek van het probleem.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Beschikbaarheid Azure-opslag controleren : beschikbaarheid toevoegen

Als u een daling van de beschikbaarheid ziet, kan er een probleem zijn met het platform en controleert u de [Azure-status](https://azure.microsoft.com/status/). Als daar geen probleem wordt weergegeven, dient u een nieuw ondersteuningsverzoek in.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Time-out van Azure-opslag controleren - De statistieken van het opslagaccount toevoegen:

* ClientTimeOutFout
* ServerTimeOutDFout ServerTimeOutError ServerTimeOutD
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Waarden in de *TimeOutError-statistieken geven aan dat een IO-bewerking te lang duurde en een time-out heeft. Door de volgende stappen te werken, worden mogelijke oorzaken geïdentificeerd.

AverageServerLatency verhogingen op hetzelfde moment op de TimeOutErrors kan een platform probleem. Een nieuw ondersteuningsverzoek indienen in deze situatie.

GemiddeldeE2ELatentie staat voor clientlatentie. Controleer hoe de IOPS door de toepassing wordt uitgevoerd. Zoek naar een verhoging of constant hoge TotalRequests-statistiek. Deze statistiek vertegenwoordigt IOPS. Als u de limieten van het opslagaccount of enkele VHD begint te raken, kan de latentie gerelateerd zijn aan beperking.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Controleren op Azure-opslagbeperking - De statistieken van het opslagaccount toevoegen: ThrottlingError

Waarden voor beperking geven aan dat u wordt beperkt op opslagaccountniveau, wat betekent dat u de IOPS-limiet van het account bereikt. U bepalen of u de drempelwaarde voor IMP's bereikt door de metrische **TotalRequests te**controleren.

Houd er rekening mee dat elke VHD een limiet van 500 IOPS of 60 MBits heeft, maar gebonden is aan de cumulatieve limiet van 20000 IOPS per opslagaccount.

Met deze statistiek u niet zien welke blob de beperking veroorzaakt en welke er door worden beïnvloed. U raakt echter de IOPS- of Ingress/Egress-limieten van het opslagaccount.

Als u wilt bepalen of u de IOPS-limiet bereikt, gaat u naar de diagnose van het opslagaccount en controleert u de TotalRequests om te zien of u 20 duizend TotalRequests nadert. Identificeer een wijziging in het patroon, of u de limiet voor de eerste keer ziet of dat deze limiet op een bepaald moment plaatsvindt.

Met nieuwe schijfaanbiedingen onder standaardopslag kunnen de IOPS- en doorvoerlimieten verschillen, maar de cumulatieve limiet van het Standaardopslagaccount is 20000 IOPS (Premium-opslag heeft verschillende limieten op account- of schijfniveau). Lees meer over verschillende standaardopslagschijfaanbiedingen en per schijflimieten:

* [Schaalbaarheid en prestatiedoelen voor VM-schijven in Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

#### <a name="references"></a>Verwijzingen

* [Schaalbaarheids- en prestatiedoelen voor blob-opslagaccounts met premium pagina's](../../storage/blobs/scalability-targets-premium-page-blobs.md)

De bandbreedte van het opslagaccount wordt gemeten aan de hand van de statistieken van het opslagaccount: TotalIngress en TotalEgress. U hebt verschillende drempelwaarden voor bandbreedte, afhankelijk van het type redundantie en regio's.

* [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../../storage/common/scalability-targets-standard-account.md)

Controleer de TotalIngress en TotalEgress tegen de invallen- en uitgangslimieten voor het type redundantie en regio voor redundantie van de opslagaccount.

Controleer doorvoerlimieten van de VHD's die aan de VM zijn gekoppeld. Voeg de schijf lees en schrijf van vm-statistieken toe.

Nieuwe schijfaanbiedingen onder standaardopslag hebben verschillende IOPS- en doorvoerlimieten (IOPS worden niet per VHD weergegeven). Bekijk de gegevens om te zien of u de grenzen van gecombineerde doorvoerMB van de VHD(s) op VM-niveau bereikt met Schijflezen en Schrijven en optimaliseer vervolgens uw VM-opslagconfiguratie om voorbij enkele VHD-limieten te schalen. Lees meer over verschillende standaardopslagschijfaanbiedingen en per schijflimieten:

* [Schaalbaarheid en prestatiedoelen voor VM-schijven in Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

### <a name="high-disk-utilizationlatency-remediation"></a>Herstel van hoge schijfgebruik/latentie

Verlaag de gebruikerslatentie en optimaliseer VM IO om voorbij VHD-limieten te schalen

* [IO optimaliseren voor Windows in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Io optimaliseren voor Linux in Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Beperking verminderen

Als u de bovengrenzen van opslagaccounts bereikt, moet u de VHD's opnieuw in evenwicht brengen tussen opslagaccounts. Raadpleeg [schaalbaarheid en prestatiedoelen voor Azure Storage](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Verhoog de doorvoer en verminder de latentie

Als u een latentiegevoelige toepassing hebt en een hoge doorvoer vereist, migreert u uw VHD's naar Azure Premium-opslag met behulp van de VM van de DS- en GS-serie.

Deze artikelen bespreken de specifieke scenario's:

* [Migreren naar Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Azure Premium Storage gebruiken met SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Volgende stappen

Als u op enig moment in dit artikel meer hulp nodig hebt, neemt u contact op met de Azure-experts op [de forums MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/)

U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
