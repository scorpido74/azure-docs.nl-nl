---
title: Azure-bestandssynchronisatie controleren | Microsoft Documenten
description: Azure File Sync controleren.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699294"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync bewaken

Gebruik Azure File Sync om de bestandsshares van uw organisatie in Azure-bestanden te centraliseren, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, NFS en FTPS. Je zoveel caches hebben als je nodig hebt over de hele wereld.

In dit artikel wordt beschreven hoe u de implementatie van Azure File Sync controleren met Azure Monitor, Storage Sync Service en Windows Server.

De volgende bewakingsopties zijn momenteel beschikbaar.

## <a name="azure-monitor"></a>Azure Monitor

Gebruik [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) om statistieken weer te geven en waarschuwingen te configureren voor synchronisatie, cloudgelaagdheid en serverconnectiviteit.  

### <a name="metrics"></a>Metrische gegevens

Metrische gegevens voor Azure File Sync zijn standaard ingeschakeld en worden elke 15 minuten naar Azure Monitor verzonden.

Als u Azure File Sync-statistieken wilt weergeven in Azure Monitor, selecteert u het brontype **Storage Sync Services.**

De volgende statistieken voor Azure File Sync zijn beschikbaar in Azure Monitor:

| Naam van meetwaarde | Beschrijving |
|-|-|
| Bytes gesynchroniseerd | Grootte van de overgedragen gegevens (uploaden en downloaden).<br><br>Eenheid: Bytes<br>Aggregatietype: som<br>Toepasselijke afmetingen: servereindpuntnaam, synchronisatierichting, groepsnaam synchroniseren |
| Terugroepactie voor cloudtiering | Omvang van de gegevens teruggeroepen.<br><br>**Opmerking:** deze statistiek wordt in de toekomst verwijderd. Gebruik de statistiek Cloud tiering recall size om de grootte van de teruggeroepen gegevens te controleren.<br><br>Eenheid: Bytes<br>Aggregatietype: som<br>Toepasselijke dimensie: Servernaam |
| Cloud tiering recall grootte | Omvang van de gegevens teruggeroepen.<br><br>Eenheid: Bytes<br>Aggregatietype: som<br>Toepasselijke dimensie: Servernaam, Groepsnaam synchroniseren |
| Cloud tiering recall grootte per toepassing | Grootte van de gegevens die door toepassing worden teruggeroepen.<br><br>Eenheid: Bytes<br>Aggregatietype: som<br>Toepasselijke dimensie: toepassingsnaam, servernaam, naam van de synchronisatiegroep |
| Cloud tiering recall throughput Cloud tiering recall throughput Cloud tiering recall throughput Cloud tier | Grootte van de doorvoer van gegevensterugroepen.<br><br>Eenheid: Bytes<br>Aggregatietype: som<br>Toepasselijke dimensie: Servernaam, Groepsnaam synchroniseren |
| Bestanden die niet worden gesynchroniseerd | Aantal bestanden die niet worden gesynchroniseerd.<br><br>Eenheid: Aantal<br>Aggregatietype: som<br>Toepasselijke afmetingen: servereindpuntnaam, synchronisatierichting, groepsnaam synchroniseren |
| Bestanden gesynchroniseerd | Aantal overgedragen bestanden (uploaden en downloaden).<br><br>Eenheid: Aantal<br>Aggregatietype: som<br>Toepasselijke afmetingen: servereindpuntnaam, synchronisatierichting, groepsnaam synchroniseren |
| Server online status | Aantal hartslagen die van de server zijn ontvangen.<br><br>Eenheid: Aantal<br>Aggregatietype: maximum<br>Toepasselijke dimensie: Servernaam |
| Sessieresultaat synchroniseren | Sessieresultaat synchroniseren (1=geslaagde synchronisatiesessie; 0=mislukte synchronisatiesessie)<br><br>Eenheid: Aantal<br>Aggregatietypen: Maximum<br>Toepasselijke afmetingen: servereindpuntnaam, synchronisatierichting, groepsnaam synchroniseren |

### <a name="alerts"></a>Waarschuwingen

Als u waarschuwingen wilt configureren in Azure Monitor, selecteert u de opslagsynchronisatieservice en selecteert u vervolgens de [azure-bestandssynchronisatiestatistiek](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) die u voor de waarschuwing wilt gebruiken.  

In de volgende tabel worden enkele voorbeeldscenario's weergegeven die moeten worden gecontroleerd en de juiste statistiek die u voor de waarschuwing moet gebruiken:

