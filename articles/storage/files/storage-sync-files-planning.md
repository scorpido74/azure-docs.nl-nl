---
title: Een Azure File Sync-implementatie plannen | Microsoft Docs
description: Meer informatie over hoe u rekening moet houden bij het plannen van een Azure Files-implementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 483603b8ff2f4b51f85d21d6ff4f02ad6f8a8272
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162086"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planning voor de implementatie van Azure Files Sync
Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken voor toegang tot uw gegevens lokaal, zoals SMB, NFS en FTPS. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

In dit artikel worden belang rijke aandachtspunten voor een Azure File Sync-implementatie beschreven. Het is raadzaam om ook [een Azure files implementatie](storage-files-planning.md)te lezen. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync terminologie
Voordat u de details van planning voor een Azure File Sync-implementatie krijgt, is het belang rijk dat u de terminologie begrijpt.

### <a name="storage-sync-service"></a>Opslag synchronisatie service
De opslag synchronisatie service is de Azure-resource op het hoogste niveau voor Azure File Sync. De resource van de opslag synchronisatie service is een peer van de bron van het opslag account en kan ook worden geïmplementeerd voor Azure-resource groepen. Een afzonderlijke resource op het hoogste niveau van de bron van het opslag account is vereist omdat de opslag synchronisatie service synchronisatie relaties met meerdere opslag accounts kan maken via meerdere synchronisatie groepen. Voor een abonnement kunnen meerdere bronnen voor opslag synchronisatie service zijn geïmplementeerd.

### <a name="sync-group"></a>Synchronisatie groep
Een synchronisatiegroep definieert de synchronisatietopologie voor een verzameling bestanden. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Als u bijvoorbeeld twee verschillende sets bestanden hebt die u met Azure File Sync wilt beheren, maakt u twee synchronisatie groepen en voegt u verschillende eind punten toe aan elke synchronisatie groep. Een opslag synchronisatie service kan net zo veel synchronisatie groepen als u wilt hosten.  

### <a name="registered-server"></a>Geregistreerde server
Het geregistreerde Server object vertegenwoordigt een vertrouwens relatie tussen uw server (of cluster) en de opslag synchronisatie service. U kunt zoveel servers naar een exemplaar van de opslag synchronisatie service registreren als u wilt. Een server (of cluster) kan echter slechts met één opslag synchronisatie service tegelijk worden geregistreerd.

### <a name="azure-file-sync-agent"></a>Azure File Sync-agent
De Azure File Sync-agent is een downloadbaar pakket waardoor Windows Server met een Azure-bestandsshare kan worden gesynchroniseerd. De Azure File Sync-agent heeft drie hoofd onderdelen: 
- **FileSyncSvc. exe**: de achtergrond service die verantwoordelijk is voor het bewaken van wijzigingen op server eindpunten en voor het initiëren van synchronisatie sessies naar Azure.
- **StorageSync. sys**: het Azure file sync-bestandssysteem filter, dat verantwoordelijk is voor het belagen van bestanden aan Azure files (wanneer Cloud lagen zijn ingeschakeld).
- **Power shell Management cmdlets**: Power shell-cmdlets die u gebruikt om te communiceren met de Azure-resource provider micro soft. StorageSync. U kunt dit vinden op de volgende locaties (standaard):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Server eindpunt
Een servereindpunt representeert een bepaalde locatie op een geregistreerde server, bijvoorbeeld een map op een servervolume. Meerdere server eindpunten kunnen bestaan op hetzelfde volume als hun naam ruimten elkaar niet overlappen (bijvoorbeeld `F:\sync1` en `F:\sync2`). U kunt beleid voor Cloud lagen afzonderlijk configureren voor elk server eindpunt. 

U kunt een server eindpunt maken via een koppel punt. Opmerking: mountpoints binnen het server-eind punt worden overgeslagen.  

U kunt een server eindpunt maken op het systeem volume, maar er zijn twee beperkingen als u dit doet:
* Cloud lagen kunnen niet worden ingeschakeld.
* Het snel terugzetten van de naam ruimte (waarbij het systeem snel de volledige naam ruimte inpakt en vervolgens begint met het terughalen van inhoud), wordt niet uitgevoerd.


> [!Note]  
> Alleen niet-Verwissel bare volumes worden ondersteund.  Stations die zijn toegewezen via een externe share, worden niet ondersteund voor een server eindpunt pad.  Daarnaast kan een server eindpunt zich bevinden op het Windows-systeem volume, hoewel Cloud lagen niet worden ondersteund op het systeem volume.

