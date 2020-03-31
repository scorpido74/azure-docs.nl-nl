---
title: Handleiding voor het oplossen van prestaties azure files
description: Bekende prestatieproblemen met Azure-bestandsshares en bijbehorende tijdelijke oplossingen.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598082"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemen met azure-bestanden oplossen

In dit artikel worden enkele veelvoorkomende problemen weergegeven met betrekking tot Azure-bestandsshares. Het biedt mogelijke oorzaken en oplossingen wanneer deze problemen worden ondervonden.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Hoge latentie, lage doorvoer en algemene prestatieproblemen

### <a name="cause-1-share-experiencing-throttling"></a>Oorzaak 1: Ervaart delen beperken

Het standaardquotum voor een premiumaandeel is 100 GiB, wat 100 Baseline IOPS biedt (met een potentieel tot 300 gedurende een uur). Zie het gedeelte [Ingerichte aandelen](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) van de planningsgids voor meer informatie over provisioning en de relatie met IOPS.

Als u wilt bevestigen of uw aandeel wordt beperkt, u Azure Metrics in de portal gebruiken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** en zoek vervolgens naar **Statistieken**.

1. Selecteer **Metrische gegevens**.

1. Selecteer uw opslagaccount als bron.

1. Selecteer **Bestand** als de metrische naamruimte.

1. Selecteer **Transacties** als statistiek.

1. Voeg een filter toe voor **ResponseType** en controleer of aanvragen een antwoordcode van **SuccessWithThrottling** (voor SMB) of **ClientThrottlingError** (voor REST) hebben.

