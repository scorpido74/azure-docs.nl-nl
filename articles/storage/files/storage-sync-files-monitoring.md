---
title: Azure File Sync bewaken | Microsoft Docs
description: Azure File Sync bewaken.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515200"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync bewaken

Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken voor toegang tot uw gegevens lokaal, zoals SMB, NFS en FTPS. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

In dit artikel wordt beschreven hoe u uw Azure File Sync-implementatie bewaakt met behulp van Azure Monitor, de opslag synchronisatie service en Windows Server.

De volgende bewakings opties zijn momenteel beschikbaar.

## <a name="azure-monitor"></a>Azure Monitor

Gebruik [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor het weer geven van metrische gegevens en voor het configureren van waarschuwingen voor synchronisatie, Cloud lagen en server verbindingen.  

### <a name="metrics"></a>Metrische gegevens

De metrische gegevens voor Azure File Sync zijn standaard ingeschakeld en worden verzonden naar Azure Monitor om de 15 minuten.

Als u Azure File Sync metrische gegevens in Azure Monitor wilt weer geven, selecteert u het resource type **Storage Sync Services** .

De volgende metrische gegevens voor Azure File Sync zijn beschikbaar in Azure Monitor:

| Naam van metrische gegevens | Beschrijving |
|-|-|
| Gesynchroniseerde bytes | Grootte van de overgedragen gegevens (uploaden en downloaden).<br><br>Eenheid: bytes<br>Aggregatie type: Sum<br>Toepasselijke dimensies: naam server eindpunt, synchronisatie richting, naam synchronisatie groep |
| Cloud lagen intrekken | De grootte van de gegevens die worden ingetrokken.<br><br>**Opmerking**: deze metrische gegevens worden in de toekomst verwijderd. Gebruik de grootte van de Cloud-laag voor het intrekken van de grootte van gegevens die zijn ingetrokken.<br><br>Eenheid: bytes<br>Aggregatie type: Sum<br>Toepasselijke dimensie: Server naam |
| Grootte van intrekken Cloud lagen | De grootte van de gegevens die worden ingetrokken.<br><br>Eenheid: bytes<br>Aggregatie type: Sum<br>Toepasselijke dimensie: Server naam, naam van synchronisatie groep |
| Grootte van intrekken van Cloud lagen op toepassing | De grootte van de gegevens die worden ingetrokken door de toepassing.<br><br>Eenheid: bytes<br>Aggregatie type: Sum<br>Toepasselijke dimensie: toepassings naam, Server naam, naam synchronisatie groep |
| Door Voer van Cloud lagen intrekken | Grootte van gegevens intrekken doorvoer snelheid.<br><br>Eenheid: bytes<br>Aggregatie type: Sum<br>Toepasselijke dimensie: Server naam, naam van synchronisatie groep |
| Bestanden die niet worden gesynchroniseerd | Het aantal bestanden dat niet kan worden gesynchroniseerd.<br><br>Eenheid: aantal<br>Aggregatie type: Sum<br>Toepasselijke dimensies: naam server eindpunt, synchronisatie richting, naam synchronisatie groep |
| Gesynchroniseerde bestanden | Aantal overgebrachte bestanden (uploaden en downloaden).<br><br>Eenheid: aantal<br>Aggregatie type: Sum<br>Toepasselijke dimensies: naam server eindpunt, synchronisatie richting, naam synchronisatie groep |
| Online status van de server | Aantal heartbeats dat is ontvangen van de server.<br><br>Eenheid: aantal<br>Aggregatie type: maximum<br>Toepasselijke dimensie: Server naam |
| Resultaat van synchronisatie sessie | Resultaat van synchronisatie sessie (1 = synchronisatie sessie geslaagd; 0 = synchronisatie sessie mislukt)<br><br>Eenheid: aantal<br>Aggregatie typen: maximum<br>Toepasselijke dimensies: naam server eindpunt, synchronisatie richting, naam synchronisatie groep |

### <a name="alerts"></a>Waarschuwingen

Als u waarschuwingen in Azure Monitor wilt configureren, selecteert u de opslag synchronisatie service en selecteert u vervolgens de [Azure file sync metriek](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) die voor de waarschuwing moet worden gebruikt.  

De volgende tabel bevat enkele voor beelden van scenario's om te controleren en de juiste meet waarde voor de waarschuwing:

| Scenario | De metrische waarde die voor de waarschuwing moet worden gebruikt |
|-|-|
| Status van server eindpunt in de portal = fout | Resultaat van synchronisatie sessie |
| Bestanden kunnen niet worden gesynchroniseerd met een server of een eind punt in de Cloud | Bestanden die niet worden gesynchroniseerd |
| De geregistreerde server kan niet communiceren met de opslag synchronisatie service | Online status van de server |
| De grootte van het intrekken van Cloud lagen is 500GiB op een dag overschreden  | Grootte van intrekken Cloud lagen |

Zie [overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in azure monitor.

## <a name="storage-sync-service"></a>Opslagsynchronisatieservice

Als u de status van de geregistreerde server, de status van de server eindpunt en de metrische gegevens wilt bekijken, gaat u naar de opslag synchronisatie service in de Azure Portal. U kunt de geregistreerde server status weer geven op de Blade **geregistreerde servers** en server eindpunt status op de Blade **synchronisatie groepen** .

### <a name="registered-server-health"></a>Status van geregistreerde server

- Als de status van de **geregistreerde server** **online**is, communiceert de server met succes met de service.
- Als de status van de **geregistreerde server** **offline wordt weer gegeven**, controleert u of het AzureStorageSyncMonitor.exe-proces (Storage sync monitor) op de server wordt uitgevoerd. Als de-server zich achter een firewall of proxy bevindt, raadpleegt u [dit artikel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) voor het configureren van de firewall en proxy.

### <a name="server-endpoint-health"></a>Status van server eindpunt

- De eindpunt status van de server in de portal is gebaseerd op de synchronisatie gebeurtenissen die worden vastgelegd in het logboek voor telemetrie op de server (ID 9102 en 9302). Als een synchronisatie sessie mislukt als gevolg van een tijdelijke fout, zoals het annuleren van de fout, wordt de synchronisatie mogelijk nog steeds in orde weer gegeven in de portal zolang de huidige synchronisatie sessie de voortgang wordt gemaakt. Gebeurtenis-ID 9302 wordt gebruikt om te bepalen of er bestanden worden toegepast. Zie [synchronisatie status](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [voortgang van synchronisatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)voor meer informatie.
- Raadpleeg de [documentatie voor probleem oplossing](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) voor hulp bij het maken van een synchronisatie fout, omdat de synchronisatie niet wordt uitgevoerd.

### <a name="metric-charts"></a>Metrische grafieken

- De volgende metrische grafieken kunnen worden weer gegeven in de portal van de opslag synchronisatie service:

  | Naam van metrische gegevens | Beschrijving | Naam Blade |
  |-|-|-|
  | Gesynchroniseerde bytes | Grootte van overgedragen gegevens (uploaden en downloaden) | Synchronisatie groep, Server eindpunt |
  | Cloud lagen intrekken | Grootte van gegevens die zijn ingetrokken | Geregistreerde servers |
  | Bestanden die niet worden gesynchroniseerd | Aantal bestanden dat niet kan worden gesynchroniseerd | Server eindpunt |
  | Gesynchroniseerde bestanden | Aantal overgebrachte bestanden (uploaden en downloaden) | Synchronisatie groep, Server eindpunt |
  | Online status van de server | Aantal heartbeats dat is ontvangen van de server | Geregistreerde servers |

- Zie [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)voor meer informatie.

  > [!Note]  
  > De grafieken in de opslag synchronisatie Service Portal hebben een tijds bereik van 24 uur. Gebruik Azure Monitor om verschillende Peri Oden of dimensies weer te geven.

## <a name="windows-server"></a>Windows Server

Op Windows Server kunt u Cloud lagen, geregistreerde servers en synchronisatie status weer geven.

### <a name="event-logs"></a>Gebeurtenislogboeken

Gebruik het telemetrie-gebeurtenis logboek op de server om de geregistreerde server-, synchronisatie-en Cloud-laag status te bewaken. Het logboek voor telemetrie bevindt zich in Logboeken onder *toepassingen en Services\Microsoft\FileSync\Agent*.

Synchronisatie status:

- Gebeurtenis-ID 9102 wordt vastgelegd nadat een synchronisatie sessie is voltooid. Gebruik deze gebeurtenis om te bepalen of synchronisatie sessies zijn geslaagd (**HResult = 0**) en of er synchronisatie fouten per item zijn. Zie de documentatie [synchronisatie status](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [fouten per artikel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) voor meer informatie.

  > [!Note]  
  > Soms mislukken synchronisatie sessies volledig of hebben ze een niet-nul-PerItemErrorCount. Ze maken echter nog steeds een voortgang en sommige bestanden zijn gesynchroniseerd. U kunt dit zien in de velden toegepast zoals AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes. In deze velden kunt u zien hoeveel van de sessie is geslaagd. Als er meerdere synchronisatie sessies mislukken in een rij en deze een toenemend aantal toegewezen aantallen hebben, geeft u de synchronisatie tijd om het opnieuw te proberen voordat u een ondersteunings ticket opent.

- Gebeurtenis-ID 9302 wordt elke 5 tot 10 minuten geregistreerd als er een actieve synchronisatie sessie is. Gebruik deze gebeurtenis om te bepalen of de huidige synchronisatie sessie voortgang maakt (**AppliedItemCount > 0**). Als de synchronisatie niet wordt uitgevoerd, wordt de synchronisatie sessie uiteindelijk mislukt en wordt er een gebeurtenis-ID 9102 in het logboek geregistreerd met de fout. Zie de [voortgangs documentatie over synchronisatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)voor meer informatie.

Status van geregistreerde server:

- Gebeurtenis-ID 9301 wordt elke 30 seconden geregistreerd wanneer een server de service voor taken opvraagt. Als GetNextJob eindigt met **status = 0**, kan de server met de service communiceren. Als GetNextJob met een fout is voltooid, raadpleegt u de [documentatie voor probleem oplossing](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) voor hulp.

Status van Cloud lagen:

- Als u de activiteit voor het bijhouden van lagen op een server wilt bewaken, gebruikt u gebeurtenis-ID 9003, 9016 en 9029 in het logboek voor telemetrie, dat zich bevindt in Logboeken onder *toepassingen en Services\Microsoft\FileSync\Agent*.

  - Gebeurtenis-ID 9003 biedt fout distributie voor een server eindpunt. Bijvoorbeeld: totale aantal fouten en fout code. Er wordt één gebeurtenis per fout code in het logboek geregistreerd.
  - Gebeurtenis-ID 9016 bevat spook resultaten voor een volume. Bijvoorbeeld: percentage beschik bare ruimte is het aantal bestanden dat in de sessie is gedupliceerd en het aantal bestanden dat niet kan worden Ghost.
  - Gebeurtenis-ID 9029 bevat informatie over ghost-sessies voor een server eindpunt. Bijvoorbeeld: het aantal bestanden dat is geprobeerd in de sessie, het aantal bestanden dat in de sessie is gelaag en het aantal bestanden dat al is gelaagd.
  
- Gebruik gebeurtenis-ID 9005, 9006, 9009 en 9059 in het logboek voor telemetrie, dat zich bevindt in Logboeken onder *toepassingen en Services\Microsoft\FileSync\Agent*om de intrek activiteit op een server te controleren.

  - Gebeurtenis-ID 9005 biedt betrouw baarheid van een server eindpunt. Bijvoorbeeld: totale aantal bezochte unieke bestanden en totale aantal unieke bestanden met mislukte toegang.
  - Gebeurtenis-ID 9006 biedt fout bij het intrekken van fouten voor een server eindpunt. Bijvoorbeeld: totaal aantal mislukte aanvragen en error code. Er wordt één gebeurtenis per fout code in het logboek geregistreerd.
  - Gebeurtenis-ID 9009 biedt informatie over het intrekken van sessies voor een server eindpunt. Bijvoorbeeld: DurationSeconds, CountFilesRecallSucceeded en CountFilesRecallFailed.
  - Met gebeurtenis-ID 9059 kan de toepassing worden gedistribueerd voor een server eindpunt. Bijvoorbeeld: ShareId, toepassings naam en TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestatiemeteritems

Gebruik de prestatie meter items Azure File Sync op de server om de synchronisatie activiteit te controleren.

Als u de prestatie meter items van Azure File Sync op de server wilt weer geven, opent u prestatie meter (Perfmon.exe). U vindt de prestatie meter items onder de **overgebrachte AFS-bytes** en de objecten voor **AFS-synchronisatie bewerkingen** .

De volgende prestatie meter items voor Azure File Sync zijn beschikbaar in prestatie meter:

| Object\Counter naam van prestaties | Beschrijving |
|-|-|
| AFS bytes Transferred\Downloaded bytes per seconde | Aantal gedownloade bytes per seconde. |
| AFS bytes Transferred\Uploaded bytes per seconde | Aantal geüploade bytes per seconde. |
| AFS bytes Transferred\Total bytes per seconde | Totaal aantal bytes per seconde (uploaden en downloaden). |
| Operations\Downloaded-synchronisatie bestanden voor AFS-synchronisatie per seconde | Het aantal bestanden dat per seconde wordt gedownload. |
| Operations\Uploaded-synchronisatie bestanden voor AFS-synchronisatie per seconde | Het aantal bestanden dat per seconde is geüpload. |
| Bewerking van Operations\Total voor AFS-synchronisatie bestanden per seconde | Totaal aantal gesynchroniseerde bestanden (uploaden en downloaden). |

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Firewall-en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)
- [Veelgestelde vragen over Azure Files](storage-files-faq.md)