| Scenario | Statistiek die u wilt gebruiken voor waarschuwing |
|-|-|
| Servereindpuntstatus in de portal = Fout | Sessieresultaat synchroniseren |
| Bestanden worden niet gesynchroniseerd met een server of eindpunt in de cloud | Bestanden die niet worden gesynchroniseerd |
| Geregistreerde server communiceert niet met de Storage Sync Service | Server online status |
| Cloud tiering recall grootte heeft overschreden 500GiB in een dag  | Cloud tiering recall grootte |

Zie [Overzicht van waarschuwingen in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in Azure Monitor.

## <a name="storage-sync-service"></a>Opslagsynchronisatieservice

Als u de status van de geregistreerde server, de status van het servereindpunt en de statistieken wilt weergeven, gaat u naar de opslagsynchronisatieservice in de Azure-portal. U de status van de geregistreerde server weergeven in het blade **van Geregistreerde servers** en de status van het eindpunt van de server in het blade van **synchronisatiegroepen.**

### <a name="registered-server-health"></a>Geregistreerde serverstatus

- Als de **status van de geregistreerde server** **online**is, communiceert de server met de service.
- Als de **status van de geregistreerde server** offline wordt **weergegeven,** controleert u of het proces voor opslagsynchronisatiemonitor (AzureStorageSyncMonitor.exe) op de server wordt uitgevoerd. Als de server zich achter een firewall of proxy bevindt, raadpleegt u [dit artikel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) om de firewall en proxy te configureren.

### <a name="server-endpoint-health"></a>Servereindpuntstatus

- De status van het servereindpunt in de portal is gebaseerd op de synchronisatiegebeurtenissen die zijn aangemeld bij het gebeurtenislogboek Telemetrie op de server (ID 9102 en 9302). Als een synchronisatiesessie mislukt vanwege een tijdelijke fout, zoals fout geannuleerd, kan synchronisatie nog steeds in orde worden weergegeven in de portal zolang de huidige synchronisatiesessie vooruitgang boekt. Gebeurtenis-id 9302 wordt gebruikt om te bepalen of bestanden worden toegepast. Zie [synchronisatiestatus](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [synchronisatievoortgang](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)voor meer informatie .
- Als de portal een synchronisatiefout vertoont omdat synchronisatie geen vooruitgang boekt, [raadpleegt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) u de documentatie voor het oplossen van problemen voor richtlijnen.

### <a name="metric-charts"></a>Metrische grafieken

- De volgende metrische grafieken zijn zichtbaar in de portal Storage Sync Service:

  | Naam van meetwaarde | Beschrijving | Bladnaam |
  |-|-|-|
  | Bytes gesynchroniseerd | Grootte van overgedragen gegevens (uploaden en downloaden) | Groep synchroniseren, Servereindpunt |
  | Terugroepactie voor cloudtiering | Omvang van de aangeroepen gegevens | Geregistreerde servers |
  | Bestanden die niet worden gesynchroniseerd | Aantal bestanden dat niet wordt gesynchroniseerd | Servereindpunt |
  | Bestanden gesynchroniseerd | Aantal overgedragen bestanden (uploaden en downloaden) | Groep synchroniseren, Servereindpunt |
  | Server online status | Aantal hartslagen ontvangen van de server | Geregistreerde servers |

- Zie [Azure Monitor voor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)meer informatie.

  > [!Note]  
  > De grafieken in de portal Storage Sync Service hebben een tijdsbereik van 24 uur. Als u verschillende tijdbereiken of dimensies wilt weergeven, gebruikt u Azure Monitor.

## <a name="windows-server"></a>Windows Server

Op Windows Server u de status cloudtiering, geregistreerde server en synchronisatie weergeven.

### <a name="event-logs"></a>Gebeurtenislogboeken

Gebruik het gebeurtenislogboek Telemetrie op de server om de status van geregistreerde server-, synchronisatie- en cloudgelaagdheid te controleren. Het gebeurtenislogboek Telemetrie bevindt zich in Logboeken onder *Toepassingen en Services\Microsoft\FileSync\Agent*.

Synchronisatiestatus:

- Gebeurtenis-id 9102 wordt geregistreerd nadat een synchronisatiesessie is voltooid. Gebruik deze gebeurtenis om te bepalen of synchronisatiesessies zijn geslaagd **(HResult = 0)** en of er synchronisatiefouten per item zijn. Zie de documentatie [synchronisatiestatus](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [fouten per item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) voor meer informatie.

  > [!Note]  
  > Soms mislukken synchronisatiesessies over het algemeen of hebben een niet-nul PerItemErrorCount. Ze boeken echter nog steeds vooruitgang en sommige bestanden worden gesynchroniseerd. U dit zien in de velden AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes. Deze velden vertellen u hoeveel van de sessie is geslaagd. Als u meerdere synchronisatiesessies op een rij ziet mislukken en het aantal toegepaste weergaven toeneemt, geeft u synchronisatietijd om het opnieuw te proberen voordat u een ondersteuningsticket opent.

- Gebeurtenis-ID 9302 wordt elke 5 tot 10 minuten geregistreerd als er een actieve synchronisatiesessie is. Gebruik deze gebeurtenis om te bepalen of de huidige synchronisatiesessie vooruitgang boekt **(AppliedItemCount > 0**). Als de synchronisatie geen vooruitgang boekt, moet de synchronisatiesessie uiteindelijk mislukken en wordt een gebeurtenis-id 9102 met de fout geregistreerd. Zie de documentatie [voor synchronisatievoortgang voor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)meer informatie .

Geregistreerde serverstatus:

- Gebeurtenis-id 9301 wordt elke 30 seconden geregistreerd wanneer een server de service voor taken opvraagt. Als GetNextJob klaar is met **status = 0,** kan de server communiceren met de service. Als GetNextJob met een fout is voltooid, controleert u de [probleemoplossingsdocumentatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) voor richtlijnen.

Status van cloudlaagniveau:

- Als u de gelaagdheidsactiviteit op een server wilt controleren, gebruikt u gebeurtenis-id 9003, 9016 en 9029 in het gebeurtenislogboek Telemetrie, dat zich in Logboeken bevindt onder *Toepassingen en Services\Microsoft\FileSync\Agent*.

  - Gebeurtenis-id 9003 biedt foutdistributie voor een servereindpunt. Bijvoorbeeld: Total Error Count en ErrorCode. Eén gebeurtenis wordt per foutcode geregistreerd.
  - Gebeurtenis-ID 9016 biedt ghosting resultaten voor een volume. Bijvoorbeeld: Vrije ruimte procent is, Aantal bestanden ghosted in sessie, en Het aantal bestanden niet ghost.
  - Gebeurtenis-ID 9029 biedt informatie over ghosting-sessies voor een servereindpunt. Bijvoorbeeld: aantal bestanden die in de sessie zijn geprobeerd, het aantal bestanden dat in de sessie is gelaagd en het aantal bestanden dat al is gelaagd.
  
- Als u de terugroepactiviteit op een server wilt controleren, gebruikt u gebeurtenis-id 9005, 9006, 9009 en 9059 in het gebeurtenislogboek Telemetrie, dat zich in Logboeken bevindt onder *Toepassingen en Services\Microsoft\FileSync\Agent*.

  - Gebeurtenis-ID 9005 biedt de betrouwbaarheid van een server voor een servereindpunt. Bijvoorbeeld: Totaal aantal geopende unieke bestanden en Totaal unieke bestanden met mislukte toegang.
  - Gebeurtenis-id 9006 biedt de distributie van terugroepfouten voor een servereindpunt. Bijvoorbeeld: Total Failed Requests en ErrorCode. Eén gebeurtenis wordt per foutcode geregistreerd.
  - Gebeurtenis-ID 9009 biedt informatie over terugroepsessies voor een servereindpunt. Bijvoorbeeld: DurationSeconds, CountFilesRecallGeslaagd en CountFilesRecallFailed.
  - Gebeurtenis-id 9059 biedt de distributie van toepassingsterugroepinformatie voor een servereindpunt. Bijvoorbeeld: ShareId, Application Name en TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Prestatiemeteritems

Gebruik de prestatiemeteritems azure bestandssynchronisatie op de server om synchronisatieactiviteiten te controleren.

Als u de prestatiemeteritems voor Azure File Sync op de server wilt weergeven, opent u Prestatiemonitor (Perfmon.exe). U de tellers vinden onder de objecten **AFS Bytes Transferred** en **AFS Sync Operations.**

De volgende prestatiemeteritems voor Azure File Sync zijn beschikbaar in prestatiemeter:

| Prestatieobject\Voornaam | Beschrijving |
|-|-|
| AFS Bytes overgedragen\Gedownloade bytes per seconde | Aantal bytes dat per seconde wordt gedownload. |
| AFS Bytes overgedragen\Geüploade bytes per seconde | Aantal geüploade bytes per seconde. |
| AFS-bytes overgedragen\Totale aantal bytes per seconde | Totaal aantal bytes per seconde (uploaden en downloaden). |
| AFS-synchronisatiebewerkingen\Gedownloade synchronisatiebestanden per seconde | Aantal gedownloade bestanden per seconde. |
| AFS-synchronisatiebewerkingen\Geüploade synchronisatiebestanden per seconde | Aantal bestanden dat per seconde wordt geüpload. |
| AFS-synchronisatiebewerkingen\Total Sync File Operations/sec | Totaal aantal gesynchroniseerde bestanden (uploaden en downloaden). |

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Firewall- en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)
- [Veelgestelde vragen in Azure Files](storage-files-faq.md)
