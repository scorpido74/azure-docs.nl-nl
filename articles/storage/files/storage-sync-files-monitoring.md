---
title: Azure File Sync bewaken | Microsoft Docs
description: Lees hoe u uw Azure File Sync-implementatie kunt controleren met behulp van Azure Monitor, de opslag synchronisatie service en Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d7b29bbd508223888c6f205e25008c0b29fecea
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922931"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync bewaken

Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

In dit artikel wordt beschreven hoe u uw Azure File Sync-implementatie bewaakt met behulp van Azure Monitor, de opslag synchronisatie service en Windows Server.

In deze hand leiding worden de volgende scenario's behandeld: 
- Azure File Sync metrische gegevens weer geven in Azure Monitor.
- Maak waarschuwingen in Azure Monitor om u proactief te informeren over kritieke omstandigheden.
- Bekijk de status van uw Azure File Sync-implementatie met behulp van de Azure Portal.
- De gebeurtenis logboeken en prestatie meter items op uw Windows-servers gebruiken om de status van uw Azure File Sync-implementatie te controleren. 

## <a name="azure-monitor"></a>Azure Monitor

Gebruik [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor het weer geven van metrische gegevens en voor het configureren van waarschuwingen voor synchronisatie, Cloud lagen en server verbindingen.  

### <a name="metrics"></a>Metrische gegevens

De metrische gegevens voor Azure File Sync zijn standaard ingeschakeld en worden verzonden naar Azure Monitor om de 15 minuten.

**Azure File Sync metrische gegevens weer geven in Azure Monitor**
- Ga naar de **opslag synchronisatie service** in de **Azure Portal** en klik op **metrische gegevens**.
- Klik op de vervolg keuzelijst **metriek** en selecteer de metrische gegevens die u wilt weer geven.

De volgende metrische gegevens voor Azure File Sync zijn beschikbaar in Azure Monitor:

| Naam van meetwaarde | Beschrijving |
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

Waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Zie [overzicht van waarschuwingen in Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)voor meer informatie over het configureren van waarschuwingen in azure monitor.

**Waarschuwingen voor Azure File Sync maken**

- Ga naar de **opslag synchronisatie service** in de **Azure Portal**. 
- Klik in het gedeelte bewaking op **waarschuwingen** en klik vervolgens op **+ nieuwe waarschuwings regel**.
- Klik op **voor waarde selecteren** en geef de volgende informatie op voor de waarschuwing: 
    - **Meting**
    - **Dimensie naam**
    - **Waarschuwings logica**
- Klik op **actie groep selecteren** en voeg een actie groep (E-mail, SMS, enzovoort) toe aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
- Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
- Klik op **waarschuwings regel maken** om de waarschuwing te maken.  

De volgende tabel bevat enkele voor beelden van scenario's om te controleren en de juiste meet waarde voor de waarschuwing:

| Scenario | De metrische waarde die voor de waarschuwing moet worden gebruikt |
|-|-|
| De status van het server eindpunt bevat een fout in de portal | Resultaat van synchronisatie sessie |
| Bestanden kunnen niet worden gesynchroniseerd met een server of een eind punt in de Cloud | Bestanden die niet worden gesynchroniseerd |
| De geregistreerde server kan niet communiceren met de opslag synchronisatie service | Online status van de server |
| De grootte van het intrekken van Cloud lagen is 500GiB op een dag overschreden  | Grootte van intrekken Cloud lagen |

Zie de sectie voor [beelden van waarschuwingen](#alert-examples) voor instructies over het maken van waarschuwingen voor deze scenario's.

## <a name="storage-sync-service"></a>Opslagsynchronisatieservice

Als u de status van de geregistreerde server, de status van de server eindpunt en de metrische gegevens wilt bekijken, gaat u naar de opslag synchronisatie service in de Azure Portal. U kunt de geregistreerde server status weer geven op de Blade **geregistreerde servers** en server eindpunt status op de Blade **synchronisatie groepen** .

### <a name="registered-server-health"></a>Status van geregistreerde server

- Als de status van de **geregistreerde server** **online**is, communiceert de server met succes met de service.
- Als de status van de **geregistreerde server** **offline wordt weer gegeven**, is het proces voor het controleren van de opslag synchronisatie (AzureStorageSyncMonitor.exe) niet actief of heeft de server geen toegang tot de Azure File Sync-Service. Raadpleeg de [documentatie voor probleem oplossing](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) voor hulp.

### <a name="server-endpoint-health"></a>Status van server eindpunt

- De eindpunt status van de server in de portal is gebaseerd op de synchronisatie gebeurtenissen die worden vastgelegd in het logboek voor telemetrie op de server (ID 9102 en 9302). Als een synchronisatie sessie mislukt als gevolg van een tijdelijke fout, zoals het annuleren van de fout, wordt de synchronisatie mogelijk nog steeds in orde weer gegeven in de portal zolang de huidige synchronisatie sessie de voortgang wordt gemaakt. Gebeurtenis-ID 9302 wordt gebruikt om te bepalen of er bestanden worden toegepast. Zie [synchronisatie status](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [voortgang van synchronisatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)voor meer informatie.
- Raadpleeg de [documentatie voor probleem oplossing](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) voor hulp bij het maken van een synchronisatie fout, omdat de synchronisatie niet wordt uitgevoerd.

### <a name="metric-charts"></a>Metrische grafieken

- De volgende metrische grafieken kunnen worden weer gegeven in de portal van de opslag synchronisatie service:

  | Naam van meetwaarde | Beschrijving | Naam Blade |
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

Op de Windows-Server waarop de Azure File Sync-agent is geïnstalleerd, kunt u Cloud lagen, geregistreerde servers en synchronisatie status weer geven.

### <a name="event-logs"></a>Gebeurtenislogboeken

Gebruik het telemetrie-gebeurtenis logboek op de server om de geregistreerde server-, synchronisatie-en Cloud-laag status te bewaken. Het logboek voor telemetrie bevindt zich in Logboeken onder *toepassingen en Services\Microsoft\FileSync\Agent*.

Synchronisatie status:

- Gebeurtenis-ID 9102 wordt vastgelegd zodra een synchronisatie sessie is voltooid. Gebruik deze gebeurtenis om te bepalen of synchronisatie sessies zijn geslaagd (**HResult = 0**) en of er synchronisatie fouten per item zijn. Zie de documentatie [synchronisatie status](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) en [fouten per artikel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) voor meer informatie.

  > [!Note]  
  > Soms mislukken synchronisatie sessies volledig of hebben ze een niet-nul-PerItemErrorCount. Ze maken echter nog steeds een voortgang en sommige bestanden zijn gesynchroniseerd. U kunt dit zien in de velden toegepast zoals AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes. In deze velden kunt u zien hoeveel van de sessie is geslaagd. Als er meerdere synchronisatie sessies mislukken in een rij en deze een toenemend aantal toegewezen aantallen hebben, geeft u de synchronisatie tijd om het opnieuw te proberen voordat u een ondersteunings ticket opent.

- Gebeurtenis-ID 9121 wordt vastgelegd voor elk per-item-fout zodra de synchronisatie sessie is voltooid. Gebruik deze gebeurtenis om te bepalen hoeveel bestanden niet kunnen worden gesynchroniseerd met deze fout (**PersistentCount** en **TransientCount**). Permanente fouten per item moeten worden onderzocht, Zie [Hoe kan ik controleren of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Gebeurtenis-ID 9302 wordt elke 5 tot 10 minuten geregistreerd als er een actieve synchronisatie sessie is. Gebruik deze gebeurtenis om te bepalen of de huidige synchronisatie sessie voortgang maakt (**AppliedItemCount > 0**). Als de synchronisatie niet wordt uitgevoerd, wordt de synchronisatie sessie uiteindelijk mislukt en wordt er een gebeurtenis-ID 9102 in het logboek geregistreerd met de fout. Zie de [voortgangs documentatie over synchronisatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)voor meer informatie.

Status van geregistreerde server:

- Gebeurtenis-ID 9301 wordt elke 30 seconden geregistreerd wanneer een server de service voor taken opvraagt. Als GetNextJob eindigt met **status = 0**, kan de server met de service communiceren. Als GetNextJob met een fout is voltooid, raadpleegt u de [documentatie voor probleem oplossing](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) voor hulp.

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

## <a name="alert-examples"></a>Waarschuwings voorbeelden
Deze sectie bevat enkele voor beelden van waarschuwingen voor Azure File Sync.

  > [!Note]  
  > Als u een waarschuwing maakt en er te veel ruis is, past u de drempel waarde en de logica van de waarschuwing aan.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Een waarschuwing maken als de eindpunt status van de server een fout in de portal weergeeft

1. Ga in het **Azure Portal**naar de betreffende **opslag synchronisatie service**. 
2. Ga naar de sectie **bewaking** en klik op **waarschuwingen**. 
3. Klik op **+ nieuwe waarschuwings regel** om een nieuwe waarschuwings regel te maken. 
4. Configureer de voor waarde door te klikken op **voor waarde selecteren**.
5. Klik in de Blade **signaal logica configureren** op **synchronisatie sessie resultaat** onder signaal naam.  
6. Selecteer de volgende dimensie configuratie: 
    - Dimensie naam: **naam van server eindpunt**  
    - And**=** 
    - Dimensie waarden: **alle huidige en toekomstige waarden**  
7. Navigeer naar **waarschuwings logica** en voer de volgende handelingen uit: 
    - Drempel ingesteld op **statisch** 
    - Operator: **kleiner dan** 
    - Aggregatie type: **maximum**  
    - Drempel waarde: **1** 
    - Geëvalueerd op basis van: aggregatie granulatie = **24 uur** | Frequentie van evaluatie = **elk uur** 
    - Klik op **gereed.** 
8. Klik op **actie groep selecteren** om een actie groep (E-mail, SMS, enzovoort) toe te voegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
9. Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Een waarschuwing maken als bestanden niet kunnen worden gesynchroniseerd met een server of een eind punt in de Cloud

1. Ga in het **Azure Portal**naar de betreffende **opslag synchronisatie service**. 
2. Ga naar de sectie **bewaking** en klik op **waarschuwingen**. 
3. Klik op **+ nieuwe waarschuwings regel** om een nieuwe waarschuwings regel te maken. 
4. Configureer de voor waarde door te klikken op **voor waarde selecteren**.
5. Klik in de Blade **signaal logica configureren** op **bestanden niet synchroniseren** onder signaal naam.  
6. Selecteer de volgende dimensie configuratie: 
     - Dimensie naam: **naam van server eindpunt**  
     - And**=** 
     - Dimensie waarden: **alle huidige en toekomstige waarden**  
7. Navigeer naar **waarschuwings logica** en voer de volgende handelingen uit: 
     - Drempel ingesteld op **statisch** 
     - Operator: **groter dan** 
     - Aggregatie type: **totaal**  
     - Drempel waarde: **100** 
     - Geëvalueerd op basis van: aggregatie granulatie = **5 minuten** | Evaluatie frequentie = **elke 5 minuten** 
     - Klik op **gereed.** 
8. Klik op **actie groep selecteren** om een actie groep (E-mail, SMS, enzovoort) toe te voegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
9. Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Een waarschuwing maken als een geregistreerde server niet kan communiceren met de opslag synchronisatie service

1. Ga in het **Azure Portal**naar de betreffende **opslag synchronisatie service**. 
2. Ga naar de sectie **bewaking** en klik op **waarschuwingen**. 
3. Klik op **+ nieuwe waarschuwings regel** om een nieuwe waarschuwings regel te maken. 
4. Configureer de voor waarde door te klikken op **voor waarde selecteren**.
5. Klik in de Blade **signaal logica configureren** op **server online status** onder signaal naam.  
6. Selecteer de volgende dimensie configuratie: 
     - Dimensie naam: **Server naam**  
     - And**=** 
     - Dimensie waarden: **alle huidige en toekomstige waarden**  
7. Navigeer naar **waarschuwings logica** en voer de volgende handelingen uit: 
     - Drempel ingesteld op **statisch** 
     - Operator: **kleiner dan** 
     - Aggregatie type: **maximum**  
     - Drempel waarde (in bytes): **1** 
     - Geëvalueerd op basis van: aggregatie granulatie = **1 uur** | Evaluatie frequentie = **elke 30 minuten** 
     - Klik op **gereed.** 
8. Klik op **actie groep selecteren** om een actie groep (E-mail, SMS, enzovoort) toe te voegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
9. Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Een waarschuwing maken als de intrek grootte van de Cloud lagen 500GiB op een dag is overschreden

1. Ga in het **Azure Portal**naar de betreffende **opslag synchronisatie service**. 
2. Ga naar de sectie **bewaking** en klik op **waarschuwingen**. 
3. Klik op **+ nieuwe waarschuwings regel** om een nieuwe waarschuwings regel te maken. 
4. Configureer de voor waarde door te klikken op **voor waarde selecteren**.
5. Klik in de Blade **signaal logica configureren** op **Cloud lagen terughalen grootte** onder signaal naam.  
6. Selecteer de volgende dimensie configuratie: 
     - Dimensie naam: **Server naam**  
     - And**=** 
     - Dimensie waarden: **alle huidige en toekomstige waarden**  
7. Navigeer naar **waarschuwings logica** en voer de volgende handelingen uit: 
     - Drempel ingesteld op **statisch** 
     - Operator: **groter dan** 
     - Aggregatie type: **totaal**  
     - Drempel waarde (in bytes): **67108864000** 
     - Geëvalueerd op basis van: aggregatie granulatie = **24 uur** | Frequentie van evaluatie = **elk uur** 
    - Klik op **gereed.** 
8. Klik op **actie groep selecteren** om een actie groep (E-mail, SMS, enzovoort) toe te voegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.
9. Vul de details van de **waarschuwing** in, zoals de naam, **Beschrijving** en **Ernst**van de **waarschuwings regel**.
10. Klik op **Waarschuwingsregel maken**. 

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
- [Firewall-en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
- [Azure File Sync implementeren](storage-sync-files-deployment-guide.md)
- [Problemen oplossen met Azure File Sync](storage-sync-files-troubleshoot.md)
- [Veelgestelde vragen over Azure Files](storage-files-faq.md)
