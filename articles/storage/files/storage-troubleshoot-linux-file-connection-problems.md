---
title: Problemen met Azure-bestanden in Linux oplossen | Microsoft Documenten
description: Azure Files-problemen oplossen in Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159486"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Problemen met Azure Files in Linux oplossen

In dit artikel worden veelvoorkomende problemen weergegeven die verband houden met Azure Files wanneer u verbinding maakt met Linux-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. 

Naast de stappen voor het oplossen van problemen in dit artikel, u [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) gebruiken om ervoor te zorgen dat de Linux-client de juiste vereisten heeft. AzFileDiagnostics automatiseert de detectie van de meeste symptomen die in dit artikel worden genoemd. Het helpt bij het opzetten van uw omgeving om optimale prestaties te krijgen. U deze informatie ook vinden in de [probleemoplosser voor azure files.](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) De probleemoplosser biedt stappen om u te helpen problemen te maken met het verbinden, toewijzen en monteren van Azure Files-shares.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Kan geen verbinding maken met of een Azure-bestandsshare monteren

### <a name="cause"></a>Oorzaak

Veelvoorkomende oorzaken voor dit probleem zijn:

- U gebruikt een incompatibele Linux-distributieclient. We raden u aan de volgende Linux-distributies te gebruiken om verbinding te maken met een Azure-bestandsshare:

