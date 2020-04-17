---
title: Planning voor een Azure File Sync-implementatie | Microsoft Documenten
description: Lees waar u rekening mee moet houden bij het plannen van een Azure-bestandenimplementatie.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8666f51b88d2a70a2cb27e3606f24010771c8017
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460699"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planning voor de implementatie van Azure Files Sync

:::row:::
    :::column:::
        [![Interview en demo invoering van Azure File Sync - klik om af te spelen!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync is een service waarmee u een aantal Azure-bestandsshares opslaan op een on-premises Windows Server of cloud-vm. 
        
        In dit artikel wordt u kennis laten maken met azure file sync-concepten en -functies. Zodra u bekend bent met Azure File Sync, u overwegen de [implementatiehandleiding](storage-sync-files-deployment-guide.md) voor Azure File Sync te volgen om deze service uit te proberen.        
    :::column-end:::
:::row-end:::

De bestanden worden opgeslagen in de cloud in [Azure-bestandsshares.](storage-files-introduction.md) Azure-bestandsshares kunnen op twee manieren worden gebruikt: door deze serverloze Azure-bestandsshares (SMB) rechtstreeks te monteren of door Azure-bestandsshares on-premises te plaatsen met Azure File Sync. Welke implementatieoptie u kiest, verandert de aspecten waarmee u rekening moet houden bij het plannen van uw implementatie. 

- **Directe bevestiging van een Azure-bestandsshare:** Aangezien Azure Files SMB-toegang biedt, u Azure-bestandsshares on-premises of in de cloud monteren met behulp van de standaard SMB-client die beschikbaar is in Windows, macOS en Linux. Omdat Azure-bestandsshares serverloos zijn, hoeft u geen bestandsserver of NAS-apparaat te beheren voor productiescenario's. Dit betekent dat u geen softwarepatches hoeft toe te passen of fysieke schijven hoeft uit te wisselen. 

- **On-premises cache Azure-bestandsshare met Azure File Sync:** met Azure File Sync u de bestandsshares van uw organisatie centraliseren in Azure-bestanden, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijft. Azure File Sync transformeert een on-premises (of cloud) Windows Server in een snelle cache van uw Azure-bestandsshare. 

## <a name="management-concepts"></a>Managementconcepten
Een Azure File Sync-implementatie heeft drie fundamentele beheerobjecten:

- **Azure-bestandsshare**: Een Azure-bestandsshare is een serverloos delen van cloudbestanden, die het *cloudeindpunt* van een synchronisatierelatie voor Azure File Sync biedt. Bestanden in een Azure-bestandsshare kunnen rechtstreeks worden geopend met SMB of het FileREST-protocol, hoewel we u aanmoedigen om de bestanden voornamelijk toegang te krijgen via de Windows Server-cache wanneer het Azure-bestandsshare wordt gebruikt met Azure File Sync. Dit komt omdat Azure Files vandaag geen efficiënt wijzigingsdetectiemechanisme heeft zoals Windows Server heeft, dus wijzigingen in het Azure-bestandsaandeel nemen direct tijd in beslag om terug te komen naar de eindpunten van de server.
- **Servereindpunt:** het pad op de Windows Server dat wordt gesynchroniseerd met een Azure-bestandsshare. Dit kan een specifieke map op een volume of de wortel van het volume zijn. Er kunnen meerdere servereindpunten op hetzelfde volume bestaan als hun naamruimten elkaar niet overlappen.
- **Synchronisatiegroep:** het object dat de synchronisatierelatie definieert tussen een eindpunt in de **cloud**of Azure-bestandsshare en een servereindpunt. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Als u bijvoorbeeld twee verschillende sets bestanden hebt die u wilt beheren met Azure File Sync, maakt u twee synchronisatiegroepen en voegt u verschillende eindpunten toe aan elke synchronisatiegroep.

### <a name="azure-file-share-management-concepts"></a>Concepten voor beheer van Azure-bestandsshare
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync-beheerconcepten
Synchronisatiegroepen worden geïmplementeerd in **Storage Sync Services,** objecten op het hoogste niveau die servers registreren voor gebruik met Azure File Sync en de synchronisatiegroeprelaties bevatten. De Storage Sync Service-bron is een peer van de opslagaccountbron en kan op dezelfde manier worden geïmplementeerd in Azure-brongroepen. Een Storage Sync Service kan synchronisatiegroepen maken die Azure-bestandsshares bevatten voor meerdere opslagaccounts en meerdere geregistreerde Windows-servers.

Voordat u een synchronisatiegroep maken in een Opslagsynchronisatieservice, moet u eerst een Windows Server registreren bij de Opslagsynchronisatieservice. Hiermee wordt een **geregistreerd serverobject** gemaakt dat een vertrouwensrelatie vertegenwoordigt tussen uw server of cluster en de opslagsynchronisatieservice. Als u een Opslagsynchronisatieservice wilt registreren, moet u eerst de Azure File Sync-agent op de server installeren. Een afzonderlijke server of cluster kan worden geregistreerd bij slechts één storage sync service tegelijk.

Een synchronisatiegroep bevat één eindpunt in de cloud of Azure-bestandsshare en ten minste één servereindpunt. Het eindpuntobject van de server bevat de instellingen die de **cloudcompatibiliteitsmogelijkheid** configureren, die de caching-mogelijkheid van Azure File Sync biedt. Om te synchroniseren met een Azure-bestandsshare, moet het opslagaccount met het Azure-bestandsaandeel zich in hetzelfde Azure-gebied bevinden als de Opslagsynchronisatieservice.

### <a name="management-guidance"></a>Managementbegeleiding
Bij het implementeren van Azure File Sync raden we u aan:

- Het implementeren van Azure-bestandsshares 1:1 met Windows-bestandsshares. Het object met het eindpunt van de server biedt u een grote mate van flexibiliteit over hoe u de synchronisatietopologie instelt aan de serverzijde van de synchronisatierelatie. Als u het beheer wilt vereenvoudigen, moet u het pad van het servereindpunt overeenkomen met het pad van de Windows-bestandsshare. 

- Gebruik zo min mogelijk Storage Sync Services. Dit vereenvoudigt het beheer wanneer u synchronisatiegroepen hebt die meerdere servereindpunten bevatten, omdat een Windows Server slechts op één opslagsynchronisatieservice tegelijk kan worden geregistreerd. 

- Let op de IOPS-beperkingen van een opslagaccount bij het implementeren van Azure-bestandsshares. Idealiter zou u bestandsshares 1:1 toewijzen aan opslagaccounts, maar dit is mogelijk niet altijd mogelijk vanwege verschillende beperkingen en beperkingen, zowel van uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestandsshare in één opslagaccount te laten implementeren, moet u overwegen welke aandelen zeer actief zullen zijn en welke aandelen minder actief zullen zijn om ervoor te zorgen dat de heetste bestandsshares niet samen in dezelfde opslagaccount worden geplaatst.

## <a name="windows-file-server-considerations"></a>Overwegingen van Windows-bestandsserver
Als u de synchronisatiemogelijkheid op Windows Server wilt inschakelen, moet u de downloadbare azure-bestandsynchronisatie-agent installeren. De Azure File Sync-agent `FileSyncSvc.exe`biedt twee hoofdcomponenten: , de Windows-achtergrondservice die verantwoordelijk is `StorageSync.sys`voor het bewaken van wijzigingen op de servereindpunten en het initiëren van synchronisatiesessies, en een bestandssysteemfilter waarmee cloudtiering en snel herstel van rampen mogelijk zijn.  

### <a name="operating-system-requirements"></a>Vereisten voor het besturingssysteem
Azure File Sync wordt ondersteund met de volgende versies van Windows Server:

| Versie | Ondersteunde SKU's | Ondersteunde implementatieopties |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standaard en IoT | Volledig en kern |
| Windows Server 2016 | Datacenter-, standaard- en opslagserver | Volledig en kern |
| Windows Server 2012 R2 | Datacenter-, standaard- en opslagserver | Volledig en kern |

Toekomstige versies van Windows Server worden toegevoegd zodra ze worden uitgebracht.

> [!Important]  
> We raden u aan alle servers die u met Azure File Sync gebruikt up-to-date te houden met de nieuwste updates van Windows Update. 

### <a name="minimum-system-resources"></a>Minimale systeembronnen
Azure File Sync vereist een server, fysiek of virtueel, met ten minste één CPU en minimaal 2 GiB geheugen.

> [!Important]  
> Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de VM worden geconfigureerd met een minimum van 2048 MiB geheugen.

Voor de meeste productieworkloads raden we u aan een Azure File Sync Sync-server te configureren met alleen de minimale vereisten. Zie [Aanbevolen systeembronnen](#recommended-system-resources) voor meer informatie.

### <a name="recommended-system-resources"></a>Aanbevolen systeembronnen
Net als elke serverfunctie of -toepassing worden de systeembronvereisten voor Azure File Sync bepaald door de schaal van de implementatie; grotere implementaties op een server vereisen meer systeembronnen. Voor Azure File Sync wordt de schaal bepaald door het aantal objecten op de servereindpunten en de verloop op de gegevensset. Met één server kunnen servereindpunten in meerdere synchronisatiegroepen worden weergegeven en het aantal objecten dat in de volgende tabel wordt vermeld, wordt geboekt voor de volledige naamruimte waaraan een server is gekoppeld. 

Servereindpunt A met 10 miljoen objecten + servereindpunt B met 10 miljoen objecten = 20 miljoen objecten. Voor dat voorbeeld implementatie, raden we 8 CPU's, 16 GiB van het geheugen voor steady state, en (indien mogelijk) 48 GiB van het geheugen voor de eerste migratie.
 
Namespace-gegevens worden om prestatieredenen opgeslagen in het geheugen. Daarom vereisen grotere naamruimten meer geheugen om goede prestaties te behouden, en meer churn vereist meer CPU om te verwerken. 
 
In de volgende tabel hebben we zowel de grootte van de naamruimte als een conversie naar capaciteit voor typische bestandsshares voor algemeen gebruik, waarbij de gemiddelde bestandsgrootte 512 KiB is, opgegeven. Als de bestandsgrootte kleiner is, u overwegen extra geheugen toe te voegen voor dezelfde hoeveelheid capaciteit. Baseer uw geheugenconfiguratie op de grootte van de naamruimte.

| Grootte van de naamruimte - bestanden & mappen (miljoenen)  | Typische capaciteit (TiB)  | CPU-cores  | Aanbevolen geheugen (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (eerste synchronisatie)/ 2 (typische churn)      |
| 5        | 2.3     | 2        | 16 (eerste synchronisatie)/ 4 (typische churn)    |
| 10       | 4.7     | 4        | 32 (eerste synchronisatie)/ 8 (typische churn)   |
| 30       | 14.0    | 8        | 48 (eerste synchronisatie)/ 16 (typische churn)   |
| 50       | 23.3    | 16       | 64 (eerste synchronisatie)/ 32 (typische churn)  |
| 100*     | 46.6    | 32       | 128 (eerste synchronisatie)/ 32 (typische churn)  |

\*Het synchroniseren van meer dan 100 miljoen bestanden & mappen wordt op dit moment niet aanbevolen. Dit is een zachte limiet op basis van onze geteste drempels. Zie [schaalbaarheids- en prestatiedoelen voor Azure Files](storage-files-scale-targets.md#azure-file-sync-scale-targets)voor meer informatie.

> [!TIP]
> De eerste synchronisatie van een naamruimte is een intensieve bewerking en we raden u aan meer geheugen toe te wijzen totdat de eerste synchronisatie is voltooid. Dit is niet vereist, maar kan de initiële synchronisatie versnellen. 
> 
> Typische churn is 0,5% van de naamruimte die per dag verandert. Voor hogere niveaus van churn, overwegen het toevoegen van meer CPU. 

- Een lokaal gekoppeld volume dat is opgemaakt met het NTFS-bestandssysteem.

### <a name="evaluation-cmdlet"></a>Evaluatie cmdlet
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van de cmdlet azure file sync. Deze cmdlet controleert op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde versie van het besturingssysteem. De controles hebben betrekking op de meeste, maar niet alle van de onderstaande kenmerken; We raden u aan de rest van deze sectie zorgvuldig door te lezen om ervoor te zorgen dat uw implementatie soepel verloopt. 

De evaluatiecmdlet kan worden geïnstalleerd door de Az PowerShell-module te installeren, die kan worden geïnstalleerd door de instructies hier te [volgen: Azure PowerShell installeren en configureren.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

#### <a name="usage"></a>Gebruik  
U de evaluatietool op een aantal verschillende manieren aanroepen: u de systeemcontroles, de gegevenssetcontroles of beide uitvoeren. Ga als u zowel de systeem- als de gegevenssetcontroles uitvoert: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Ga als u alleen op uw gegevensset wilt testen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Ga alleen als het gaat om het testen van systeemvereisten:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Ga als resultaat over in CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Compatibiliteit van bestandssystemen
Azure File Sync wordt alleen ondersteund op direct gekoppelde NTFS-volumes. Direct verbonden opslag, of DAS, op Windows Server betekent dat het Windows Server-besturingssysteem eigenaar is van het bestandssysteem. DAS kan worden geleverd door schijven fysiek aan de bestandsserver te koppelen, virtuele schijven aan een vm van bestandsserver (zoals een VM gehost door Hyper-V) of zelfs via ISCSI te koppelen.

Alleen NTFS-volumes worden ondersteund; ReFS, FAT, FAT32 en andere bestandssystemen worden niet ondersteund.

In de volgende tabel ziet u de interopstatus van de functies van het NTFS-bestandssysteem: 

| Functie | Ondersteuningsstatus | Opmerkingen |
|---------|----------------|-------|
| ACL’s (toegangsbeheerlijsten) | Volledig ondersteund | Lijsten met discretionaire toegangsbeheer in Windows-stijl worden behouden door Azure File Sync en worden afgedwongen door Windows Server op servereindpunten. ACL's kunnen ook worden afgedwongen bij het rechtstreeks monteren van het Azure-bestandsaandeel, maar dit vereist extra configuratie. Zie de [sectie Identiteit](#identity) voor meer informatie. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Bevestigingspunten | Gedeeltelijk ondersteund | Bevestigingspunten kunnen de hoofdmap van een servereindpunt zijn, maar ze worden overgeslagen als ze zich in de naamruimte van een servereindpunt bevinden. |
| Kruispunten | Overgeslagen | Bijvoorbeeld Distributed File System DfrsrPrivate en DFSRoots mappen. |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Schaarse bestanden | Volledig ondersteund | Schaarse bestanden worden gesynchroniseerd (worden niet geblokkeerd), maar ze worden gesynchroniseerd met de cloud als een volledig bestand. Als de bestandsinhoud in de cloud (of op een andere server) verandert, is het bestand niet langer schaars wanneer de wijziging wordt gedownload. |
| Alternatieve gegevensstromen (ADS) | Behouden, maar niet gesynchroniseerd | Classificatietags die zijn gemaakt door de bestandsclassificatie-infrastructuur worden bijvoorbeeld niet gesynchroniseerd. Bestaande classificatietags op bestanden op elk van de servereindpunten blijven onaangeroerd. |

<a id="files-skipped"></a>Azure File Sync slaat ook bepaalde tijdelijke bestanden en systeemmappen over:

| Bestand/map | Opmerking |
|-|-|
| pagefile.sys | Bestand specifiek voor het systeem |
| Desktop.ini | Bestand specifiek voor het systeem |
| Duimschroef | Tijdelijk bestand voor miniaturen |
| ehthumbs.db | Tijdelijk bestand voor mediaminiaturen |
| ~$\*.\* | Office-tijdelijk bestand |
| \*.tmp | Tijdelijk bestand |
| \*.laccdb | Toegang tot DB-vergrendelingsbestand|
| 635D02A91C401B97884B82B3BCDAEA.* | Bestand interne synchronisatie|
| \\Informatie over systeemvolume | Map specifiek voor volume |
| $RECYCLE. Bin| Map |
| \\SyncShareState | Map voor synchronisatie |

### <a name="failover-clustering"></a>Failoverclustering
Windows Server Failover Clustering wordt ondersteund door Azure File Sync voor de implementatieoptie 'Bestandsserver voor algemeen gebruik'. Failoverclustering wordt niet ondersteund op "Scale-Out File Server for application data" (SOFS) of op Geclusterde gedeelde volumes (CSVs).

> [!Note]  
> De Azure File Sync-agent moet op elk knooppunt in een failovercluster zijn geïnstalleerd om de synchronisatie correct te laten werken.

### <a name="data-deduplication"></a>Gegevensontdubbeling
**Windows Server 2016 en Windows Server 2019**   
Gegevensontdubbeling wordt ondersteund op volumes waarbij cloudtiering is ingeschakeld op Windows Server 2016 en Windows Server 2019. Als u gegevensdeduplicatie inschakelt op een volume met cloudtiering ingeschakeld, u meer bestanden on-premises opslaan zonder meer opslagruimte in te richten. 

Wanneer Gegevensdedup is ingeschakeld op een volume waarbij cloudtiering is ingeschakeld, worden de geoptimaliseerde bestanden van Dedup binnen de locatie voor servereindpunten gelaagd weergegeven, vergelijkbaar met een normaal bestand op basis van de beleidsinstellingen voor cloudtiering. Zodra de voor Dedup geoptimaliseerde bestanden zijn gelaagd, wordt de taak voor het ophalen van gegevensdedupgarbageen automatisch uitgevoerd om schijfruimte terug te winnen door onnodige segmenten te verwijderen waarnaar niet langer wordt verwezen door andere bestanden op het volume.

Let op: de volumebesparing geldt alleen voor de server; uw gegevens in het Azure-bestandsaandeel worden niet verwijderd.

> [!Note]  
> Om gegevensdeduplicatie op volumes te ondersteunen waarbij cloudtiering is ingeschakeld op Windows Server 2019, moet Windows-update [KB4520062](https://support.microsoft.com/help/4520062) zijn geïnstalleerd en is Azure File Sync-agentversie 9.0.0.0 of nieuwer vereist.

**Windows Server 2012 R2**  
Azure File Sync biedt geen ondersteuning voor gegevensdeduplicatie en cloudlagen op hetzelfde volume op Windows Server 2012 R2. Als gegevensdeduplicatie is ingeschakeld op een volume, moet cloudlaaging worden uitgeschakeld. 

**Opmerkingen**
- Als gegevensdeduplicatie is geïnstalleerd voordat de Azure File Sync-agent wordt geïnstalleerd, is een herstart vereist om gegevensdeduplicatie en cloudlagen op hetzelfde volume te ondersteunen.
- Als Gegevensdeduplicatie is ingeschakeld op een volume nadat cloudtiering is ingeschakeld, optimaliseert de oorspronkelijke deduplicatieoptimalisatietaak bestanden op het volume dat nog niet is gelaagd en heeft de volgende impact op cloudtiering:
    - Het beleid voor vrije ruimte blijft bestanden tieren volgens de vrije ruimte op het volume met behulp van de heatmap.
    - Datumbeleid slaat het lagen van bestanden over die mogelijk anders in aanmerking kwamen voor tiering vanwege de taak voor de ontdubbelingsoptimalisatie die toegang krijgt tot de bestanden.
- Voor lopende deduplicatieoptimalisatietaken wordt cloudtiering met datumbeleid vertraagd door de instelling [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) voor gegevensdeuplicatie als het bestand nog niet is gelaagd. 
    - Voorbeeld: Als de instelling MinimumFileAgeDays zeven dagen is en het beleid voor de gelaagdheid in de cloud 30 dagen, worden bestanden na 37 dagen in het datumbeleid laag.
    - Opmerking: Zodra een bestand is gelaagd door Azure File Sync, slaat de optimalisatietaak de duplicatie het bestand over.
- Als een server met Windows Server 2012 R2 met de Azure File Sync-agent is geïnstalleerd, is geüpgraded naar Windows Server 2016 of Windows Server 2019, moeten de volgende stappen worden uitgevoerd om gegevensdeduplicatie en cloudlagen op hetzelfde volume te ondersteunen:  
    - Verwijder de Azure File Sync-agent voor Windows Server 2012 R2 en start de server opnieuw.
    - Download de Azure File Sync-agent voor de nieuwe versie van het serverbesturingssysteem (Windows Server 2016 of Windows Server 2019).
    - Installeer de Azure File Sync-agent en start de server opnieuw.  
    
    Opmerking: De configuratie-instellingen voor Azure File Sync op de server blijven behouden wanneer de agent is verwijderd en opnieuw is geïnstalleerd.

### <a name="distributed-file-system-dfs"></a>Gedistribueerd bestandssysteem (DFS)
Azure File Sync ondersteunt interop met DFS-naamruimten (DFS-N) en DFS-replicatie (DFS-R).

**DFS-naamruimten (DFS-N):** Azure File Sync wordt volledig ondersteund op DFS-N-servers. U de Azure File Sync-agent installeren op een of meer DFS-N-leden om gegevens te synchroniseren tussen de servereindpunten en het eindpunt van de cloud. Zie [overzicht van DFS-naamruimten](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)voor meer informatie.
 
**DFS-replicatie (DFS-R)**: Aangezien DFS-R en Azure File Sync beide replicatieoplossingen zijn, raden we in de meeste gevallen aan OM DFS-R te vervangen door Azure File Sync. Er zijn echter verschillende scenario's waarin u DFS-R en Azure File Sync samen wilt gebruiken:

- U migreert van een DFS-R-implementatie naar een Azure File Sync-implementatie. Zie [Een DFS-replicatieimplementatie (DFS-Replicatie) migreren naar Azure File Sync voor](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)meer informatie.
- Niet elke on-premises server die een kopie van uw bestandsgegevens nodig heeft, kan rechtstreeks met het internet worden verbonden.
- Branchservers consolideren gegevens op één hubserver, waarvoor u Azure File Sync wilt gebruiken.

Voor Azure File Sync en DFS-R moet naast elkaar werken:

1. Azure File Sync cloud tiering moet worden uitgeschakeld op volumes met DFS-R gerepliceerde mappen.
2. Servereindpunten mogen niet worden geconfigureerd in alleen-lezen replicatiemappen met alleen DFS-R.

Zie [DFS-replicatieoverzicht](https://technet.microsoft.com/library/jj127250)voor meer informatie.

### <a name="sysprep"></a>Sysprep
Het gebruik van sysprep op een server waarop de Azure File Sync-agent is geïnstalleerd, wordt niet ondersteund en kan tot onverwachte resultaten leiden. Agent installatie en server registratie moet plaatsvinden na het implementeren van de server afbeelding en het voltooien van sysprep mini-setup.

### <a name="windows-search"></a>Windows Search
Als cloudtiering is ingeschakeld op een servereindpunt, worden bestanden die trapsgewijs zijn overgeslagen en niet geïndexeerd door Windows Search. Niet-gelaagde bestanden worden correct geïndexeerd.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere HSM-oplossingen (Hierarchical Storage Management)
Er mogen geen andere HSM-oplossingen worden gebruikt met Azure File Sync.

## <a name="identity"></a>Identiteit
Azure File Sync werkt met uw standaard AD-gebaseerde identiteit zonder dat er geen speciale installatie is die verder gaat dan het instellen van synchronisatie. Wanneer u Azure File Sync gebruikt, is de algemene verwachting dat de meeste toegangen via de Azure File Sync-cachingservers gaan, in plaats van via het Azure-bestandsshare. Aangezien de servereindpunten zich op Windows Server bevinden en Windows Server ad- en Windows-achtige ACL's al heel lang ondersteunt, is er niets meer nodig om ervoor te zorgen dat de Windows-bestandsservers die zijn geregistreerd bij de Storage Sync Service, zijn samengevoegd. Azure File Sync slaat ACL's op in de bestanden in de Azure-bestandsshare en repliceert deze naar alle servereindpunten.

Hoewel het langer duurt voordat wijzigingen in de Azure-bestandsshare worden gesynchroniseerd met de servereindpunten in de synchronisatiegroep, u er ook voor zorgen dat u uw AD-machtigingen voor uw bestandsshare ook rechtstreeks in de cloud afdwingen. Om dit te doen, moet u het domein lid worden van uw opslagaccount met uw on-premises AD, net als de manier waarop uw Windows-bestandsservers zijn samengevoegd. Zie Azure [Files Active Directory-overzicht](storage-files-active-directory-overview.md)voor meer informatie over het samenvoegen van uw opslagaccount door een domein met een Active Directory van de klant.

> [!Important]  
> Domein dat lid wordt van uw opslagaccount met Active Directory is niet vereist om Azure File Sync te implementeren. Dit is een strikt optionele stap waarmee de Azure-bestandsshare on-premises ACL's kan afdwingen wanneer gebruikers de Azure-bestandsshare rechtstreeks monteren.

## <a name="networking"></a>Netwerken
De Azure File Sync-agent communiceert met uw Storage Sync Service en Azure-bestandsshare met het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS gebruiken via poort 443. SMB wordt nooit gebruikt voor het uploaden of downloaden van gegevens tussen uw Windows Server en het Azure-bestandsaandeel. Omdat de meeste organisaties HTTPS-verkeer toestaan via poort 443, als vereiste voor het bezoeken van de meeste websites, is speciale netwerkconfiguratie meestal niet vereist om Azure File Sync te implementeren.

Op basis van het beleid van uw organisatie of unieke wettelijke vereisten hebt u mogelijk restrictievere communicatie met Azure nodig en daarom biedt Azure File Sync verschillende mechanismen voor het configureren van netwerken. Op basis van uw wensen u:

- Tunnelsynchronisatie en bestandsupload-/downloadverkeer via uw ExpressRoute of Azure VPN. 
- Maak gebruik van Azure Files en Azure Networking-functies, zoals serviceeindpunten en privéeindpunten.
- Configureer Azure File Sync om uw proxy in uw omgeving te ondersteunen.
- Gasgeven voor netwerkactiviteit vanuit Azure File Sync.

Zie voor meer informatie over het configureren van de netwerkfunctionaliteit van Azure File Sync:
- [Azure File Sync-proxy en -firewallinstellingen](storage-sync-files-firewall-and-proxy.md)
- [Zorgen voor Azure File Sync is een goede buur in uw datacenter](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Versleuteling
Bij het gebruik van Azure File Sync zijn er drie verschillende versleutelingslagen om rekening mee te houden: versleuteling op de at-rest-opslag van Windows Server, versleuteling die wordt gebruikt tussen de Azure File Sync-agent en Azure en versleuteling in de rest van uw gegevens in het Azure-bestandsshare. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-versleuteling in rust 
Er zijn twee strategieën voor het versleutelen van gegevens op Windows Server die over het algemeen werken met Azure File Sync: versleuteling onder het bestandssysteem, zodat het bestandssysteem en alle gegevens die eraan zijn geschreven, is versleuteld en versleuteling binnen de bestandsindeling zelf. Deze methoden sluiten elkaar niet uit; ze kunnen samen worden gebruikt indien gewenst, omdat het doel van encryptie anders is.

Om versleuteling onder het bestandssysteem te bieden, biedt Windows Server BitLocker-inbox. BitLocker is volledig transparant voor Azure File Sync. De belangrijkste reden om een versleutelingsmechanisme zoals BitLocker te gebruiken, is om fysieke exfiltratie van gegevens uit uw on-premises datacenter te voorkomen door iemand die de schijven steelt en om te voorkomen dat een ongeautoriseerd besturingssysteem wordt omzeild om ongeautoriseerde lees-/schrijfbewerkingen naar uw gegevens uit te voeren. Zie [BitLocker-overzicht](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)voor meer informatie over BitLocker.

Producten van derden die op dezelfde manier werken als BitLocker, omdat ze onder het NTFS-volume zitten, moeten op dezelfde manier volledig transparant werken met Azure File Sync. 

De andere belangrijke methode voor het versleutelen van gegevens is het versleutelen van de gegevensstroom van het bestand wanneer de toepassing het bestand opslaat. Sommige toepassingen kunnen dit native doen, maar dit is meestal niet het geval. Een voorbeeld van een methode voor het versleutelen van de gegevensstroom van het bestand is Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. De belangrijkste reden om een versleutelingsmechanisme zoals AIP/RMS te gebruiken, is om te voorkomen dat gegevens uit uw bestandsshare worden verwijderd door mensen die deze naar alternatieve locaties kopiëren, zoals naar een flashstation of deze te e-mailen naar een onbevoegde persoon. Wanneer de gegevensstroom van een bestand wordt versleuteld als onderdeel van de bestandsindeling, blijft dit bestand worden versleuteld op het Azure-bestandsaandeel. 

Azure File Sync werkt niet samen met NTFS Encrypted File System (NTFS EFS) of versleutelingsoplossingen van derden die boven het bestandssysteem zitten, maar onder de gegevensstroom van het bestand. 

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Azure File Sync-agent communiceert met uw Storage Sync Service en Azure-bestandsshare met het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS gebruiken via poort 443. Azure File Sync verzendt geen onversleutelde aanvragen via HTTP. 

Azure-opslagaccounts bevatten een switch voor het vereisen van versleuteling tijdens het transport, die standaard is ingeschakeld. Zelfs als de switch op opslagaccountniveau is uitgeschakeld, wat betekent dat onversleutelde verbindingen met uw Azure-bestandsshares mogelijk zijn, gebruikt Azure File Sync nog steeds alleen versleutelde kanalen om toegang te krijgen tot uw bestandsshare.

De belangrijkste reden om versleuteling tijdens het transport voor het opslagaccount uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of oudere Linux-distributie, en rechtstreeks met een Azure-bestandsshare praten. Als de verouderde toepassing wordt gesproken met de Windows Server-cache van het bestandsshare, heeft het omdraaien van deze instelling geen effect. 

We raden u ten zeerste aan ervoor te zorgen dat versleuteling van gegevens tijdens het transport is ingeschakeld.

Zie [beveiligde overdracht in Azure-opslag vereisen](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie over versleuteling tijdens het transport.

### <a name="azure-file-share-encryption-at-rest"></a>Versleuteling van Azure-bestandsshare in rust
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standaardbestandsshares inschakelen tot 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Beschikbaarheid van Azure-bestandssynchronisatieregio's
Azure File Sync is beschikbaar in de volgende regio's:

| Azure-cloud | Geografische regio | Azure-regio | Regiocode |
|-------------|-------------------|--------------|-------------|
| Public | Azië | Azië - oost | `eastasia` |
| Public | Azië | Azië - zuidoost | `southeastasia` |
| Public | Australië | Australië - oost | `australiaeast` |
| Public | Australië | Australië - zuidoost | `australiasoutheast` |
| Public | Brazilië | Brazilië - zuid | `brazilsouth` |
| Public | Canada | Canada - midden | `canadacentral` |
| Public | Canada | Canada - oost | `canadaeast` |
| Public | Europa | Europa - noord | `northeurope` |
| Public | Europa | Europa -west | `westeurope` |
| Public | Frankrijk | Frankrijk - centraal | `francecentral` |
| Public | Frankrijk | Frankrijk Zuid* | `francesouth` |
| Public | India | India - centraal | `centralindia` |
| Public | India | India - zuid | `southindia` |
| Public | Japan | Japan - oost | `japaneast` |
| Public | Japan | Japan - west | `japanwest` |
| Public | Korea | Korea - centraal | `koreacentral` |
| Public | Korea | Korea - zuid | `koreasouth` |
| Public | Zuid-Afrika | Zuid-Afrika Noord | `southafricanorth` |
| Public | Zuid-Afrika | Zuid-Afrika West* | `southafricawest` |
| Public | VAE | VAE Centraal* | `uaecentral` |
| Public | VAE | VAE Noord | `uaenorth` |
| Public | VK | Verenigd Koninkrijk Zuid | `uksouth` |
| Public | VK | Verenigd Koninkrijk West | `ukwest` |
| Public | VS | VS - centraal | `centralus` |
| Public | VS | VS - oost | `eastus` |
| Public | VS | VS - oost 2 | `eastus2` |
| Public | VS | VS - noord-centraal | `northcentralus` |
| Public | VS | VS - zuid-centraal | `southcentralus` |
| Public | VS | VS - west-centraal | `westcentralus` |
| Public | VS | VS - west | `westus` |
| Public | VS | VS - west 2 | `westus2` |
| US Gov | VS | VS (overheid) - Arizona | `usgovarizona` |
| US Gov | VS | VS (overheid) - Texas | `usgovtexas` |
| US Gov | VS | VS (overheid) - Virginia | `usgovvirginia` |

Azure File Sync ondersteunt alleen synchronisatie met een Azure-bestandsshare dat zich in dezelfde regio bevindt als de Opslagsynchronisatieservice.

Voor de regio's die zijn gemarkeerd met sterretjes, moet u contact opnemen met Azure Support om toegang tot Azure Storage in die regio's aan te vragen. Het proces wordt in [dit document](https://azure.microsoft.com/global-infrastructure/geographies/)beschreven.

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Georedundante en Geo-zone redundante opslag hebben de mogelijkheid om handmatig failover opslag naar de secundaire regio. We raden u aan dit niet buiten een ramp te doen wanneer u Azure File Sync gebruikt vanwege de toegenomen kans op gegevensverlies. In het geval van een ramp waarbij u een handmatige failover van opslag wilt starten, moet u een ondersteuningsaanvraag openen met Microsoft om Azure File Sync te laten synchroniseren met het secundaire eindpunt.

## <a name="migration"></a>Migratie
Als u een bestaande Windows-bestandsserver hebt, kan Azure File Sync direct op zijn plaats worden geïnstalleerd, zonder dat gegevens naar een nieuwe server hoeven te worden verplaatst. Als u van plan bent te migreren naar een nieuwe Windows-bestandsserver als onderdeel van het aannemen van Azure File Sync, zijn er verschillende mogelijke benaderingen om gegevens over te zetten:

- Maak servereindpunten voor uw oude bestandsshare en uw nieuwe bestandsshare en laat Azure File Sync de gegevens synchroniseren tussen de servereindpunten. Het voordeel van deze aanpak is dat het heel gemakkelijk is om de opslag op uw nieuwe bestandsserver te overschrijven, omdat Azure File Sync cloudtiering op de hoogte is. Wanneer u er klaar voor bent, u eindgebruikers overknippen naar de bestandsshare op de nieuwe server en het servereindpunt van de oude bestandsshare verwijderen.

- Maak alleen een servereindpunt op de nieuwe bestandsserver en kopieer `robocopy`gegevens naar de oude bestandsshare met behulp van . Afhankelijk van de topologie van bestandsshares op uw nieuwe server (hoeveel aandelen u op elk volume hebt, hoe gratis elk volume `robocopy` is, enz.), moet u mogelijk tijdelijk extra opslagruimte inrichten, omdat de verwachting is dat van uw oude server naar uw nieuwe server binnen uw on-premises datacenter sneller wordt voltooid dan Azure File Sync gegevens naar Azure zal verplaatsen.

Het is ook mogelijk om Data Box te gebruiken om gegevens te migreren naar een Azure File Sync-implementatie. Wanneer klanten gegevensbox willen gebruiken om gegevens in te nemen, doen ze dat meestal omdat ze denken dat het de snelheid van hun implementatie zal verhogen of omdat het zal helpen bij scenario's met beperkte bandbreedte. Hoewel het waar is dat het gebruik van een gegevensvak om gegevens in uw Azure File Sync-implementatie in te nemen het bandbreedtegebruik vermindert, zal het waarschijnlijk sneller zijn voor de meeste scenario's om een online gegevensupload na te streven via een van de hierboven beschreven methoden. Zie Gegevens synchroniseren naar [Azure File Sync met Azure Data Box](storage-sync-offline-data-transfer.md)voor meer informatie over het gebruik van gegevensvak om gegevens in uw Azure File Sync-implementatie in te nemen.

Een veel voorkomende fout die klanten maken bij het migreren van gegevens naar hun nieuwe Azure File Sync-implementatie is om gegevens rechtstreeks naar het Azure-bestandsaandeel te kopiëren, in plaats van op hun Windows-bestandsservers. Hoewel Azure File Sync alle nieuwe bestanden op het Azure-bestandsaandeel identificeert en ze weer synchroniseert met uw Windows-bestandsshares, is dit over het algemeen aanzienlijk langzamer dan het laden van gegevens via de Windows-bestandsserver. Veel Azure-kopieerhulpprogramma's, zoals AzCopy, hebben het extra nadeel dat het niet alle belangrijke metagegevens van een bestand, zoals tijdstempels en ACL's, worden gekopieerd.

## <a name="antivirus"></a>Antivirus
Omdat antivirus werkt door bestanden te scannen op bekende kwaadaardige code, kan een antivirusproduct leiden tot het terugroepen van gelaagde bestanden. In versie 4.0 en hoger van de Azure File Sync-agent hebben gelaagde bestanden het beveiligde Windows-kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ingesteld. We raden u aan om overleg te plegen met uw softwareleverancier om te leren hoe u de oplossing configureren om leesbestanden over te slaan met deze attribuutset (velen doen dit automatisch). 

De interne antivirusoplossingen van Microsoft, Windows Defender en System Center Endpoint Protection (SCEP), slaan beide automatisch leesbestanden over waarop dit kenmerk is ingesteld. We hebben ze getest en een klein probleem geïdentificeerd: wanneer u een server toevoegt aan een bestaande synchronisatiegroep, worden bestanden kleiner dan 800 bytes teruggeroepen (gedownload) op de nieuwe server. Deze bestanden blijven op de nieuwe server staan en worden niet trapsgewijs weergegeven omdat ze niet voldoen aan de vereiste gelaagdheidsgrootte (>64kb).

> [!Note]  
> Antivirusleveranciers kunnen de compatibiliteit tussen hun product en Azure File Sync controleren met behulp van de [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), die beschikbaar is om te downloaden in het Microsoft Download centrum.

## <a name="backup"></a>Backup 
Net als antivirusoplossingen kunnen back-upoplossingen leiden tot het terugroepen van gelaagde bestanden. We raden u aan een back-upoplossing voor de cloud te gebruiken om een back-up te maken van het Azure-bestandsaandeel in plaats van een on-premises back-upproduct.

Als u een on-premises back-upoplossing gebruikt, moeten back-ups worden uitgevoerd op een server in de synchronisatiegroep waarin cloudtiering is uitgeschakeld. Wanneer u een herstel uitvoert, gebruikt u de opties voor herstel op volumeniveau of bestandsniveau. Bestanden die zijn hersteld met de optie herstel op bestandsniveau worden gesynchroniseerd met alle eindpunten in de synchronisatiegroep en bestaande bestanden worden vervangen door de versie die is hersteld van back-up.  Herstelop volumeniveau vervangt geen nieuwere bestandsversies in de Azure-bestandsshare of andere servereindpunten.

> [!Note]  
> Bare-metal (BMR) herstellen kan onverwachte resultaten veroorzaken en wordt momenteel niet ondersteund.

> [!Note]  
> Met versie 9 van de Azure File Sync-agent worden VSS-momentopnamen (inclusief tabblad Vorige versies) nu ondersteund op volumes waarvoor cloudlagen zijn ingeschakeld. U moet echter de compatibiliteit van de vorige versie inschakelen via PowerShell. [Meer informatie](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Volgende stappen
* [Firewall- en proxy-instellingen overwegen](storage-sync-files-firewall-and-proxy.md)
* [Planning voor de implementatie van Azure Files](storage-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
* [Azure File Sync bewaken](storage-sync-files-monitoring.md)