![Metrics opties voor premium fileshares](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Zie Een waarschuwing maken [als een bestandsshare wordt beperkt](#how-to-create-an-alert-if-a-file-share-is-throttled)als een bestandsshare wordt beperkt.

### <a name="solution"></a>Oplossing

- Vergroot de ingerichte capaciteit van aandelen door een hoger quotum op uw aandeel op te geven.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Oorzaak 2: Metagegevens/naamruimte zware werkbelasting

Als de meerderheid van uw aanvragen metagegevens zijn gericht (zoals createfile/openfile/closefile/queryinfo/querydirectory), is de latentie slechter in vergelijking met lees-/schrijfbewerkingen.

Om te bevestigen of de meeste van uw verzoeken metagegevens zijn gericht, u dezelfde stappen gebruiken als hierboven. Behalve in plaats van een filter voor **ResponseType**toe te voegen, voegt u een filter toe voor **API-naam**.

![Filteren op API-naam in uw statistieken](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Tijdelijke oplossing

- Controleer of de toepassing kan worden gewijzigd om het aantal metagegevensbewerkingen te verminderen.
- Voeg een VHD toe aan de bestandsshare en monteer VHD over SMB van de client om bestanden bewerkingen uit te voeren tegen de gegevens. Deze aanpak werkt voor scenario's voor één schrijver en meerdere lezers en maakt het mogelijk om lokale metagegevens te gebruiken, met prestaties die vergelijkbaar zijn met een lokale direct-attached storage.

### <a name="cause-3-single-threaded-application"></a>Oorzaak 3: Single-threaded toepassing

Als de toepassing die door de klant wordt gebruikt single-threaded is, kan dit resulteren in een aanzienlijk lagere IOPS/doorvoer dan het maximaal mogelijke op basis van uw ingerichte aandeelgrootte.

### <a name="solution"></a>Oplossing

- Verhoog de parallellisme van toepassingen door het aantal threads te verhogen.
- Schakel over naar toepassingen waar parallellisme mogelijk is. Voor kopieerbewerkingen kunnen klanten bijvoorbeeld AzCopy of RoboCopy van Windows-clients of de **parallelle** opdracht op Linux-clients gebruiken.

## <a name="very-high-latency-for-requests"></a>Zeer hoge latentie voor aanvragen

### <a name="cause"></a>Oorzaak

De client-VM kan zich in een andere regio bevinden dan de bestandsshare.

### <a name="solution"></a>Oplossing

- Voer de toepassing uit vanaf een vm die zich in hetzelfde gebied bevindt als de bestandsshare.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Client kan maximale doorvoer niet bereiken die door het netwerk wordt ondersteund

Een mogelijke oorzaak hiervan is een gebrek aan SMB multi-channel ondersteuning. Op dit moment ondersteunen Azure-bestandsshares slechts één kanaal, dus er is slechts één verbinding van de client-VM naar de server. Deze enkele verbinding is gekoppeld aan één kern op de client-VM, zodat de maximale doorvoer die mogelijk is vanuit een VM wordt gekoppeld aan één kern.

### <a name="workaround"></a>Tijdelijke oplossing

- Het verkrijgen van een VM met een grotere kern kan helpen bij het verbeteren van de doorvoer.
- Als u de clienttoepassing uitvoert vanaf meerdere VM's, wordt de doorvoer verhoogd.

- Gebruik WAAR mogelijk REST API's.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>De doorvoer op Linux-clients is aanzienlijk lager in vergelijking met Windows-clients.

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de implementatie van SMB client op Linux.

### <a name="workaround"></a>Tijdelijke oplossing

- Verdeel de belasting over meerdere VM's.
- Gebruik op dezelfde VM meerdere bevestigingspunten met de optie **nosharesock** en spreid de belasting over deze bevestigingspunten.
- Op Linux, probeer montage met **nostrictsync** optie om te voorkomen dat het forceren van SMB flush op elke **fsync** call. Voor Azure Files verstoort deze optie de consistentie van gegevens niet, maar kan dit resulteren in verouderde bestandsmetagegevens in de mapvermelding **(opdracht ls -l).** Als u direct metagegevens van het bestand opvraagt **(stat-opdracht)** worden de meest up-to-date bestandsmetagegevens retourneren.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hoge latencies voor metadata zware workloads met uitgebreide open / close operaties.

### <a name="cause"></a>Oorzaak

Gebrek aan ondersteuning voor directory leases.

### <a name="workaround"></a>Tijdelijke oplossing

- Vermijd indien mogelijk een overmatige openings-/sluitingshendel op dezelfde map binnen een korte periode.
- Voor Linux VM's verhoogt u de time-out van de directoryentry-cache door **actimeo=\<sec->** op te geven als een mount-optie. Standaard is het een seconde, dus een grotere waarde zoals drie of vijf zou kunnen helpen.
- Voor Linux VM's, upgrade de kernel naar 4.20 of hoger.

## <a name="low-iops-on-centosrhel"></a>Lage IOPS op CentOS/RHEL

### <a name="cause"></a>Oorzaak

IO-diepte groter dan één wordt niet ondersteund op CentOS/RHEL.

### <a name="workaround"></a>Tijdelijke oplossing

- Upgrade naar CentOS 8 / RHEL 8.
- Overstappen naar Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Langzaam kopiëren van bestanden van en naar Azure-bestanden in Linux

Als u te maken ondervindt van langzaam kopiëren van bestanden van en naar Azure Files, bekijkt u de sectie [Langzaam kopiëren van bestanden van en naar Azure Files in Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) in de handleiding voor het oplossen van problemen met Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Zenuwachtig / zaagtand patroon voor IOPS

### <a name="cause"></a>Oorzaak

Clienttoepassing overschrijdt consistent baseline IOPS. Momenteel is er geen servicezijde smoothing van de aanvraagbelasting, dus als de client de baseline IOPS overschrijdt, wordt deze beperkt door de service. Dat throttling kan resulteren in de klant ervaren van een zenuwachtig / zaag-tand IOPS patroon. In dit geval kan de gemiddelde IOPS die door de client wordt bereikt, lager zijn dan de IOPS-basislijn.

### <a name="workaround"></a>Tijdelijke oplossing

- Verlaag de aanvraagbelasting van de clienttoepassing, zodat het aandeel niet wordt beperkt.
- Verhoog het quotum van het aandeel, zodat het aandeel niet wordt beperkt.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Overmatige aanroepen van DirectoryOpen/DirectoryClose

### <a name="cause"></a>Oorzaak

Als het aantal DirectoryOpen/DirectoryClose-aanroepen tot de top API-aanroepen behoort en u niet verwacht dat de client zoveel aanzet, kan het een probleem zijn met de antivirus die is geïnstalleerd op de Azure-client-VM.

### <a name="workaround"></a>Tijdelijke oplossing

- Een oplossing voor dit probleem is beschikbaar in de [April Platform Update voor Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Het maken van bestanden verloopt trager dan verwacht

### <a name="cause"></a>Oorzaak

Workloads die afhankelijk zijn van het maken van een groot aantal bestanden, zien geen wezenlijk verschil tussen de prestaties van premium bestandsshares en standaardbestandsshares.

### <a name="workaround"></a>Tijdelijke oplossing

- Geen.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Trage prestaties van Windows 8.1 of Server 2012 R2

### <a name="cause"></a>Oorzaak

Hoger dan verwachte latentie toegang tot Azure Files voor IO-intensieve workloads.

### <a name="workaround"></a>Tijdelijke oplossing

- Installeer de beschikbare [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Een waarschuwing maken als een bestandsshare wordt beperkt

1. Klik in de [Azure-portal](https://portal.azure.com)op **Monitor**. 

2. Klik **op Waarschuwingen** en klik vervolgens op **+ Nieuwe waarschuwingsregel**.

3. Klik **op Selecteren** om de **opslagaccount/bestandsbron** te selecteren die de bestandsshare bevat waarop u wilt waarschuwen en klik vervolgens op **Gereed**. Als de naam van het opslagaccount bijvoorbeeld contoso is, selecteert u de contoso/bestandsbron.

4. Klik **op Toevoegen** om een voorwaarde toe te voegen.

5. U ziet een lijst met signalen die worden ondersteund voor het opslagaccount en selecteert u de statistiek **Transacties.**

6. Ga in het **tekenblad Signaallogica** configureren naar de dimensie **Antwoordtype,** klik op de vervolgkeuzelijst **Dimensiewaarden** en selecteer **SuccessWithThrottling** (voor SMB) of **ClientThrottlingError** (voor REST). 

  > [!NOTE]
  > Als de dimensiewaarde SuccessWithThrottling of ClientThrottlingError niet wordt vermeld, betekent dit dat de resource niet is beperkt.  Als u de dimensiewaarde **+** wilt toevoegen, klikt u op de vervolgkeuzelijst Naast de waarden **van** dimensie, typt u **SuccessWithThrottling** of **ClientThrottlingError**, klikt u op **OK** en herhaalt u stap #6.

7. Ga naar de dimensie **Bestandsdelen,** klik op de vervolgkeuzelijst **Dimensiewaarden** en selecteer de bestandsshare(s) waarop u wilt waarschuwen. 

  > [!NOTE]
  > Als de bestandsshare een standaardbestandsshare is, zijn de vervolgkeuzewaarden voor dimensiewaarden leeg omdat metrische gegevens per aandeel niet beschikbaar zijn voor standaardbestandsshares. Throttling waarschuwingen voor standaard bestandsshares worden geactiveerd als een bestandsaandeel binnen het opslagaccount wordt beperkt en de waarschuwing niet identificeert welk bestandsaandeel is beperkt. Aangezien statistieken per aandeel niet beschikbaar zijn voor standaardbestandsshares, is de aanbeveling om één bestandsaandeel per opslagaccount te hebben. 

8. Definieer de **waarschuwingsparameters** (drempel, operator, aggregatiegranulariteit en frequentie) die worden gebruikt om de metrische waarschuwingsregel te evalueren en klik op **Gereed**.

  > [!TIP]
  > Als u een statische drempelwaarde gebruikt, kan de metrische grafiek helpen bij het bepalen van een redelijke drempelwaarde als het bestandsaandeel momenteel wordt beperkt. Als u een dynamische drempelwaarde gebruikt, worden in de metrische grafiek de berekende drempelwaarden weergegeven op basis van recente gegevens.

9. Voeg een **actiegroep** (e-mail, sms, enz.) toe aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.

10. Vul de **waarschuwingsgegevens** in, zoals **de naam van de waarschuwingsregel**, **Beschrijving** en **Ernst**.

11. Klik **op Waarschuwingsregel maken** om de waarschuwing te maken.

Zie [Overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in Azure Monitor.
