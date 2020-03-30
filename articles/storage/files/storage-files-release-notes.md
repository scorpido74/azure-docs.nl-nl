---
title: Releasenotes voor de Azure File Sync-agent | Microsoft Documenten
description: Opmerkingen vrijgeven voor de Azure File Sync-agent.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5b247e8f4a611ace227f5adcef7e0e27de341c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454632"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Releaseopmerkingen voor de Azure File Sync-agent
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Uw installaties van Windows Server worden getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als u waar ook ter wereld nodig hebt.

Dit artikel bevat de releaseopmerkingen voor de ondersteunde versies van de Azure File Sync-agent.

## <a name="supported-versions"></a>Ondersteunde versies
De volgende versies worden ondersteund voor de Azure File Sync-agent:

| Mijlpaal | Versienummer agent | Releasedatum | Status |
|----|----------------------|--------------|------------------|
| Update-rollup van december 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 december 2019 | Ondersteund |
| V9 Release - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 december 2019 | Ondersteund |
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 oktober 2019 | Ondersteund |
| Update-rollup juli 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 juli 2019 | Ondersteund |
| Update-rollup juli 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 juli 2019 | Ondersteund |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 juni 2019 | Ondersteund |
| Update-rollup juni 2019 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 juni 2019 | Ondersteund - Agent-versie verloopt op 21 april 2020 |
| Update-rollup juni 2019 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 juni 2019 | Ondersteund - Agent-versie verloopt op 21 april 2020 |
| Update-rollup mei 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 mei 2019 | Ondersteund - Agent-versie verloopt op 21 april 2020 |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 april 2019 | Ondersteund - Agent-versie verloopt op 21 april 2020 |
| Update-rollup april 2019 - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 april 2019 | Ondersteund - Agent-versie verloopt op 18 maart 2020 |
| Update-rollup van maart 2019 - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 maart 2019 | Ondersteund - Agent-versie verloopt op 18 maart 2020 |
| V5 Release - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 februari 2019 | Ondersteund - Agent-versie verloopt op 18 maart 2020 |
| V4-versie | 4.0.1.0 - 4.3.0.0 | N.v.t. | Niet ondersteund - Agent-versies verlopen op 6 november 2019 |
| V3-versie | 3.1.0.0 - 3.4.0.0 | N.v.t. | Niet ondersteund - Agent-versies verlopen op 19 augustus 2019 |
| Pre-GA-agenten | 1.1.0.0 - 3.0.13.0 | N.v.t. | Niet ondersteund - Agent-versies zijn verlopen op 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>Agent versie 9.1.0.0
De volgende releasenotes zijn voor versie 9.1.0.0 van de Azure File Sync-agent die op 12 december 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 9.0.0.0.