Als u een server locatie met een bestaande set bestanden als een server-eind punt aan een synchronisatie groep toevoegt, worden deze bestanden samengevoegd met andere bestanden die zich al op andere eind punten in de synchronisatie groep bevinden.

### <a name="cloud-endpoint"></a>Cloud-eind punt
Een eind punt in de Cloud is een Azure-bestands share die deel uitmaakt van een synchronisatie groep. De volledige Azure-bestands shares worden gesynchroniseerd en een Azure-bestands share kan slechts lid zijn van één Cloud eindpunt. Daarom kan een Azure-bestands share slechts lid zijn van één synchronisatie groep. Als u een Azure-bestands share met een bestaande set bestanden als een Cloud-eind punt aan een synchronisatie groep toevoegt, worden de bestaande bestanden samengevoegd met andere bestanden die zich al op andere eind punten in de synchronisatie groep bevinden.

> [!Important]  
> Azure File Sync ondersteunt het maken van wijzigingen aan de Azure-bestands share rechtstreeks. Wijzigingen die zijn aangebracht op de Azure-bestands share moeten echter eerst worden gedetecteerd door een Azure File Sync wijzigings detectie taak. Een wijzigings detectie taak wordt slechts eenmaal per 24 uur geïnitieerd voor een Cloud eindpunt. Daarnaast worden wijzigingen die zijn aangebracht in een Azure-bestands share via het REST-protocol, de SMB-tijd voor het laatst gewijzigd niet bijgewerkt en wordt deze niet gezien als een wijziging door synchronisatie. Zie [Azure files Veelgestelde vragen](storage-files-faq.md#afs-change-detection)voor meer informatie.

### <a name="cloud-tiering"></a>Cloudopslaglagen 
Cloud lagen is een optionele functie van Azure File Sync waarbij veelgebruikte bestanden lokaal op de server worden opgeslagen in de cache, terwijl alle andere bestanden worden gelaagd op Azure Files op basis van beleids instellingen. Zie [Wat is Cloud lagen](storage-sync-cloud-tiering.md)? voor meer informatie.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Systeem vereisten en-interoperabiliteit Azure File Sync 
In deze sectie worden de systeem vereisten en interoperabiliteit van Azure File Sync agent met Windows Server-functies en-rollen en oplossingen van derden beschreven.

### <a name="evaluation-cmdlet"></a>Evaluatie-cmdlet
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van de Azure File Sync Evaluation-cmdlet. Met deze cmdlet wordt gecontroleerd op mogelijke problemen met uw bestands systeem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturings systeem. De controles dekken de meeste van de hieronder genoemde functies We raden u aan de rest van deze sectie zorgvuldig te lezen om ervoor te zorgen dat uw implementatie probleemloos verloopt. 

De evaluatie-cmdlet kan worden geïnstalleerd door de installatie van de AZ Power shell-module, die kan worden geïnstalleerd door de volgende instructies te volgen: [Installeer en configureer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Gebruik  
U kunt het evaluatie hulpprogramma op een aantal verschillende manieren aanroepen: u kunt de systeem controles, de gegevensset controleert of beide. Systeem-en gegevensset controles uitvoeren: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Alleen uw gegevensset testen:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Alleen systeem vereisten testen:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
De resultaten weer geven in CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systeemvereisten
- Een server met een van de volgende versies van het besturings systeem:

    | Version | Ondersteunde Sku's | Ondersteunde implementatie opties |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Data Center en Standard | Volledig en kern geheugen |
    | Windows Server 2016 | Data Center en Standard | Volledig en kern geheugen |
    | Windows Server 2012 R2 | Data Center en Standard | Volledig en kern geheugen |
    | Windows Server IoT 2019 voor opslag| Data Center en Standard | Volledig en kern geheugen |
    | Windows Storage Server 2016| Data Center en Standard | Volledig en kern geheugen |
    | Windows Storage Server 2012 R2| Data Center en Standard | Volledig en kern geheugen |

    Toekomstige versies van Windows Server worden toegevoegd zodra ze worden vrijgegeven.

    > [!Important]  
    > U wordt aangeraden alle servers die u gebruikt met Azure File Sync up-to-date te houden met de meest recente updates van Windows Update. 

- Een server met mini maal 2 GiB geheugen.

    > [!Important]  
    > Als de server wordt uitgevoerd op een virtuele machine waarvoor dynamisch geheugen is ingeschakeld, moet de VM worden geconfigureerd met een minimale 2048-MiB van het geheugen.
    
- Een lokaal gekoppeld volume dat is geformatteerd met het NTFS-bestands systeem.

### <a name="file-system-features"></a>Bestandssysteem functies

| Functie | Ondersteunings status | Opmerkingen |
|---------|----------------|-------|
| Toegangs beheer lijsten (Acl's) | Volledig ondersteund | Windows-Acl's worden bewaard door Azure File Sync en worden afgedwongen door Windows Server op server-eind punten. Windows-Acl's worden nog niet ondersteund door Azure Files als bestanden rechtstreeks in de cloud worden geopend. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Koppelpunten | Gedeeltelijk ondersteund | Koppel punten kunnen de hoofdmap van een server eindpunt zijn, maar ze worden overgeslagen als ze zijn opgenomen in de naam ruimte van een server eindpunt. |
| Koppelingen | Overgeslagen | Bijvoorbeeld Distributed File System mappen DfrsrPrivate en DFSRoots. |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Sparse bestanden | Volledig ondersteund | Verspreide bestanden worden gesynchroniseerd (worden niet geblokkeerd), maar gesynchroniseerd met de Cloud als een volledig bestand. Als de bestands inhoud in de Cloud (of op een andere server) wordt gewijzigd, wordt het bestand niet langer verspreid wanneer de wijziging wordt gedownload. |
| Alternatieve gegevens stromen (ADS) | Behouden, maar niet gesynchroniseerd | Classificatie Tags die zijn gemaakt door de infra structuur voor bestands classificatie worden bijvoorbeeld niet gesynchroniseerd. Bestaande classificatie Tags op bestanden op elk van de server-eind punten blijven ongewijzigd. |

> [!Note]  
> Alleen NTFS-volumes worden ondersteund. ReFS, FAT, FAT32 en andere bestands systemen worden niet ondersteund.

### <a name="files-skipped"></a>Overgeslagen bestanden

| Bestand/map | Opmerking |
|-|-|
| pagefile.sys | Bestand dat specifiek is voor systeem |
| Desktop.ini | Bestand dat specifiek is voor systeem |
| duims. db | Tijdelijk bestand voor miniatuur weergaven |
| ehthumbs. db | Tijdelijk bestand voor miniatuur weergaven van media |
| ~$\*.\* | Tijdelijk Office-bestand |
| \*. tmp | Tijdelijk bestand |
| \*. LACCDB | Access DB-bestand vergren delen|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern synchronisatie bestand|
| Informatie over \\systeem volume | Map die specifiek is voor het volume |
| $RECYCLE. DOCKOPSLAGLOCATIE| Map |
| \\SyncShareState | Map voor synchronisatie |

### <a name="failover-clustering"></a>Failover Clustering
Windows Server Failover Clustering wordt ondersteund door Azure File Sync voor de implementatie optie ' Bestands server voor algemeen gebruik '. Failover Clustering wordt niet ondersteund op Scale-out bestandsserver voor toepassings gegevens (SOFS) of op geclusterde gedeelde volumes (Csv's).

> [!Note]  
> De Azure File Sync-agent moet worden geïnstalleerd op elk knoop punt in een failovercluster zodat synchronisatie goed kan worden uitgevoerd.

### <a name="data-deduplication"></a>Gegevensontdubbeling
**Windows server 2016 en Windows server 2019**   
Gegevensontdubbeling wordt ondersteund op volumes waarvoor Cloud lagen zijn ingeschakeld op Windows Server 2016 en Windows Server 2019. Door Gegevensontdubbeling in te scha kelen op een volume waarvoor Cloud lagen zijn ingeschakeld, kunt u meer bestanden on-premises opslaan zonder dat u meer opslag ruimte hoeft in te richten. 

Als Gegevensontdubbeling is ingeschakeld op een volume waarop Cloud lagen zijn ingeschakeld, worden geoptimaliseerde bestanden in de eindpunt locatie van het server niveau vergelijkbaar met een normaal bestand op basis van de beleids instellingen voor Cloud lagen. Zodra de geoptimaliseerde bestanden voor ontdubbeling zijn gelaagd, wordt de garbagecollection-taak voor Gegevensontdubbeling automatisch uitgevoerd om schijf ruimte vrij te maken door overbodige segmenten te verwijderen waarnaar niet meer wordt verwezen door andere bestanden op het volume.

Houd er rekening mee dat de besparing van volumes alleen van toepassing is op de-server. uw gegevens in de Azure-bestands share worden niet ontdubbeld.

> [!Note]  
> Voor de ondersteuning van Gegevensontdubbeling op volumes waarvoor Cloud lagen zijn ingeschakeld op Windows Server 2019, moet Windows Update [KB4520062](https://support.microsoft.com/help/4520062) zijn geïnstalleerd en moeten Azure file sync agent versie 9.0.0.0 of nieuwer zijn vereist.

**Windows Server 2012 R2**  
Azure File Sync biedt geen ondersteuning voor Gegevensontdubbeling en Cloud lagen op hetzelfde volume op Windows Server 2012 R2. Als Gegevensontdubbeling op een volume is ingeschakeld, moet Cloud lagen worden uitgeschakeld. 

**Opmerkingen**
- Als Gegevensontdubbeling is geïnstalleerd voordat de Azure File Sync-agent wordt geïnstalleerd, moet opnieuw opstarten zijn vereist voor de ondersteuning van Gegevensontdubbeling en Cloud lagen op hetzelfde volume.
- Als Gegevensontdubbeling is ingeschakeld op een volume nadat Cloud lagen zijn ingeschakeld, optimaliseert de eerste optimalisatie taak voor ontdubbeling bestanden op het volume dat nog niet al is gelaagd en heeft de volgende invloed op Cloud lagen:
    - Het beleid voor beschik bare ruimte gaat door met het heatmap met behulp van de beschik bare ruimte op het volume.
    - Met datum beleid wordt het trapsgewijs scha kelen van bestanden die mogelijk anderszins in aanmerking komen voor het maken van lagen, overgeslagen door de optimalisatie taak voor ontdubbeling om toegang te krijgen tot de bestanden.
- Voor voortdurende optimalisatie taken met ontdubbeling wordt de Cloud Tiering met het datum beleid vertraagd door de [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) -instelling voor gegevensontdubbeling als het bestand nog niet is gelaagd. 
    - Voor beeld: als de instelling MinimumFileAgeDays zeven dagen is en het beleid voor Cloud lagen 30 dagen is, worden de bestanden na 37 dagen in het datum beleid gelaagd.
    - Opmerking: wanneer een bestand wordt gelaagd door Azure File Sync, wordt het bestand door de optimalisatie taak voor ontdubbeling overgeslagen.
- Als een server met Windows Server 2012 R2 waarop de Azure File Sync-agent is geïnstalleerd, is bijgewerkt naar Windows Server 2016 of Windows Server 2019, moeten de volgende stappen worden uitgevoerd voor de ondersteuning van Gegevensontdubbeling en Cloud lagen op hetzelfde volume:  
    - Verwijder de Azure File Sync-agent voor Windows Server 2012 R2 en start de server opnieuw op.
    - Down load de Azure File Sync-agent voor de nieuwe versie van het besturings systeem van de server (Windows Server 2016 of Windows Server 2019).
    - Installeer de Azure File Sync agent en start de server opnieuw op.  
    
    Opmerking: de Azure File Sync configuratie-instellingen op de server blijven behouden wanneer de agent wordt verwijderd en opnieuw wordt geïnstalleerd.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync ondersteunt interop met DFS-naam ruimten (DFS-N) en DSF-replicatie (DFS-R).

**DFS-naam ruimten (DFS-n)** : Azure File Sync wordt volledig ondersteund op DFS-N-servers. U kunt de Azure File Sync-agent installeren op een of meer DFS-N-leden om gegevens te synchroniseren tussen de server eindpunten en het Cloud eindpunt. Zie overzicht van DFS- [naam ruimten](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)voor meer informatie.
 
**DSF-replicatie (DFS-r)** : Aangezien DFS-r en Azure file sync beide replicatie oplossingen zijn, raden wij in de meeste gevallen DFS-r te vervangen door Azure file sync. Er zijn echter verschillende scenario's waarin u DFS-R en Azure File Sync tegelijk wilt gebruiken:

- U migreert van een DFS-R-implementatie naar een Azure File Sync-implementatie. Zie [een DSF-replicatie-implementatie (DFS-R) migreren naar Azure file sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)voor meer informatie.
- Niet elke on-premises server waarvoor een kopie van uw bestands gegevens nodig is, kan rechtstreeks op internet worden aangesloten.
- Vertakkings servers consolideren gegevens op één hub-server waarvoor u Azure File Sync wilt gebruiken.

Voor Azure File Sync en DFS-R om naast elkaar te werken:

1. Azure File Sync Cloud lagen moeten worden uitgeschakeld op volumes met gerepliceerde DFS-R-mappen.
2. Server eindpunten mogen niet worden geconfigureerd in DFS-R-alleen-lezen replicatie mappen.

Zie [DSF-replicatie Overview](https://technet.microsoft.com/library/jj127250)voor meer informatie.

### <a name="sysprep"></a>Sysprep
Het gebruik van Sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. De agent installatie en-Server registratie moeten plaatsvinden na het implementeren van de server installatie kopie en het volt ooien van de Mini-Setup van Sysprep.

### <a name="windows-search"></a>Windows Search
Als Cloud lagen zijn ingeschakeld op een server eindpunt, worden de gelaagde bestanden overgeslagen en niet geïndexeerd met Windows Search. Niet-gelaagde bestanden worden correct geïndexeerd.

### <a name="antivirus-solutions"></a>Antivirus oplossingen
Omdat anti virus werkt door bestanden te scannen op bekende schadelijke code, kan een antivirus product ertoe leiden dat gelaagde bestanden worden teruggehaald. In versies 4,0 en hoger van de Azure File Sync-agent hebben gelaagde bestanden het beveiligde Windows-kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ingesteld. We raden u aan om te leren werken met uw software leverancier voor meer informatie over het configureren van de oplossing voor het overs laan van het lezen van bestanden met deze kenmerkset (veel doen dat automatisch). 

De interne anti-virus oplossingen van micro soft, Windows Defender en System Center Endpoint Protection (SCEP), over het automatisch overs laan van het lezen van bestanden waarvoor dit kenmerk is ingesteld. We hebben deze getest en een klein probleem geïdentificeerd: wanneer u een server aan een bestaande synchronisatie groep toevoegt, worden bestanden die kleiner zijn dan 800 bytes op de nieuwe server ingetrokken (gedownload). Deze bestanden blijven op de nieuwe server en worden niet getierd, omdat ze niet voldoen aan de vereiste voor de laag limiet (> 64 KB).

> [!Note]  
> Antivirus leveranciers kunnen de compatibiliteit controleren tussen hun product en Azure File Sync met behulp van de [Azure file sync anti virus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), die beschikbaar is voor downloaden in het micro soft Download centrum.

### <a name="backup-solutions"></a>Back-upoplossingen
Net als bij antivirus oplossingen kunnen back-upoplossingen ertoe leiden dat gelaagde bestanden worden teruggehaald. U kunt het beste een Cloud back-upoplossing gebruiken om een back-up te maken van de Azure-bestands share in plaats van een on-premises back-upproduct

Als u een on-premises back-upoplossing gebruikt, moeten back-ups worden uitgevoerd op een server in de synchronisatie groep waarvoor Cloud lagen zijn uitgeschakeld. Wanneer u een herstel bewerking uitvoert, gebruikt u de opties op volume-of bestands niveau herstellen. Bestanden die zijn hersteld met de optie herstel op bestands niveau worden gesynchroniseerd naar alle eind punten in de synchronisatie groep en bestaande bestanden worden vervangen door de versie die wordt hersteld vanuit een back-up.  Herstel bewerkingen op volume niveau worden niet vervangen door nieuwere bestands versies in de Azure-bestands share of andere server eindpunten.

> [!Note]  
> Herstellen met Bare-Metal (BMR) kan leiden tot onverwachte resultaten en wordt op dit moment niet ondersteund.

> [!Note]  
> Met versie 9 van de Azure File Sync agent worden VSS-moment opnamen (inclusief het tabblad vorige versies) nu ondersteund op volumes waarvoor Cloud lagen zijn ingeschakeld. U moet echter compatibiliteit met eerdere versies inschakelen via Power shell. [Meer informatie](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Oplossingen voor versleuteling
Ondersteuning voor versleutelings oplossingen is afhankelijk van hoe ze worden geïmplementeerd. Het is bekend dat Azure File Sync werkt met:

- BitLocker-versleuteling
- Azure Information Protection, Azure Rights Management Services (Azure RMS) en Active Directory RMS

Azure File Sync is bekend dat deze niet werkt met:

- EFS (Encrypted File System) met NTFS

Over het algemeen moet Azure File Sync interoperabiliteit ondersteunen met versleutelings oplossingen die onder het bestands systeem vallen, zoals BitLocker, en met oplossingen die zijn geïmplementeerd in de bestands indeling, zoals Azure Information Protection. Er is geen speciale interoperabiliteit gemaakt voor oplossingen die boven het bestands systeem (zoals NTFS EFS) vallen.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere HSM-oplossingen (hiërarchische opslag beheer)
Er mogen geen andere HSM-oplossingen worden gebruikt met Azure File Sync.

## <a name="region-availability"></a>Beschikbaarheid in regio’s
Azure File Sync is alleen beschikbaar in de volgende regio's:

| Regio | Locatie van Data Center |
|--------|---------------------|
| Australië - oost | Nieuwe South Wales |
| Australië - zuidoost | Victoria |
| Brazilië - zuid | Sao Paulo-status |
| Canada - midden | Toronto |
| Canada - oost | Quebec |
| India - centraal | Pune |
| US - centraal | Iowa |
| Azië - oost | Hongkong SAR |
| US - oost | Virginia |
| US - oost 2 | Virginia |
| Frankrijk - centraal | Parijs |
| Frankrijk-zuid * | Marseille |
| Korea - centraal | Seoul |
| Korea - zuid | Busan |
| Japan - oost | Tokyo, Saitama |
| Japan - west | Osaka |
| US - noord-centraal | Illinois |
| Europa - noord | Ierland |
| Zuid-Afrika - noord | Johannes Burg |
| Zuid-Afrika-west * | Kaap stad |
| US - zuid-centraal | Holland |
| India - zuid | Chennai |
| Azië - zuidoost | Singapore |
| Verenigd Koninkrijk Zuid | Londen |
| Verenigd Koninkrijk West | Cardiff |
| VS (overheid) - Arizona | Arizona |
| VS (overheid) - Texas | Holland |
| VS (overheid) - Virginia | Virginia |
| UAE - noord | Dubai |
| UAE-centraal * | Abu Dhabi |
| Europa -west | Nederland |
| US - west-centraal | Wyoming |
| US - west | Californië |
| US - west 2 | Utrecht |

Azure File Sync ondersteunt alleen synchronisatie met een Azure-bestands share die zich in dezelfde regio bevinden als de opslag synchronisatie service.

Voor de regio's die zijn gemarkeerd met sterretjes, moet u contact opnemen met de ondersteuning van Azure om toegang aan te vragen tot Azure Storage in die regio's. Het proces wordt beschreven in [dit document](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Herstel na nood gevallen voor Azure
Als u wilt beveiligen tegen het verlies van een Azure-regio, wordt Azure File Sync geïntegreerd met [geo-redundante opslag](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). GRS-opslag werkt met behulp van asynchrone blok replicatie tussen opslag in de primaire regio, waarmee u normaal gesp roken communiceert en opslag in de gekoppelde secundaire regio. In het geval van een nood situatie waardoor een Azure-regio tijdelijk of permanent offline gaat, wordt de opslag van micro soft overgezet naar de gekoppelde regio. 

> [!Warning]  
> Als u uw Azure-bestands share als een Cloud-eind punt in een GRS-opslag account gebruikt, mag u de failover van het opslag account niet starten. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen. In het geval van een verlies van een Azure-regio, zal micro soft de failover van het opslag account activeren op een manier die compatibel is met Azure File Sync.

Ter ondersteuning van de failover-integratie tussen geo-redundante opslag en Azure File Sync, worden alle Azure File Sync regio's gekoppeld aan een secundaire regio die overeenkomt met de secundaire regio die wordt gebruikt door de opslag. Deze paren zijn als volgt:

| Primaire regio      | Gekoppelde regio      |
|---------------------|--------------------|
| Australië - oost      | Australië - zuidoost|
| Australië - zuidoost | Australië - oost     |
| Brazilië - zuid        | US - zuid-centraal   |
| Canada - midden      | Canada - oost        |
| Canada - oost         | Canada - midden     |
| India - centraal       | India - zuid        |
| US - centraal          | US - oost 2          |
| Azië - oost           | Azië - zuidoost     |
| US - oost             | US - west            |
| US - oost 2           | US - centraal         |
| Frankrijk - centraal      | Frankrijk - zuid       |
| Frankrijk - zuid        | Frankrijk - centraal     |
| Japan - oost          | Japan - west         |
| Japan - west          | Japan - oost         |
| Korea - centraal       | Korea - zuid        |
| Korea - zuid         | Korea - centraal      |
| Europa - noord        | Europa -west        |
| US - noord-centraal    | US - zuid-centraal   |
| Zuid-Afrika - noord  | Zuid-Afrika - west  |
| Zuid-Afrika - west   | Zuid-Afrika - noord |
| US - zuid-centraal    | US - noord-centraal   |
| India - zuid         | India - centraal      |
| Azië - zuidoost      | Azië - oost          |
| Verenigd Koninkrijk Zuid            | Verenigd Koninkrijk West            |
| Verenigd Koninkrijk West             | Verenigd Koninkrijk Zuid           |
| VS (overheid) - Arizona      | VS (overheid) - Texas       |
| US Gov - Iowa         | VS (overheid) - Virginia    |
| VS (overheid) - Virginia      | VS (overheid) - Texas       |
| Europa -west         | Europa - noord       |
| US - west-centraal     | US - west 2          |
| US - west             | US - oost            |
| US - west 2           | US - west-centraal    |

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>Aanbevolen Azure File Sync machine configuratie

Azure File Sync computer vereisten worden bepaald door het aantal objecten in de naam ruimte en het verloop op de gegevensset. Eén server kan worden gekoppeld aan meerdere synchronisatie groepen en het aantal objecten dat wordt vermeld in de volgende tabel accounts voor de volledige naam ruimte waaraan een server is gekoppeld. Bijvoorbeeld server eindpunt A met 10.000.000 objecten + server eindpunt B met 10.000.000 objecten = 20.000.000 objecten. Voor de implementatie van een voor beeld raden we 8CPU, 16GiB aan geheugen toe voor stabiele status en (indien mogelijk) 48GiB van geheugen voor de eerste migratie.
 
Naam ruimte gegevens worden in het geheugen opgeslagen om prestatie redenen. Als gevolg hiervan hebben grotere naam ruimten meer geheugen nodig om goede prestaties te behouden, en is er meer CPU vereist voor het uitvoeren van het verloop. 
 
In de volgende tabel hebben we zowel de grootte van de naam ruimte als de conversie van de capaciteit voor veelvoorkomende bestands shares voor algemene doel einden, waarbij de gemiddelde grootte van het bestand 512KiB is. Als de bestands grootten kleiner zijn, kunt u overwegen extra geheugen toe te voegen voor dezelfde hoeveelheid capaciteit. Baseer uw geheugen configuratie op de grootte van de naam ruimte.

| Grootte van de naam ruimte-bestanden & directory's (miljoenen)  | Typische capaciteit (TiB)  | CPU-kernen  | Aanbevolen geheugen (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (initiële synchronisatie)/2 (standaard verloop)      |
| 5        | 2.4     | 2        | 16 (initiële synchronisatie)/4 (standaard verloop)    |
| 10       | 4.8     | 4        | 32 (initiële synchronisatie)/8 (standaard verloop)   |
| 30       | 14,3    | 8        | 48 (initiële synchronisatie)/16 (typische verloop)   |
| 50       | 23,8    | 16       | 64 (initiële synchronisatie)/32 (standaard verloop)  |
| 100 *     | 47,7   | 32       | 128 (initiële synchronisatie)/32 (standaard verloop)  |

\*meer dan 100.000.000 bestanden & directory's niet zijn getest. Dit is een zachte limiet.

> [!TIP]
> De initiële synchronisatie van een naam ruimte is een intensieve bewerking. het is raadzaam meer geheugen toe te wijzen totdat de initiële synchronisatie is voltooid. Dit is niet vereist, maar kan de initiële synchronisatie versnellen. 
> 
> Normaal verloop is 0,5% van de naam ruimte gewijzigd per dag. Overweeg meer CPU toe te voegen voor een hogere mate van verloop. 

## <a name="next-steps"></a>Volgende stappen
* [Firewall-en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
* [Implementatie van Azure Files plannen](storage-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
* [Azure File Sync bewaken](storage-sync-files-monitoring.md)
