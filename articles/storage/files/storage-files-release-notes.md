---
title: Release opmerkingen voor de Azure File Sync-agent | Microsoft Docs
description: Release opmerkingen voor de Azure File Sync-agent.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 1b11c6beda5b261d1edc77dcbb365d8d8df8bd09
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176710"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Release opmerkingen voor de Azure File Sync-agent
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Uw installaties van Windows Server worden getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als u waar ook ter wereld nodig hebt.

Dit artikel bevat de releaseopmerkingen voor de ondersteunde versies van de Azure File Sync-agent.

## <a name="supported-versions"></a>Ondersteunde versies
De volgende versies worden ondersteund voor de Azure File Sync-agent:

| Mijlpalen | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| V8 release- [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 oktober 2019 | Ondersteund |
| Update pakket van juli 2019- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 juli 2019 | Ondersteund |
| Update pakket van juli 2019- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 juli 2019 | Ondersteund |
| V7-release- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 juni 2019 | Ondersteund |
| Update pakket van juni 2019- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 juni 2019 | Ondersteund |
| Update pakket van juni 2019- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 juni 2019 | Ondersteund |
| Update pakket van mei 2019- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 mei 2019 | Ondersteund |
| V6-release- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 april 2019 | Ondersteund |
| Update pakket van april 2019- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 april 2019 | Ondersteund |
| Update pakket van maart 2019- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 maart 2019 | Ondersteund |
| V5-release- [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 februari 2019 | Ondersteund |
| Update pakket van januari 2019- [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 januari 2019 | Ondersteund: de versie van de agent verloopt op 5 november 2019 |
| Update pakket van december 2018- [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 december 2018 | Ondersteund: de versie van de agent verloopt op 5 november 2019 |
| Update pakket van december 2018 | 4.1.0.0 | 4 december 2018 | Ondersteund: de versie van de agent verloopt op 5 november 2019 |
| V4-release | 4.0.1.0 | 13 november 2018 | Ondersteund: de versie van de agent verloopt op 5 november 2019 |
| V3-release | 3.1.0.0 - 3.4.0.0 | Niet ondersteund | Niet ondersteund: agent versies verlopen op 19 augustus 2019 |
| Pre-GA-agents | 1.1.0.0-3.0.13.0 | N/A | Niet ondersteund: agent versies verlopen op 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>8\.0.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 8.0.0.0 van de Azure File Sync-agent (uitgebracht op 8 oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Prestatie verbeteringen herstellen
    - Snellere herstel tijden voor herstel die zijn uitgevoerd via Azure Backup. Teruggezette bestanden worden sneller naar Azure File Sync-servers gesynchroniseerd. 
- Verbeterde portal-ervaring voor Cloud lagen  
    - Als u gelaagde bestanden hebt die niet kunnen worden ingetrokken, kunt u nu de intrek fouten weer geven in de eigenschappen van het server eindpunt. De eindpunt status van de server geeft nu ook een fout-en beperkings procedure weer als het stuur programma voor het filter voor Cloud lagen niet is geladen op de server.
- Eenvoudigere agent installatie
    - De Az\AzureRM Power shell-module is niet meer vereist voor het registreren van de server die de installatie eenvoudiger en snel maakt.
- Diverse verbeteringen in prestaties en betrouw baarheid

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de discretionaire ACL (Access Control List) van een security descriptor als dit groter is dan 2 kB. (Dit probleem geldt alleen wanneer er meer dan ongeveer 40 vermeldingen voor toegangsbeheer (ACE's) bestaan voor één item.)
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden niet meer toegankelijk als de bestanden niet worden ingetrokken voordat het server eindpunt wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het server eindpunt opnieuw. Als er 30 dagen zijn verstreken sinds het server eindpunt is verwijderd of als het Cloud-eind punt is verwijderd, kunnen gelaagde bestanden die niet zijn ingetrokken, onbruikbaar worden. Zie [gelaagde bestanden niet toegankelijk op de server na het verwijderen van een server eindpunt](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)voor meer informatie.
- Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell [-cmdlet invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.

## <a name="agent-version-7200"></a>7\.2.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 7.2.0.0 van de Azure File Sync agent die is uitgebracht op 24 juli 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 7.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- De opslag synchronisatie agent (FileSyncSvc) loopt vast als de proxy configuratie null is.
- Server-eind punt start BCDR (fout 0x80c80257-ECS_E_BCDR_IN_PROGRESS) als meerdere eind punten op de server dezelfde naam hebben.
- Verbeteringen in de betrouw baarheid van Cloud lagen.

## <a name="agent-version-7100"></a>7\.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 7.1.0.0 van de Azure File Sync agent die is uitgebracht op 12 juli 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 7.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Het openen of door bladeren van een server eindpunt locatie via SMB verloopt traag op Windows Server 2012 R2. 
- Het CPU-gebruik is verhoogd na de installatie van de Azure File Sync V6-agent.
- Verbeteringen in de telemetrie in Cloud lagen.
- Diverse verbeteringen van de betrouw baarheid voor Cloud lagen en synchronisatie.

## <a name="agent-version-7000"></a>7\.0.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 7.0.0.0 van de Azure File Sync-agent (uitgebracht op 19 juni 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor grotere grootte van bestands shares
    - Met de preview-versie van grotere Azure-bestands shares verhogen we ook onze ondersteunings limieten voor bestands synchronisatie. In deze eerste stap ondersteunt Azure File Sync nu Maxi maal 25 TB en 50.000.000 bestanden in één synchronisatie naam ruimte. Als u een voor beeld van een grote bestands share wilt gebruiken, vult u dit formulier https://aka.ms/azurefilesatscalesurvey in. 
- Ondersteuning voor Firewall en virtuele netwerk instelling voor opslag accounts
    - Azure File Sync ondersteunt nu de instelling Firewall en virtueel netwerk voor opslag accounts. Zie [instellingen voor Firewall en virtueel netwerk configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)om uw implementatie te configureren voor gebruik met de instelling Firewall en virtueel netwerk.
- Power shell-cmdlet voor het direct synchroniseren van bestanden die zijn gewijzigd in de Azure-bestands share
    - Om bestanden direct te synchroniseren die in de Azure-bestands share zijn gewijzigd, kan de Power shell-cmdlet invoke-AzStorageSyncChangeDetection worden gebruikt om de detectie van wijzigingen in de Azure-bestands share hand matig te initiëren. Deze cmdlet is bedoeld voor scenario's waarbij een type geautomatiseerd proces wijzigingen aanbrengt in de Azure-bestands share of de wijzigingen worden uitgevoerd door een beheerder (zoals het verplaatsen van bestanden en mappen naar de share). Voor wijzigingen van de eind gebruiker is het aanbeveling om de Azure File Sync-agent te installeren in een IaaS-VM en eind gebruikers hebben toegang tot de bestands share via de IaaS-VM. Op deze manier worden alle wijzigingen snel gesynchroniseerd met andere agents zonder dat u de cmdlet invoke-AzStorageSyncChangeDetection hoeft te gebruiken. Zie de documentatie voor [invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) voor meer informatie.
- Verbeterde portal ervaring als u bestanden tegen komt die niet worden gesynchroniseerd
    - Als u bestanden hebt die niet kunnen worden gesynchroniseerd, worden er nu onderscheid gemaakt tussen tijdelijke en permanente fouten in de portal. Tijdelijke fouten worden meestal vanzelf opgelost zonder dat de beheerder actie hoeft te ondernemen. Een bestand dat momenteel in gebruik is, wordt pas gesynchroniseerd nadat de bestands ingang is gesloten. Voor permanente fouten wordt nu het aantal bestanden weer gegeven dat wordt beïnvloed door elke fout. Het aantal permanente fouten wordt ook weer gegeven in de kolom bestanden niet synchroniseren van alle server eindpunten in een synchronisatie groep.
- Verbeterd Azure Backup herstel op bestands niveau
    - Afzonderlijke bestanden die zijn hersteld met behulp van Azure Backup, worden nu sneller gedetecteerd en gesynchroniseerd met het server eindpunt.
- Verbeterde betrouw baarheid van Cloud lagen intrekken van cmdlet 
    - Met de cmdlet invoke-StorageSyncFileRecall kunnen klanten nu het aantal nieuwe pogingen per bestand opgeven en per bestand vertraging voor opnieuw proberen, vergelijkbaar met Robocopy. Voorheen zouden met deze cmdlet alle gelaagde bestanden onder een bepaald pad in wille keurige volg orde worden teruggehaald. Met de para meter New-Order haalt deze cmdlet de nieuwste gegevens eerst op en voldoet het beleid voor Cloud lagen (stoppen met terugtrekken als aan het datum beleid wordt voldaan of aan de beschik bare ruimte op het volume wordt voldaan).
- Ondersteuning voor alleen TLS 1,2 (TLS 1,0 en 1,1 is uitgeschakeld)
    - Azure File Sync ondersteunt nu alleen TLS 1,2 op servers met TLS 1,0 en 1,1 uitgeschakeld. Vóór deze verbetering zou de registratie van de server mislukken als TLS 1,0 en 1,1 op de server zijn uitgeschakeld.
- Diverse verbeteringen in prestaties en betrouw baarheid voor synchronisatie en Cloud lagen
    - Er zijn verschillende verbeteringen in de betrouw baarheid en prestaties in deze release. Sommige hiervan zijn bedoeld om het maken van Cloud lagen efficiënter en Azure File Sync als een volledig werk in die situaties, wanneer u een planning voor bandbreedte regeling hebt ingesteld.

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de discretionaire ACL (Access Control List) van een security descriptor als dit groter is dan 2 kB. (Dit probleem geldt alleen wanneer er meer dan ongeveer 40 vermeldingen voor toegangsbeheer (ACE's) bestaan voor één item.)
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden niet meer toegankelijk als de bestanden niet worden ingetrokken voordat het server eindpunt wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het server eindpunt opnieuw. Als er 30 dagen zijn verstreken sinds het server eindpunt is verwijderd of als het Cloud-eind punt is verwijderd, kunnen gelaagde bestanden die niet zijn ingetrokken, onbruikbaar worden.
- Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.

## <a name="agent-version-6300"></a>6\.3.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 6.3.0.0 van de Azure File Sync agent die is uitgebracht op 27 juni 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Het openen of door bladeren van een server eindpunt locatie via SMB is traag op Windows Server 2012 R2 
- Verbeterd CPU-gebruik na de installatie van de Azure File Sync V6-agent
- Verbeteringen in telemetrie in Cloud lagen

## <a name="agent-version-6200"></a>6\.2.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 6.2.0.0 van de Azure File Sync agent die is uitgebracht op 13 juni 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Na het maken van een server-eind punt is er een hoog CPU-gebruik mogelijk wanneer de bestanden van de achtergrond worden gedownload naar de server
- Synchronisatie en Cloud lagen kunnen mislukken met fout ECS_E_SERVER_CREDENTIAL_NEEDED vanwege een verval datum van het token
- Het intrekken van een bestand kan mislukken als de URL voor het downloaden van het bestand gereserveerde tekens bevat 

## <a name="agent-version-6100"></a>6\.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 6.1.0.0 van de Azure File Sync agent die is uitgebracht op 6 mei 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Windows-beheer centrum kan de agent versie en de configuratie van het server eindpunt niet weer geven op servers waarop Azure File Sync agent versie 6,0 is geïnstalleerd.

## <a name="agent-version-6000"></a>6\.0.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 6.0.0.0 van de Azure File Sync-agent (uitgebracht op 22 april 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor automatische updates van de agent
  - We hebben uw feedback gehoord en een functie voor automatisch bijwerken toegevoegd aan de Azure File Sync Server-Agent. Zie [Azure file sync beleid voor agent updates](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)voor meer informatie.
- Ondersteuning voor Azure file share-Acl's
  - Azure File Sync heeft altijd synchronisatie Acl's ondersteund tussen server eindpunten, maar de Acl's zijn niet gesynchroniseerd met het Cloud eindpunt (Azure-bestands share). Deze release voegt ondersteuning toe voor het synchroniseren van Acl's tussen server-en Cloud eindpunten.
- Parallelle upload-en download synchronisatie sessies voor een server eindpunt 
  - Server eindpunten bieden nu ondersteuning voor het uploaden en downloaden van bestanden op hetzelfde moment. Er wordt niet meer gewacht tot een down load is voltooid zodat bestanden kunnen worden geüpload naar de Azure-bestands share. 
- Nieuwe cmdlets voor Cloud lagen voor het ophalen van de status van volumes en lagen
  - Er kunnen nu twee nieuwe server-Local Power shell-cmdlets worden gebruikt om Cloud lagen en informatie over het terughalen van bestanden te verkrijgen. Ze maken logboek gegevens van twee gebeurtenis kanalen op de server beschikbaar:
    - Met Get-StorageSyncFileTieringResult worden alle bestanden en hun paden weer gegeven die niet zijn gelaagd en rapporten over de reden waarom.
    - Get-StorageSyncFileRecallResult rapporteert alle gebeurtenissen voor het terughalen van bestanden. Er wordt een lijst weer gegeven met elk bestand dat wordt ingetrokken en het bijbehorende pad, of het is geslaagd of mislukt.
  - Standaard kunnen beide gebeurtenis kanalen Maxi maal 1 MB bevatten. u kunt de hoeveelheid bestanden die wordt gerapporteerd verg Roten door de grootte van het gebeurtenis kanaal te verg Roten.
- Ondersteuning voor FIPS-modus
  - Azure File Sync ondersteunt nu de FIPS-modus op servers waarop de Azure File Sync-agent is geïnstalleerd.
    - Voordat u de FIPS-modus op uw server inschakelt, moet u de module Azure File Sync agent en [Package Management](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) op de server installeren. Als FIPS al is ingeschakeld op de server, moet u de [Package Management-module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) [hand matig downloaden](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) naar uw server.
- Diverse verbeteringen van de betrouw baarheid voor Cloud lagen en synchronisatie

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund voor de nano Server-implementatie optie.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de discretionaire ACL (Access Control List) van een security descriptor als dit groter is dan 2 kB. (Dit probleem geldt alleen wanneer er meer dan ongeveer 40 vermeldingen voor toegangsbeheer (ACE's) bestaan voor één item.)
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden niet meer toegankelijk als de bestanden niet worden ingetrokken voordat het server eindpunt wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het server eindpunt opnieuw. Als er 30 dagen zijn verstreken sinds het server eindpunt is verwijderd of als het Cloud-eind punt is verwijderd, kunnen gelaagde bestanden die niet zijn ingetrokken, onbruikbaar worden.
- Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
- Wanneer u bestandseigenschappen bekijkt vanuit een SMB-client, lijkt het misschien of het kenmerk 'offline' niet goed is ingesteld als gevolg van SMB-caching van bestandsmetagegevens.

## <a name="agent-version-5200"></a>5\.2.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 5.2.0.0 van de Azure File Sync agent uitgebracht: 4 april 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 5.0.2.0.

Lijst met problemen die in deze release zijn opgelost:  
- Verbeteringen van de betrouw baarheid voor offline gegevens overdracht en de functies voor het hervatten van gegevens overdracht
- Verbeteringen voor telemetrie synchroniseren

## <a name="agent-version-5100"></a>5\.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 5.1.0.0 van de Azure File Sync agent die is uitgebracht op 7 maart 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 5.0.2.0.

Lijst met problemen die in deze release zijn opgelost:  
- Bestanden kunnen niet worden gesynchroniseerd met de fout 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) als het wijzigen van de inventarisatie op de server mislukt
- Als een synchronisatie sessie of bestand een fout 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE) ontvangt, wordt de bewerking nu opnieuw geprobeerd
- Bestanden kunnen niet worden gesynchroniseerd met de fout 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Hoog geheugen gebruik kan optreden bij het terughalen van bestanden
- Verbeteringen in telemetrie in Cloud lagen 

## <a name="agent-version-5020"></a>5\.0.2.0 van agent versie
De volgende release opmerkingen zijn voor versie 5.0.2.0 van de Azure File Sync-agent (uitgebracht op 12 februari 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor Azure Government Cloud
  - Er is preview-ondersteuning toegevoegd voor de Azure Government Cloud. Hiervoor is een wit abonnement en een speciale down load van de agent van micro soft vereist. Als u toegang wilt krijgen tot de preview, kunt u ons rechtstreeks een e-mail sturen op [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Ondersteuning voor Gegevensontdubbeling
    - Gegevensontdubbeling wordt nu volledig ondersteund met Cloud lagen die zijn ingeschakeld op Windows Server 2016 en Windows Server 2019. Als u ontdubbeling inschakelt op een volume waarvoor Cloud lagen zijn ingeschakeld, kunt u meer bestanden on-premises opslaan zonder dat u meer opslag ruimte hoeft in te richten.
- Ondersteuning voor offline gegevens overdracht (bijvoorbeeld via Data Box)
    - Migreer eenvoudig grote hoeveel heden gegevens naar Azure File Sync via een wille keurige manier. U kunt kiezen Azure Data Box, AzCopy en zelfs migratie services van derden. U hoeft geen enorme hoeveel heden band breedte te gebruiken om uw gegevens in azure op te halen, in het geval van Data Box: u kunt er gewoon e-mailen. Zie [Offline gegevensoverdracht docs](https://aka.ms/AFS/OfflineDataTransfer)(Engelstalig) voor meer informatie.
- Verbeterde synchronisatie prestaties
    - Klanten met meerdere server eindpunten op hetzelfde volume hebben mogelijk trage synchronisatie prestaties vóór deze release. Azure File Sync maakt een tijdelijke VSS-moment opname eenmaal per dag op de server om bestanden met geopende ingangen te synchroniseren. Synchronisatie ondersteunt nu meerdere server eindpunten die worden gesynchroniseerd op een volume wanneer een VSS-synchronisatie sessie actief is. Er wordt niet meer gewacht tot een VSS-synchronisatie sessie is voltooid zodat de synchronisatie kan worden voortgezet op andere server eindpunten op het volume.
- Verbeterde bewaking in de portal
    - Er zijn grafieken toegevoegd aan de opslag synchronisatie service-portal om het volgende te bekijken:
        - Aantal gesynchroniseerde bestanden
        - Grootte van overgedragen gegevens
        - Aantal bestanden dat niet wordt gesynchroniseerd
        - Grootte van gegevens die zijn ingetrokken
        - Status van Server connectiviteit
    - Zie [Azure file sync bewaken](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)voor meer informatie.
- Verbeterde schaal baarheid en betrouw baarheid
    - Het maximum aantal bestandssysteem objecten (directory's en bestanden) in een directory is verhoogd tot 1.000.000. Vorige limiet is 200.000.
    - Er wordt geprobeerd om de gegevens overdracht te hervatten in plaats van opnieuw te verzenden wanneer een overdracht wordt onderbroken voor grote bestanden 

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund in Windows Server Core-of nano Server-implementatie opties.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.
- De FIPS-modus wordt niet ondersteund en moet worden uitgeschakeld. 

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de discretionaire ACL (Access Control List) van een security descriptor als dit groter is dan 2 kB. (Dit probleem geldt alleen wanneer er meer dan ongeveer 40 vermeldingen voor toegangsbeheer (ACE's) bestaan voor één item.)
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden niet meer toegankelijk als de bestanden niet worden ingetrokken voordat het server eindpunt wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het server eindpunt opnieuw. Als er 30 dagen zijn verstreken sinds het server eindpunt is verwijderd of als het Cloud-eind punt is verwijderd, kunnen gelaagde bestanden die niet zijn ingetrokken, onbruikbaar worden.
- Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
- Wanneer u bestandseigenschappen bekijkt vanuit een SMB-client, lijkt het misschien of het kenmerk 'offline' niet goed is ingesteld als gevolg van SMB-caching van bestandsmetagegevens.

## <a name="agent-version-4300"></a>4\.3.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 4.3.0.0 van de Azure File Sync agent die is uitgebracht op 14 januari 2019. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 4.0.1.0.

Lijst met problemen die in deze release zijn opgelost:  
- Bestanden worden niet getierd na het upgraden van de Azure File Sync agent naar versie 4. x.
- AfsUpdater. exe wordt nu ondersteund op Windows Server 2019.
- Diverse verbeteringen van de betrouw baarheid voor synchronisatie. 

## <a name="agent-version-4200"></a>4\.2.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 4.2.0.0 van de Azure File Sync agent die is uitgebracht op 10 december 2018. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 4.0.1.0.

Lijst met problemen die in deze release zijn opgelost:  
- Een Stop fout 0x3B of Stop fout 0x1E kan optreden wanneer een VSS-moment opname wordt gemaakt.  
- Er is sprake van een geheugenlek wanneer Cloud lagen zijn ingeschakeld  

## <a name="agent-version-4100"></a>4\.1.0.0 van agent versie
De volgende release opmerkingen zijn voor versie 4.1.0.0 van de Azure File Sync agent die is uitgebracht op 4 december 2018. Deze opmerkingen zijn opgenomen in aanvulling op de release opmerkingen van versie 4.0.1.0.

Lijst met problemen die in deze release zijn opgelost:  
- Het kan zijn dat de server niet meer reageert vanwege een geheugenlek in de Cloud.  
- De agent installatie mislukt met de volgende fout: Fout 1921. Kan de opslag synchronisatie agent (FileSyncSvc) van de service niet stoppen.  Controleer of u voldoende rechten hebt om systeem services te stoppen.  
- De FileSyncSvc-service (Storage Sync agent) kan vastlopen wanneer het geheugen gebruik hoog is.  
- Diverse verbeteringen van de betrouw baarheid voor Cloud lagen en synchronisatie.

## <a name="agent-version-4010"></a>4\.0.1.0 van agent versie
De volgende release opmerkingen zijn voor versie 4.0.1.0 van de Azure File Sync-agent (uitgebracht op 13 november 2018).

### <a name="evaluation-tool"></a>Evaluatie programma
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het Azure File Sync-evaluatie programma. Dit hulp programma is een Azure PowerShell-cmdlet waarmee wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. Zie het gedeelte [evaluatie hulpprogramma's](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) in de plannings handleiding voor instructies voor de installatie en het gebruik. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server, Zie [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

- Het installatie pakket van de agent moet worden geïnstalleerd met verhoogde machtigingen (Administrator).
- De agent wordt niet ondersteund in Windows Server Core-of nano Server-implementatie opties.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent vereist ten minste 2 GiB geheugen. Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
- De FileSyncSvc-service (Storage Sync agent) biedt geen ondersteuning voor Server eindpunten die zich bevinden op een volume waarop de SVI-map (System Volume Information) is gecomprimeerd. Deze configuratie resulteert in onverwachte resultaten.
- De FIPS-modus wordt niet ondersteund en moet worden uitgeschakeld. 
- Een Stop fout 0x3B of Stop fout 0x1E kan optreden wanneer een VSS-moment opname wordt gemaakt.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [problemen met Azure file sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- Bestands server bron beheer (FSRM)-bestands controles kunnen eindeloze synchronisatie fouten veroorzaken wanneer bestanden worden geblokkeerd vanwege de bestands controle.
- Het uitvoeren van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De Azure File Sync-agent moet worden geïnstalleerd na de implementatie van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.
- Gegevensontdubbeling en cloudopslaglagen worden niet ondersteund op hetzelfde volume.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Raadpleeg de [gids voor probleem oplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een punt.
- Paden langer dan 2.048 tekens.
- Het gedeelte met de discretionaire ACL (Access Control List) van een security descriptor als dit groter is dan 2 kB. (Dit probleem geldt alleen wanneer er meer dan ongeveer 40 vermeldingen voor toegangsbeheer (ACE's) bestaan voor één item.)
- Het gedeelte met de SACL (System Access Control List) van een security descriptor die wordt gebruikt voor controle.
- Uitgebreide kenmerken.
- Alternatieve gegevensstromen.
- Reparse-punten.
- Vaste koppelingen.
- Compressie (indien ingesteld op een serverbestand) blijft niet behouden wanneer wijzigingen vanuit andere eindpunten naar dat bestand worden gesynchroniseerd.
- Elk bestand dat is gecodeerd met EFS (of een andere versleuteling in de gebruikersmodus) dat voorkomt dat de service de gegevens leest.

    > [!Note]  
    > Gegevens die onderweg zijn tussen eindpunten worden altijd versleuteld door Azure File Sync. Inactieve gegevens (data-at-rest) worden altijd versleuteld in Azure.
 
### <a name="server-endpoint"></a>Server eindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden niet meer toegankelijk als de bestanden niet worden ingetrokken voordat het server eindpunt wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het server eindpunt opnieuw. Als er 30 dagen zijn verstreken sinds het server eindpunt is verwijderd of als het Cloud-eind punt is verwijderd, kunnen gelaagde bestanden die niet zijn ingetrokken, onbruikbaar worden.
- Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturings systeem of toepassings wissel bestand niet op in de locatie van een server eindpunt.
- De server naam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Cloud-eind punt
- Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt één keer per 24 uur geïnitieerd voor een Cloud eindpunt. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie.
- De opslag synchronisatie service en/of het opslag account kunnen worden verplaatst naar een andere resource groep of een ander abonnement binnen de bestaande Azure AD-Tenant. Als het opslag account is verplaatst, moet u de Hybrid File Sync-Service toegang tot het opslag account geven (Zie [controleren of Azure file sync toegang heeft tot het opslag account](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-Tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- De op datum gebaseerde beleids instelling voor het maken van een Cloud wordt gebruikt om bestanden op te geven die in de cache moeten worden opgeslagen als ze worden geopend in een opgegeven aantal dagen. Zie [overzicht van Cloud lagen](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering)voor meer informatie.
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden met behulp van Robocopy kopieert, gebruikt u de optie/MIR om de tijds tempels van het bestand te behouden. Dit zorgt ervoor dat oudere bestanden eerder zijn gelaagd dan recent geopende bestanden.
- Wanneer u bestandseigenschappen bekijkt vanuit een SMB-client, lijkt het misschien of het kenmerk 'offline' niet goed is ingesteld als gevolg van SMB-caching van bestandsmetagegevens.