Probleem opgelost in deze release:  
- Synchronisatie mislukt met een van de volgende fouten na een upgrade naar Azure File Sync-agent versie 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent versie 9.0.0.0
De volgende releasenotes zijn voor versie 9.0.0.0 van de Azure File Sync-agent (uitgebracht op 2 december 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor selfserviceherstel
    - Gebruikers kunnen hun bestanden nu herstellen met behulp van de vorige versiefunctie. Voorafgaand aan de v9-release werd de vorige versiefunctie niet ondersteund op volumes waarop cloudtiering was ingeschakeld. Deze functie moet voor elk volume afzonderlijk zijn ingeschakeld, waarop een eindpunt met cloudtiering is ingeschakeld. Zie voor meer informatie  
[Selfservice herstellen via vorige versies en VSS (Volume Shadow Copy Service)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Ondersteuning voor grotere bestandsgroottes 
    - Azure File Sync ondersteunt nu maximaal 64TiB en 100 miljoen bestanden in één enkele, synchronisatie van naamruimte.  
 
- Ondersteuning voor gegevensontdubbeling op Server 2019 
    - Data deduplicatie wordt nu ondersteund met cloud tiering ingeschakeld op Windows Server 2019. Om gegevensdeduplicatie op volumes met cloudlagen te ondersteunen, moet Windows-update [KB4520062](https://support.microsoft.com/help/4520062) worden geïnstalleerd. 
 
- Verbeterde minimale bestandsgrootte voor een bestand naar laag 
    - De minimale bestandsgrootte voor een bestand tot laag is nu gebaseerd op de clustergrootte van het bestandssysteem (het dubbele van de clustergrootte van het bestandssysteem). Standaard is de clustergrootte van het NTFS-bestandssysteem 4KB, de resulterende minimale bestandsgrootte voor een bestand tot laag is 8KB. 
 
- Cmdlet voor netwerkconnectiviteitstest 
    - Als onderdeel van de Azure File Sync-configuratie moeten meerdere serviceeindpunten worden gecontacteerd. Ze hebben elk hun eigen DNS-naam die toegankelijk moet zijn voor de server. Deze URL's zijn ook specifiek voor het gebied waarop een server is geregistreerd. Zodra een server is geregistreerd, kan de connectivity test cmdlet (PowerShell and Server Registration Utility) worden gebruikt om communicatie te testen met alle URL's die specifiek zijn voor deze server. Deze cmdlet kan helpen problemen op te lossen wanneer onvolledige communicatie voorkomt dat de server volledig werkt met Azure File Sync en kan worden gebruikt om proxy- en firewallconfiguraties te verfijnen.  
 
        Voer de volgende PowerShell-opdrachten uit om de netwerkverbindingstest uit te voeren: 
 
        Importmodule "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Servereindpuntverbetering verwijderen wanneer cloudtiering is ingeschakeld 
    - Net als voorheen leidt het verwijderen van een servereindpunt niet tot het verwijderen van bestanden in de Azure-bestandsshare. Het gedrag voor hearse punten op de lokale server is echter gewijzigd. Hearse punten (verwijzingen naar bestanden die niet lokaal op de server staan) worden nu verwijderd bij het verwijderen van een servereindpunt. De bestanden met volledige cache blijven op de server staan. Deze verbetering is aangebracht om zwevende bestanden met [een verweesde laag](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) te voorkomen bij het verwijderen van een servereindpunt. Als het servereindpunt opnieuw wordt gemaakt, worden de hearse punten voor de gelaagde bestanden opnieuw gemaakt op de server.  
 
- Verbeteringen in prestaties en betrouwbaarheid 
    - Minder terugroepfouten. De terugroepgrootte wordt nu automatisch aangepast op basis van de netwerkbandbreedte. 
    - Verbeterde downloadprestaties bij het toevoegen van een nieuwe server aan een synchronisatiegroep. 
    - Minder bestanden die niet worden gesynchroniseerd vanwege problemen met beperkingen. 
    - Bestanden kunnen niet tieren of worden in bepaalde scenario's onverwacht teruggeroepen als het eindpuntpad van de server een punt voor volumemount is.
    
### <a name="evaluation-tool"></a>Evaluatietool
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het evaluatiehulpprogramma azure bestandssynchronisatie. Deze tool is een Azure PowerShell-cmdlet die controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie [Sectie Evaluatietool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) voor installatie- en gebruiksinstructies in de planningshandleiding. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server.

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde (beheerders) machtigingen.
- De agent wordt niet ondersteund op de implementatieoptie Nano Server.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent heeft minstens 2 GiB geheugen nodig. Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.
- De FileSync Agent-service (Storage Sync Agent) biedt geen ondersteuning voor servereindpunten op een volume waarop de svi-map (System Volume Information) is gecomprimeerd. Deze configuratie zal leiden tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- FSRM-bestandsschermen (File Server Resource Manager) kunnen eindeloze synchronisatiefouten veroorzaken wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. De Azure File Sync-agent moet worden geïnstalleerd nadat de serverafbeelding is geïmplementeerd en de sysprep-mini-installatie is voltooid.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Handleiding voor probleemoplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een periode.
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
 
### <a name="server-endpoint"></a>Servereindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat het eindpunt van de server wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het eindpunt van de server opnieuw. Als er 30 dagen zijn verstreken sinds het eindpunt van de server is verwijderd of als het eindpunt van de cloud is verwijderd, zijn gelaagde bestanden die niet zijn teruggeroepen, onbruikbaar. Zie [Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)voor meer informatie.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturingssysteem- of toepassingspagingbestand niet op binnen een eindpuntlocatie van de server.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Eindpunt in de cloud
- Azure File Sync ondersteunt het rechtstreeks aanbrengen van wijzigingen in het Azure-bestandsaandeel. Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel moeten echter eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt één keer per 24 uur gestart voor een cloudeindpunt. Als u bestanden die in de Azure-bestandsshare worden gewijzigd, onmiddellijk wilt synchroniseren, kan de PowerDlet [Invoke-AzStorageChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestandsshare te starten. Bovendien worden wijzigingen in een Azure-bestandsshare via het REST-protocol de laatste gewijzigde tijd van het SMB niet bijgewerkt en worden ze niet gezien als een wijziging door synchronisatie.
- De opslagsynchronisatieservice en/of opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met robocopy, gebruikt u de optie /MIR om bestandstijdstempels te bewaren. Dit zorgt ervoor dat oudere bestanden sneller worden gegelaagdeerd dan recent geopende bestanden.
- Bestanden kunnen niet laag als de pagefile.sys zich bevindt op een volume dat cloud tiering ingeschakeld. De pagefile.sys moet zich bevinden op een volume waarop cloudtiering is uitgeschakeld.

## <a name="agent-version-8000"></a>Agent versie 8.0.0.0
De volgende releasenotes zijn voor versie 8.0.0.0 van de Azure File Sync-agent (uitgebracht op 8 oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Prestatieverbeteringen herstellen
    - Snellere hersteltijden voor herstel via Azure Backup. Herstelde bestanden worden veel sneller gesynchroniseerd met Azure File Sync-servers. 
- Verbeterde ervaring in cloudtiering-portal  
    - Als u gelaagde bestanden hebt die zich niet kunnen herinneren, u nu de terugroepfouten in de eigenschappen van het eindpunt van de server weergeven. Ook wordt in de status van het servereindpunt nu een fout- en mitigatiestappen weergegeven als het filterstuurprogramma voor cloudlagen niet op de server wordt geladen.
- Eenvoudigere installatie van agent
    - De Az\AzureRM PowerShell-module is niet langer nodig om de server te registreren, waardoor de installatie eenvoudiger en sneller wordt.
- Diverse verbeteringen in prestaties en betrouwbaarheid

### <a name="evaluation-tool"></a>Evaluatietool
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het evaluatiehulpprogramma azure bestandssynchronisatie. Deze tool is een Azure PowerShell-cmdlet die controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie [Sectie Evaluatietool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) voor installatie- en gebruiksinstructies in de planningshandleiding. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server.

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde (beheerders) machtigingen.
- De agent wordt niet ondersteund op de implementatieoptie Nano Server.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent heeft minstens 2 GiB geheugen nodig. Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.
- De FileSync Agent-service (Storage Sync Agent) biedt geen ondersteuning voor servereindpunten op een volume waarop de svi-map (System Volume Information) is gecomprimeerd. Deze configuratie zal leiden tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- FSRM-bestandsschermen (File Server Resource Manager) kunnen eindeloze synchronisatiefouten veroorzaken wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. De Azure File Sync-agent moet worden geïnstalleerd nadat de serverafbeelding is geïmplementeerd en de sysprep-mini-installatie is voltooid.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Handleiding voor probleemoplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een periode.
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
 
### <a name="server-endpoint"></a>Servereindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat het eindpunt van de server wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het eindpunt van de server opnieuw. Als er 30 dagen zijn verstreken sinds het eindpunt van de server is verwijderd of als het eindpunt van de cloud is verwijderd, zijn gelaagde bestanden die niet zijn teruggeroepen, onbruikbaar. Zie [Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)voor meer informatie.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturingssysteem- of toepassingspagingbestand niet op binnen een eindpuntlocatie van de server.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Eindpunt in de cloud
- Azure File Sync ondersteunt het rechtstreeks aanbrengen van wijzigingen in het Azure-bestandsaandeel. Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel moeten echter eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt één keer per 24 uur gestart voor een cloudeindpunt. Als u bestanden die in de Azure-bestandsshare worden gewijzigd, onmiddellijk wilt synchroniseren, kan de PowerDlet [Invoke-AzStorageChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestandsshare te starten. Bovendien worden wijzigingen in een Azure-bestandsshare via het REST-protocol de laatste gewijzigde tijd van het SMB niet bijgewerkt en worden ze niet gezien als een wijziging door synchronisatie.
- De opslagsynchronisatieservice en/of opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met robocopy, gebruikt u de optie /MIR om bestandstijdstempels te bewaren. Dit zorgt ervoor dat oudere bestanden sneller worden gegelaagdeerd dan recent geopende bestanden.

## <a name="agent-version-7200"></a>Agent versie 7.2.0.0
De volgende releasenotes zijn voor versie 7.2.0.0 van de Azure File Sync-agent die op 24 juli 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 7.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Storage Sync Agent (FileSyncSvc) loopt vast als de proxyconfiguratie nietig is.
- Servereindpunt start BCDR (fout 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) als meerdere eindpunten op de server dezelfde naam hebben.
- Verbeteringen in de betrouwbaarheid van de cloud.

## <a name="agent-version-7100"></a>Agent versie 7.1.0.0
De volgende releasenotes zijn voor versie 7.1.0.0 van de Azure File Sync-agent die op 12 juli 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 7.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Toegang tot of browsen op een servereindpuntlocatie via SMB is traag op Windows Server 2012 R2. 
- Verhoogde CPU-benutting na het installeren van de Azure File Sync v6-agent.
- Cloud tiering telemetrie verbeteringen.
- Diverse verbeteringen in de betrouwbaarheid voor cloudtiering en synchronisatie.

## <a name="agent-version-7000"></a>Agent versie 7.0.0.0
De volgende releasenotes zijn voor versie 7.0.0.0 van de Azure File Sync-agent (uitgebracht op 19 juni 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor grotere bestandsgroottes
    - Met de preview van grotere Azure-bestandsshares verhogen we ook onze ondersteuningslimieten voor bestandssynchronisatie. In deze eerste stap ondersteunt Azure File Sync nu maximaal 25 TB en 50 miljoen bestanden in één enkele, synchronisatie van naamruimte. Als u een aanvraag wilt indienen voor https://aka.ms/azurefilesatscalesurveyhet voorbeeld van de grote bestandsshare, vult u dit formulier in . 
- Ondersteuning voor firewall- en virtuele netwerkinstelling voor opslagaccounts
    - Azure File Sync ondersteunt nu de firewall- en virtuele netwerkinstelling voor opslagaccounts. Zie [Firewall- en virtuele netwerkinstellingen configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)om uw implementatie te configureren om te werken met de firewall- en virtuele netwerkinstelling.
- PowerShell-cmdlet om bestanden die zijn gewijzigd in het Azure-bestandsaandeel onmiddellijk te synchroniseren
    - Als u bestanden die in de Azure-bestandsshare worden gewijzigd, onmiddellijk wilt synchroniseren, kan de PowerDlet Invoke-AzStorageChangeDetection PowerShell worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestandsshare te starten. Deze cmdlet is bedoeld voor scenario's waarbij een of ander type geautomatiseerd proces wijzigingen aanbrengt in de Azure-bestandsshare of de wijzigingen worden uitgevoerd door een beheerder (zoals het verplaatsen van bestanden en mappen naar het aandeel). Voor wijzigingen van eindgebruikers is de aanbeveling om de Azure File Sync-agent in een IaaS-vm te installeren en eindgebruikers toegang te geven tot het bestandsaandeel via de IaaS-VM. Op deze manier worden alle wijzigingen snel gesynchroniseerd met andere agents zonder dat u de cmdlet Invoke-AzStorageSyncChangeDetection hoeft te gebruiken. Zie de documentatie [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) voor meer informatie.
- Verbeterde portalervaring als u bestanden tegenkomt die niet worden gesynchroniseerd
    - Als u bestanden hebt die niet worden gesynchroniseerd, maken we nu onderscheid tussen tijdelijke en aanhoudende fouten in de portal. Tijdelijke fouten lossen zichzelf meestal op zonder dat er een beheeractie nodig is. Een bestand dat momenteel in gebruik is, wordt bijvoorbeeld pas gesynchroniseerd nadat de bestandsgreep is gesloten. Voor aanhoudende fouten geven we nu het aantal bestanden weer dat door elke fout wordt beïnvloed. Het aantal permanente fouten wordt ook weergegeven in de bestanden die de kolom van alle servereindpunten niet synchroniseren in een synchronisatiegroep.
- Verbeterde herstel van Azure Backup-bestandsniveau
    - Afzonderlijke bestanden die zijn hersteld met Azure Backup, worden nu sneller gedetecteerd en gesynchroniseerd met het eindpunt van de server.
- Verbeterde betrouwbaarheid van cloudtiering-terugroepen cmdlet 
    - Met de cmdlet Invoke-StorageSyncFileRecall kunnen klanten nu per bestand het aantal opnieuw proberen en per bestand opnieuw vertragen, vergelijkbaar met robocopy. Voorheen zou deze cmdlet alle gelaagde bestanden onder een bepaald pad in willekeurige volgorde herinneren. Met de nieuwe parameter -Order herinnert deze cmdlet zich eerst de heetste gegevens en eer t.o.v. het cloudtieringbeleid (stop met herinneren of aan het datumbeleid is voldaan of dat de volumevrije ruimte is nageleefd; wat het eerst gebeurt).
- Alleen ondersteuning voor TLS 1.2 (TLS 1.0 en 1.1 is uitgeschakeld)
    - Azure File Sync ondersteunt nu alleen tls 1.2 op servers waarop TLS 1.0 en 1.1 zijn uitgeschakeld. Voorafgaand aan deze verbetering zou de serverregistratie mislukken als TLS 1.0 en 1.1 op de server zijn uitgeschakeld.
- Diverse prestatie- en betrouwbaarheidsverbeteringen voor synchronisatie en cloudlagen
    - Er zijn verschillende verbeteringen in de betrouwbaarheid en prestaties in deze release. Sommige van hen zijn gericht op het efficiënter maken van cloudtiering en Azure File Sync als geheel werkt beter in die situaties wanneer u een bandbreedtebeperkingsschema hebt ingesteld.

### <a name="evaluation-tool"></a>Evaluatietool
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het evaluatiehulpprogramma azure bestandssynchronisatie. Deze tool is een Azure PowerShell-cmdlet die controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie [Sectie Evaluatietool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) voor installatie- en gebruiksinstructies in de planningshandleiding. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server.

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde (beheerders) machtigingen.
- De agent wordt niet ondersteund op de implementatieoptie Nano Server.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent heeft minstens 2 GiB geheugen nodig. Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.
- De FileSync Agent-service (Storage Sync Agent) biedt geen ondersteuning voor servereindpunten op een volume waarop de svi-map (System Volume Information) is gecomprimeerd. Deze configuratie zal leiden tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- FSRM-bestandsschermen (File Server Resource Manager) kunnen eindeloze synchronisatiefouten veroorzaken wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. De Azure File Sync-agent moet worden geïnstalleerd nadat de serverafbeelding is geïmplementeerd en de sysprep-mini-installatie is voltooid.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Handleiding voor probleemoplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een periode.
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
 
### <a name="server-endpoint"></a>Servereindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat het eindpunt van de server wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het eindpunt van de server opnieuw. Als er 30 dagen zijn verstreken sinds het eindpunt van de server is verwijderd of als het eindpunt van de cloud is verwijderd, zijn gelaagde bestanden die niet zijn teruggeroepen, onbruikbaar.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturingssysteem- of toepassingspagingbestand niet op binnen een eindpuntlocatie van de server.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Eindpunt in de cloud
- Azure File Sync ondersteunt het rechtstreeks aanbrengen van wijzigingen in het Azure-bestandsaandeel. Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel moeten echter eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt één keer per 24 uur gestart voor een cloudeindpunt. Bovendien worden wijzigingen in een Azure-bestandsshare via het REST-protocol de laatste gewijzigde tijd van het SMB niet bijgewerkt en worden ze niet gezien als een wijziging door synchronisatie.
- De opslagsynchronisatieservice en/of opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met robocopy, gebruikt u de optie /MIR om bestandstijdstempels te bewaren. Dit zorgt ervoor dat oudere bestanden sneller worden gegelaagdeerd dan recent geopende bestanden.

## <a name="agent-version-6300"></a>Agent versie 6.3.0.0
De volgende releasenotes zijn voor versie 6.3.0.0 van de Azure File Sync-agent die op 27 juni 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Toegang tot of browsen op een servereindpuntlocatie via SMB is traag op Windows Server 2012 R2 
- Meer CPU-gebruik na installatie van de V6-agent Azure File Sync
- Verbeteringen in cloudlagen telemetrie

## <a name="agent-version-6200"></a>Agent versie 6.2.0.0
De volgende releasenotes zijn voor versie 6.2.0.0 van de Azure File Sync-agent die op 13 juni 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Na het maken van een servereindpunt kan een hoog CPU-gebruik optreden wanneer achtergrondterugroepen bestanden naar de server downloaden
- Synchronisatie- en cloudgelaagdheidsbewerkingen kunnen mislukken met foutECS_E_SERVER_CREDENTIAL_NEEDED als gevolg van het verlopen van token
- Het herinneren van een bestand kan mislukken als de URL om het bestand te downloaden gereserveerde tekens bevat 

## <a name="agent-version-6100"></a>Agent versie 6.1.0.0
De volgende releasenotes zijn voor versie 6.1.0.0 van de Azure File Sync-agent die op 6 mei 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 6.0.0.0.

Lijst met problemen die in deze release zijn opgelost:  
- Windows Admin Center kan de agentversie en servereindpuntconfiguratie niet weergeven op servers waarop Azure File Sync agent versie 6.0 is geïnstalleerd.

## <a name="agent-version-6000"></a>Agent versie 6.0.0.0
De volgende releasenotes zijn voor versie 6.0.0.0 van de Azure File Sync-agent (uitgebracht op 22 april 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor automatische update van agent
  - We hebben uw feedback gehoord en een functie voor automatische update toegevoegd aan de Azure File Sync-serveragent. Zie updatebeleid voor [Azure File Sync-agent](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)voor meer informatie .
- Ondersteuning voor Azure-acl.s.
  - Azure File Sync heeft altijd ondersteuning ondersteund voor het synchroniseren van ACL's tussen servereindpunten, maar de ACL's zijn niet gesynchroniseerd met het cloudeindpunt (Azure-bestandsshare). Deze release voegt ondersteuning toe voor het synchroniseren van ACL's tussen server- en cloudeindpunten.
- Parallelle synchronisatiesessies voor het uploaden en downloaden van een servereindpunt 
  - Servereindpunten ondersteunen nu het tegelijkertijd uploaden en downloaden van bestanden. U hoeft niet langer te wachten tot een download is voltooid, zodat bestanden kunnen worden geüpload naar het Azure-bestandsshare. 
- Nieuwe cloudtiering-cmdlets om de status van volume en lagen te krijgen
  - Twee nieuwe, serverlokale PowerShell-cmdlets kunnen nu worden gebruikt om informatie over cloudlagen en bestandsterugroepen te verkrijgen. Ze maken logboekinformatie van twee gebeurteniskanalen op de server beschikbaar:
    - Get-StorageSyncFileTieringResult geeft een overzicht van alle bestanden en hun paden die niet zijn gelaagd en rapporteert over de reden waarom.
    - Get-StorageSyncFileRecallResult rapporteert alle gebeurtenissen voor het terugroepen van bestanden. Het bevat elk bestand teruggeroepen en het pad, evenals succes of fout voor die recall.
  - Standaard kunnen beide gebeurteniskanalen elk maximaal 1 MB opslaan : u de hoeveelheid gerapporteerde bestanden verhogen door de grootte van het gebeurteniskanaal te vergroten.
- Ondersteuning voor fips-modus
  - Azure File Sync ondersteunt nu het inschakelen van de FIPS-modus op servers waarop de Azure File Sync-agent is geïnstalleerd.
    - Installeer de Azure File Sync-agent en de [PackageManagement-module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) op uw server voordat u de FIPS-modus op uw server inschakelt. Als FIPS al is ingeschakeld op de server, [downloadt u](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) de [Module PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) handmatig naar uw server.
- Diverse verbeteringen in de betrouwbaarheid voor cloudtiering en -synchronisatie

### <a name="evaluation-tool"></a>Evaluatietool
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het evaluatiehulpprogramma azure bestandssynchronisatie. Deze tool is een Azure PowerShell-cmdlet die controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie [Sectie Evaluatietool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) voor installatie- en gebruiksinstructies in de planningshandleiding. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server.

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde (beheerders) machtigingen.
- De agent wordt niet ondersteund op de implementatieoptie Nano Server.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent heeft minstens 2 GiB geheugen nodig. Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.
- De FileSync Agent-service (Storage Sync Agent) biedt geen ondersteuning voor servereindpunten op een volume waarop de svi-map (System Volume Information) is gecomprimeerd. Deze configuratie zal leiden tot onverwachte resultaten.

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- FSRM-bestandsschermen (File Server Resource Manager) kunnen eindeloze synchronisatiefouten veroorzaken wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. De Azure File Sync-agent moet worden geïnstalleerd nadat de serverafbeelding is geïmplementeerd en de sysprep-mini-installatie is voltooid.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Handleiding voor probleemoplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een periode.
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
 
### <a name="server-endpoint"></a>Servereindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat het eindpunt van de server wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het eindpunt van de server opnieuw. Als er 30 dagen zijn verstreken sinds het eindpunt van de server is verwijderd of als het eindpunt van de cloud is verwijderd, zijn gelaagde bestanden die niet zijn teruggeroepen, onbruikbaar.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturingssysteem- of toepassingspagingbestand niet op binnen een eindpuntlocatie van de server.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Eindpunt in de cloud
- Azure File Sync ondersteunt het rechtstreeks aanbrengen van wijzigingen in het Azure-bestandsaandeel. Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel moeten echter eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt één keer per 24 uur gestart voor een cloudeindpunt. Bovendien worden wijzigingen in een Azure-bestandsshare via het REST-protocol de laatste gewijzigde tijd van het SMB niet bijgewerkt en worden ze niet gezien als een wijziging door synchronisatie.
- De opslagsynchronisatieservice en/of opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met robocopy, gebruikt u de optie /MIR om bestandstijdstempels te bewaren. Dit zorgt ervoor dat oudere bestanden sneller worden gegelaagdeerd dan recent geopende bestanden.
- Wanneer u bestandseigenschappen bekijkt vanuit een SMB-client, lijkt het misschien of het kenmerk 'offline' niet goed is ingesteld als gevolg van SMB-caching van bestandsmetagegevens.

## <a name="agent-version-5200"></a>Agent versie 5.2.0.0
De volgende releasenotes zijn voor versie 5.2.0.0 van de Azure File Sync-agent die op 4 april 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 5.0.2.0.

Lijst met problemen die in deze release zijn opgelost:  
- Betrouwbaarheidsverbeteringen voor offline functies voor gegevensoverdracht en het hervatten van gegevensoverdracht
- Verbeteringen in telemetrie synchroniseren

## <a name="agent-version-5100"></a>Agent versie 5.1.0.0
De volgende releasenotes zijn voor versie 5.1.0.0 van de Azure File Sync-agent die op 7 maart 2019 is uitgebracht. Deze notities zijn een aanvulling op de release notes voor versie 5.0.2.0.

Lijst met problemen die in deze release zijn opgelost:  
- Bestanden kunnen niet worden gesynchroniseerd met fout 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) als de opsomming van wijzigingen niet op de server optreedt
- Als een synchronisatiesessie of -bestand een fout ontvangt 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), wordt de bewerking nu opnieuw geprobeerd
- Bestanden kunnen niet worden gesynchroniseerd met fout 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Er kan een hoog geheugengebruik optreden bij het terugroepen van bestanden
- Verbeteringen in cloudlagen telemetrie 

## <a name="agent-version-5020"></a>Agent versie 5.0.2.0
De volgende releasenotes zijn voor versie 5.0.2.0 van de Azure File Sync-agent (uitgebracht op 12 februari 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbeteringen en problemen die zijn opgelost

- Ondersteuning voor Azure Government-cloud
  - We hebben preview-ondersteuning toegevoegd voor de Azure Government-cloud. Hiervoor is een abonnement op de witte lijst en een speciale agent download van Microsoft vereist. Om toegang te krijgen tot de [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)preview, stuur ons dan direct een e-mail naar .
- Ondersteuning voor gegevensontdubbeling
    - Data deduplicatie wordt nu volledig ondersteund met cloud tiering ingeschakeld op Windows Server 2016 en Windows Server 2019. Door ontdubbeling mogelijk te maken op een volume met cloudtiering ingeschakeld, u meer on-premises bestanden in de cache opslaan zonder meer opslagruimte in te richten.
- Ondersteuning voor offline gegevensoverdracht (bijvoorbeeld via Data Box)
    - Migreer eenvoudig grote hoeveelheden gegevens naar Azure File Sync via alle middelen die u kiest. U kiezen voor Azure Data Box, AzCopy en zelfs migratieservices van derden. U hoeft geen enorme hoeveelheden bandbreedte te gebruiken om uw gegevens in Azure te krijgen, in het geval van Data Box - mail het daar gewoon! Zie [Offline gegevensoverdrachtdocumenten voor](https://aka.ms/AFS/OfflineDataTransfer)meer informatie.
- Verbeterde synchronisatieprestaties
    - Klanten met meerdere servereindpunten op hetzelfde volume hebben mogelijk trage synchronisatieprestaties ervaren voorafgaand aan deze release. Azure File Sync maakt één keer per dag een tijdelijke VSS-momentopname op de server om bestanden met geopende handgrepen te synchroniseren. Synchronisatie ondersteunt nu meerdere servereindpunten die worden gesynchroniseerd op een volume wanneer een VSS-synchronisatiesessie actief is. U hoeft niet langer te wachten tot een VSS-synchronisatiesessie is voltooid, zodat de synchronisatie kan worden hervat op andere servereindpunten op het volume.
- Verbeterde monitoring in het portaal
    - Er zijn grafieken toegevoegd in de portal Storage Sync Service om het overzicht te geven:
        - Aantal gesynchroniseerde bestanden
        - Omvang van de overgedragen gegevens
        - Aantal bestanden dat niet wordt gesynchroniseerd
        - Omvang van de aangeroepen gegevens
        - Serververbindingsstatus
    - Zie [Azure File Sync controleren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)voor meer informatie.
- Verbeterde schaalbaarheid en betrouwbaarheid
    - Het maximum aantal bestandssysteemobjecten (mappen en bestanden) in een map is gestegen tot 1.000.000. De vorige limiet was 200.000.
    - Sync probeert de gegevensoverdracht te hervatten in plaats van opnieuw te verzenden wanneer een overdracht wordt onderbroken voor grote bestanden 

### <a name="evaluation-tool"></a>Evaluatietool
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van het evaluatiehulpprogramma azure bestandssynchronisatie. Deze tool is een Azure PowerShell-cmdlet die controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. Zie [Sectie Evaluatietool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) voor installatie- en gebruiksinstructies in de planningshandleiding. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallatie en serverconfiguratie
Zie [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het installeren en configureren van de Azure File Sync-agent met Windows Server.

- Het installatiepakket van de agent moet worden geïnstalleerd met verhoogde (beheerders) machtigingen.
- De agent wordt niet ondersteund op implementatieopties voor Windows Server Core of Nano Server.
- De agent wordt alleen ondersteund op Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2.
- De agent heeft minstens 2 GiB geheugen nodig. Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.
- De FileSync Agent-service (Storage Sync Agent) biedt geen ondersteuning voor servereindpunten op een volume waarop de svi-map (System Volume Information) is gecomprimeerd. Deze configuratie zal leiden tot onverwachte resultaten.
- FIPS-modus wordt niet ondersteund en moet worden uitgeschakeld. 

### <a name="interoperability"></a>Interoperabiliteit
- Antivirusprogramma's, back-uptoepassingen en andere toepassingen die toegang hebben tot gelaagde bestanden, kunnen leiden tot ongewenste intrekking tenzij ze het kenmerk offline respecteren en het lezen van de inhoud van die bestanden overslaan. Zie [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)voor meer informatie.
- FSRM-bestandsschermen (File Server Resource Manager) kunnen eindeloze synchronisatiefouten veroorzaken wanneer bestanden worden geblokkeerd vanwege het bestandsscherm.
- Het uitvoeren van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. De Azure File Sync-agent moet worden geïnstalleerd nadat de serverafbeelding is geïmplementeerd en de sysprep-mini-installatie is voltooid.

### <a name="sync-limitations"></a>Synchronisatiebeperkingen
De volgende items worden niet gesynchroniseerd, maar de rest van het systeem blijft normaal functioneren:
- Bestanden met niet-ondersteunde tekens. Zie [Handleiding voor probleemoplossing](storage-sync-files-troubleshoot.md#handling-unsupported-characters) voor een lijst met niet-ondersteunde tekens.
- Bestanden of mappen die eindigen met een periode.
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
 
### <a name="server-endpoint"></a>Servereindpunt
- Een servereindpunt kan alleen worden gemaakt op een NTFS-volume. ReFS, FAT, FAT32 en andere bestandssystemen worden op dit moment niet ondersteund door Azure File Sync.
- Gelaagde bestanden worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat het eindpunt van de server wordt verwijderd. Als u de toegang tot de bestanden wilt herstellen, maakt u het eindpunt van de server opnieuw. Als er 30 dagen zijn verstreken sinds het eindpunt van de server is verwijderd of als het eindpunt van de cloud is verwijderd, zijn gelaagde bestanden die niet zijn teruggeroepen, onbruikbaar.
- Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.
- Failoverclustering wordt alleen ondersteund met geclusterde schijven, maar niet met CSV's (Cluster Shared Volume).
- Een servereindpunt kan niet worden genest. Een eindpunt van dit type kan zich samen met een ander eindpunt op hetzelfde volume bevinden.
- Sla een besturingssysteem- of toepassingspagingbestand niet op binnen een eindpuntlocatie van de server.
- De servernaam in de portal wordt niet bijgewerkt als de naam van de server wordt gewijzigd.

### <a name="cloud-endpoint"></a>Eindpunt in de cloud
- Azure File Sync ondersteunt het rechtstreeks aanbrengen van wijzigingen in het Azure-bestandsaandeel. Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel moeten echter eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt één keer per 24 uur gestart voor een cloudeindpunt. Bovendien worden wijzigingen in een Azure-bestandsshare via het REST-protocol de laatste gewijzigde tijd van het SMB niet bijgewerkt en worden ze niet gezien als een wijziging door synchronisatie.
- De opslagsynchronisatieservice en/of opslagaccount kunnen worden verplaatst naar een andere brongroep of -abonnement binnen de bestaande Azure AD-tenant. Als het opslagaccount wordt verplaatst, moet u de Hybrid File Sync Service toegang geven tot het opslagaccount (zie [Ervoor zorgen dat Azure File Sync toegang heeft tot het opslagaccount).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Azure File Sync biedt geen ondersteuning voor het verplaatsen van het abonnement naar een andere Azure AD-tenant.

### <a name="cloud-tiering"></a>Cloudopslaglagen
- Als een gelaagd bestand met behulp van Robocopy naar een andere locatie wordt gekopieerd, wordt het resulterende bestand niet in een laag geplaatst. Het kenmerk 'offline' kan zijn ingesteld omdat Robocopy dat kenmerk onterecht opneemt in kopieerbewerkingen.
- Wanneer u bestanden kopieert met robocopy, gebruikt u de optie /MIR om bestandstijdstempels te bewaren. Dit zorgt ervoor dat oudere bestanden sneller worden gegelaagdeerd dan recent geopende bestanden.
- Wanneer u bestandseigenschappen bekijkt vanuit een SMB-client, lijkt het misschien of het kenmerk 'offline' niet goed is ingesteld als gevolg van SMB-caching van bestandsmetagegevens.