|   | SMB 2.1 <br>(Monteert vm's binnen hetzelfde Azure-gebied) | SMB 3.0 <br>(Mounts van on-premises en cross-region) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ |   |
| openSUSE | 13,2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- CIFS-hulpprogramma's (cifs-utils) worden niet op de client geïnstalleerd.
- De minimale SMB/CIFS-versie, 2.1, is niet geïnstalleerd op de client.
- SMB 3.0-versleuteling wordt niet ondersteund op de client. De voorgaande tabel bevat een lijst met Linux-distributies die montage ondersteunen vanuit on-premises en cross-region met behulp van encryptie. Voor andere distributies zijn kernel 4.11 en hogere versies vereist.
- U probeert verbinding te maken met een opslagaccount via TCP-poort 445, die niet wordt ondersteund.
- U probeert verbinding te maken met een Azure-bestandsshare van een Azure-vm en de VM bevindt zich niet in hetzelfde gebied als het opslagaccount.
- Als de vereiste instelling [Voor beveiligde overdracht]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld op het opslagaccount, staan Azure Files alleen verbindingen toe die SMB 3.0 met versleuteling gebruiken.

### <a name="solution"></a>Oplossing

Als u het probleem wilt oplossen, gebruikt u het [hulpprogramma voor het oplossen van problemen voor azure-bestanden die fouten op Linux maken.](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) Deze tool:

* Helpt u bij het valideren van de client running omgeving.
* Detecteert de incompatibele clientconfiguratie die toegangsfouten voor Azure Files zou veroorzaken.
* Geeft prescriptieve richtlijnen voor zelffixatie.
* Verzamelt de diagnostische sporen.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>'Fout monteren(13): Geweigerde toestemming' bij het monteren van een Azure-bestandsshare

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: Ongecodeerd communicatiekanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Zie [Vereisten voor het koppelen van een Azure-bestandsshare met Linux en het cifs-utils-pakket](storage-how-to-use-files-linux.md#prerequisites) voor meer informatie. 

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Maak verbinding vanaf een client die SMB-versleuteling ondersteunt of verbinding maakt vanaf een virtuele machine in hetzelfde datacenter als het Azure-opslagaccount dat wordt gebruikt voor het Azure-bestandsshare.
2. Controleer of de vereiste instelling [Secure transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is uitgeschakeld op het opslagaccount als de client geen SMB-versleuteling ondersteunt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: Virtuele netwerk- of firewallregels zijn ingeschakeld op het opslagaccount 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security) voor meer informatie.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[geweigerde machtiging] Schijfquotum overschreden" wanneer u een bestand probeert te openen

In Linux ontvangt u een foutmelding die lijkt op het volgende:

**\<bestandsnaam> [machtiging geweigerd] Schijfquotum overschreden**

### <a name="cause"></a>Oorzaak

U hebt de bovengrens van gelijktijdige open handgrepen bereikt die zijn toegestaan voor een bestand.

Er is een quotum van 2.000 open handgrepen op één bestand. Wanneer u 2.000 geopende handgrepen hebt, wordt er een foutbericht weergegeven waarin staat dat het quotum is bereikt.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige open handgrepen door sommige handgrepen te sluiten en probeer de bewerking opnieuw.

Als u geopende handgrepen voor een bestandsshare, map of bestand wilt weergeven, gebruikt u de PowerShell-cmdlet [Get-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle)  

Als u open handgrepen voor een bestandsshare, map of bestand wilt sluiten, gebruikt u de PowerShell-cmdlet [Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in Az PowerShell-moduleversie 2.4 of hoger. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u de nieuwste Az PowerShell-module wilt installeren.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Langzaam kopiëren van bestanden van en naar Azure-bestanden in Linux

- Als u geen specifieke i/o-groottevereiste hebt, raden we u aan 1 MiB als I/O-maat te gebruiken voor optimale prestaties.
- Gebruik de juiste kopieermethode:
    - Gebruik [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor elke overdracht tussen twee bestandsshares.
    - Met behulp van cp of dd met parallel kan de kopieersnelheid te verbeteren, het aantal threads is afhankelijk van uw use case en werklast. In de volgende voorbeelden worden zes voorbeelden gebruikt: 
    - cp-voorbeeld (cp gebruikt de standaardblokgrootte van het `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`bestandssysteem als de grootte van de chunk): .
    - dd-voorbeeld (met deze opdracht wordt de grootte van de segment expliciet ingesteld op 1 MiB):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Open source tools van derden zoals:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Sorteert bestanden en verpakt ze in partities.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Gebruikt Fpart en een kopieertool om meerdere exemplaren te spawnen om gegevens van src_dir naar dst_url te migreren.
        - [Multi](https://github.com/pkolano/mutil) - Multi-threaded cp en md5sum op basis van GNU coreutils.
- Als u de bestandsgrootte van tevoren instelt, in plaats van elke schrijftekst een uitschuifbare schrijf, helpt u de kopieersnelheid te verbeteren in scenario's waarin de bestandsgrootte bekend is. Als het verlengen van schrijfbewerkingen moet worden vermeden, u met `truncate - size <size><file>` opdracht een doelbestandsgrootte instellen. Daarna kopieert `dd if=<source> of=<target> bs=1M conv=notrunc`de opdracht een bronbestand zonder dat de grootte van het doelbestand herhaaldelijk hoeft te worden bijgewerkt. U bijvoorbeeld de grootte van het doelbestand instellen voor elk bestand dat u wilt kopiëren (ervan uitgaan dat een aandeel is gemonteerd onder /mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - en dan - bestanden kopiëren zonder het uitbreiden van schrijft in parallel:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>'Mount-fout(115): bewerking nu in uitvoering' wanneer u Azure-bestanden monteert met SMB 3.0

### <a name="cause"></a>Oorzaak

Sommige Linux-distributies bieden nog geen ondersteuning voor versleutelingsfuncties in SMB 3.0. Gebruikers ontvangen mogelijk foutbericht 115 als ze proberen Azure Files te koppelen met behulp van SMB 3.0, omdat er een functie ontbreekt. SMB 3.0 met volledige versleuteling wordt alleen ondersteund als u gebruikmaakt van Ubuntu 16.04 of hoger.

### <a name="solution"></a>Oplossing

De versleutelingsfunctie voor SMB 3.0 voor Linux is geïntroduceerd in de kernel 4.11. Deze functie maakt het koppelen van een Azure-bestandsshare mogelijk, on-premises of vanuit een andere Azure-regio. Sommige Linux-distributies hebben mogelijk wijzigingen van de 4.11-kernel naar oudere versies van de Linux-kernel die ze onderhouden, teruggedraaid. Raadpleeg [Azure Files met Linux](storage-how-to-use-files-linux.md)gebruiken om te bepalen of uw versie van Linux SMB 3.0 ondersteunt met versleuteling. 

Als uw Linux SMB-client geen ondersteuning biedt voor versleutelen, koppelt u Azure Files met behulp van SMB 2.1 vanaf een Azure Linux-VM die zich in hetzelfde datacenter bevindt als de bestandsshare. Controleer of de instelling [Veilige overdracht vereist]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is uitgeschakeld in het opslagaccount. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fout 'Geen toegang' wanneer u een Azure File Share probeert te openen of verwijderen  
Wanneer u een Azure-bestandsshare in de portal probeert te openen of verwijderen, ontvangt u mogelijk de volgende fout:

Geen toegang  
Foutcode: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 1: Virtuele netwerk- of firewallregels zijn ingeschakeld op het opslagaccount

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security) voor meer informatie.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Oorzaak 2: Uw gebruikersaccount heeft geen toegang tot het opslagaccount

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Blader naar het opslagaccount waar de Azure-bestandsshare zich bevindt, klik op **Toegangsbeheer (IAM)** en controleer of uw gebruikersaccount toegang heeft tot het opslagaccount. Zie [Uw opslagaccount beveiligen met RBAC (Role-Based Access Control)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)voor meer informatie.

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Kan een bestand of map in een Azure-bestandsshare niet verwijderen

### <a name="cause"></a>Oorzaak
Dit probleem treedt meestal op als het bestand of de map een open greep heeft. 

### <a name="solution"></a>Oplossing

Als de SMB-clients alle geopende handgrepen hebben gesloten en het probleem zich blijft voordoen, voert u het volgende uit:

- Gebruik de [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-cmdlet om open handgrepen weer te geven.

- Gebruik de [PowerShell-cmdlet Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) om open handgrepen te sluiten. 

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in Az PowerShell-moduleversie 2.4 of hoger. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u de nieuwste Az PowerShell-module wilt installeren.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Langzame prestaties van een Azure-bestandsshare die is gekoppeld aan een Linux-VM

### <a name="cause-1-caching"></a>Oorzaak 1: Caching

Een mogelijke oorzaak van trage prestaties is uitgeschakeld caching. Caching kan handig zijn als u herhaaldelijk toegang hebt tot een bestand, anders kan het een overhead zijn. Controleer of u de cache gebruikt voordat u deze uitschakelt.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Als u wilt controleren of caching is uitgeschakeld, zoekt u naar de **vermelding cache=**

**Cache=none** geeft aan dat caching is uitgeschakeld. Monteer het aandeel opnieuw met de standaardopdracht voor het monteren of door de optie **cache=strict** expliciet toe te voegen aan de opdracht monteren om ervoor te zorgen dat de standaardcache- of 'strikte' cachingmodus is ingeschakeld.

In sommige scenario's kan de **serverino-mount-optie** ervoor zorgen dat de opdracht **LS** stat uitvoert tegen elke mapvermelding. Dit gedrag leidt tot prestatiedegradatie wanneer u een grote map aanbiedt. U de mount opties in uw **/ etc / fstab** vermelding:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

U ook controleren of de juiste opties worden gebruikt door het uitvoeren van de **sudo mount | grep cifs** commando en het controleren van de output. Het volgende is voorbeelduitvoer:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Als de **optie cache=strict** of **serverino** niet aanwezig is, wordt Azure Files opnieuw uitelkaar zetten en gemonteerd door de opdracht monteren uit de [documentatie](../storage-how-to-use-files-linux.md)uit te voeren. Controleer vervolgens opnieuw of de **/etc/fstab-vermelding** de juiste opties heeft.

### <a name="cause-2-throttling"></a>Oorzaak 2: Throttling

Het is mogelijk dat u te maken krijgt met beperking en uw verzoeken worden verzonden naar een wachtrij. U dit verifiëren door gebruik te maken van [Azure Storage-statistieken in Azure Monitor.](../common/storage-metrics-in-azure-monitor.md)

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of uw app zich binnen de [azure files-schaaldoelen bevindt.](storage-files-scale-targets.md#azure-files-scale-targets)

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Tijdstempels gingen verloren bij het kopiëren van bestanden van Windows naar Linux

Op Linux/Unix-platforms mislukt de **cp-p-opdracht** als verschillende gebruikers bestand 1 en bestand 2 bezitten.

### <a name="cause"></a>Oorzaak

De force flag **f** in COPYFILE resulteert in het uitvoeren van **cp -p-f** op Unix. Met deze opdracht wordt ook de tijdstempel van het bestand dat u niet bezit, niet bewaard.

### <a name="workaround"></a>Tijdelijke oplossing

Gebruik de gebruiker van het opslagaccount voor het kopiëren van de bestanden:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: kan&lt;geen&gt;toegang krijgen tot ' pad': Invoer/uitvoerfout

Wanneer u bestanden in een Azure-bestandsshare probeert weer te geven met de opdracht Ls, blijft de opdracht hangen bij het aanbieden van bestanden. U krijgt de volgende fout:

**ls: kan&lt;niet&gt;toegankelijk' pad ': Input / output fout**


### <a name="solution"></a>Oplossing
Upgrade de Linux-kernel naar de volgende versies die een oplossing voor dit probleem hebben:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alle versies die groter zijn dan of gelijk zijn aan 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Kan geen symbolische links maken - ln: niet om symbolische link 't' te maken: Operatie niet ondersteund

### <a name="cause"></a>Oorzaak
Standaard maakt het monteren van Azure-bestandsshares op Linux met behulp van CIFS geen ondersteuning voor symbolische koppelingen (symlinks) mogelijk. U ziet een fout als deze:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Oplossing
De Linux CIFS-client ondersteunt geen creatie van symbolische koppelingen in Windows-stijl via het SMB 2- of 3-protocol. Momenteel ondersteunt de Linux-client een andere stijl van symbolische links genaamd [Minshall +Franse symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) voor zowel het maken als volgen van bewerkingen. Klanten die symbolische links nodig hebben, kunnen gebruik maken van de optie "mfsymlinks". Wij raden "mfsymlinks" omdat het ook het formaat dat Macs gebruiken.

Als u symlinks wilt gebruiken, voegt u het volgende toe aan het einde van de opdracht CIFS-montage:

```
,mfsymlinks
```

Dus de opdracht ziet er ongeveer als:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

U vervolgens symlinks maken zoals voorgesteld op de [wiki.](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Mount error(112): Host is down" vanwege een time-out van de verbinding

Een koppelfout 112 treedt op de Linux-client op wanneer de client gedurende lange tijd inactief is. Na een lange tijd van inactiviteit wordt de verbinding met de client verbroken en treedt er een time-out op.  

### <a name="cause"></a>Oorzaak

De verbinding kan om de volgende redenen inactief zijn:

-   Fouten in de netwerkcommunicatie waardoor een TCP-verbinding met de server niet opnieuw tot stand kan worden gebracht, wanneer de standaardoptie voor soft koppelen wordt gebruikt
-   Recente oplossingen voor opnieuw verbinden die niet aanwezig zijn in oudere kernels

### <a name="solution"></a>Oplossing

Dit probleem met opnieuw verbinden in de Linux-kernel is nu opgelost als onderdeel van de volgende wijzigingen:

- [Opnieuw verbinden herstellen om de nieuwe verbinding voor de SMB3-sessie niet uit te stellen lang nadat de socket opnieuw is verbonden](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Echoservice onmiddellijk aanroepen nadat de socket opnieuw is verbonden](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Los een mogelijke geheugenbeschadiging op tijdens opnieuw verbinding maken](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Fix a possible double locking of mutex during reconnect (for kernel v4.9 and later) CIFS: Fix a possible double locking of mutex during reconnect (for kernel v4.9 and later) CIFS: Fix a possible double locking of mutex during reconnect (for kernel v4.9 and later) CIF](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Deze wijzigingen zijn mogelijk nog niet doorgevoerd in alle Linux-distributies. Als u een populaire Linux-distributie gebruikt, u op de [Azure-bestanden met Linux gebruiken](storage-how-to-use-files-linux.md) controleren welke versie van uw distributie de nodige kernelwijzigingen heeft.

### <a name="workaround"></a>Tijdelijke oplossing

U kunt dit probleem omzeilen door een harde koppeling op te geven. Met een harde koppeling wordt afgedwongen dat er wordt gewacht totdat er een verbinding tot stand is gebracht, of tot de client expliciet wordt onderbroken. U kunt dit gebruiken om fouten vanwege time-outs van het netwerk te voorkomen. Deze tijdelijke oplossing kan echter leiden tot onbepaalde wachttijden. Wees voorbereid om verbindingen te stoppen, indien nodig.

Als u geen upgrade kunt uitvoeren naar de nieuwste kernelversies, kunt u dit probleem omzeilen door een bestand in de Azure-bestandsshare te bewaren waarnaar u elke 30 seconden, of minder, schrijft. Dit moet een schrijfbewerking zijn, zoals het herschrijven van de aanmaakdatum of wijzigingsdatum van het bestand. Anders ontvangt u wellicht de resultaten die in de cache zijn opgeslagen, en wordt het opnieuw verbinden mogelijk niet geactiveerd.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error -22 on ioctl to get interface list" when you mount an Azure file share using SMB 3.0

### <a name="cause"></a>Oorzaak
Deze fout wordt geregistreerd omdat Azure Files [momenteel geen SMB-multichannel ondersteunt.](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service)

### <a name="solution"></a>Oplossing
Deze fout kan worden genegeerd.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
