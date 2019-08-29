---
title: Problemen oplossen Azure File Sync | Microsoft Docs
description: Veelvoorkomende problemen met Azure File Sync oplossen.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 33e29b02adfccf94da84dd99451117485b892ba3
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072914"
---
# <a name="troubleshoot-azure-file-sync"></a>Problemen oplossen met Azure File Sync
Gebruik Azure File Sync om de bestands shares van uw organisatie in Azure Files te centraliseren, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server bijhoudt. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken voor toegang tot uw gegevens lokaal, zoals SMB, NFS en FTPS. U kunt zoveel caches hebben als u nodig hebt in de hele wereld.

Dit artikel is bedoeld om u te helpen bij het oplossen van problemen die kunnen optreden met uw Azure File Sync-implementatie. We beschrijven ook hoe belang rijke logboeken van het systeem moeten worden verzameld als een grondig onderzoek van het probleem is vereist. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

1. [Azure Storage forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Ondersteuning. Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het Azure Portal op het tabblad **Help** de knop **Help en ondersteuning** en selecteert u vervolgens nieuwe ondersteunings **aanvraag**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, Cloud lagen, enz.). Moet ik mijn server-eind punt verwijderen en opnieuw maken?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agent-installatie en Server registratie
<a id="agent-installation-failures"></a>**Problemen met installatie fouten van agent oplossen**  
Als de installatie van de Azure File Sync agent mislukt, voert u bij een opdracht prompt met verhoogde bevoegdheid de volgende opdracht uit om logboek registratie in te scha kelen tijdens de installatie van de agent:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Controleer de installatie van Installer. log om de oorzaak van de installatie fout te bepalen.

<a id="agent-installation-on-DC"></a>**Agent installatie mislukt op Active Directory-domein controller**  
Als u probeert de synchronisatie agent te installeren op een Active Directory domein controller waar de eigenaar van de PDC-functie zich bevindt op een Windows Server 2008 R2 of onder de versie van het besturings systeem, kunt u het probleem bereiken waarbij de synchronisatie agent niet kan worden geïnstalleerd.

U kunt dit oplossen door de PDC-functie over te dragen naar een andere domein controller met Windows Server 2012 R2 of meer recent en vervolgens de synchronisatie te installeren.

<a id="server-registration-prerequisites"></a>**Bij Server registratie wordt het volgende bericht weer gegeven: "Pre-requisites are missing"** (Vereisten ontbreken)

Dit bericht wordt weer gegeven als AZ of AzureRM Power shell-module niet is geïnstalleerd in Power shell 5,1. 

> [!Note]  
> ServerRegistration. exe biedt geen ondersteuning voor Power shell 6. x. U kunt de cmdlet REGI ster-AzStorageSyncServer in Power shell 6. x gebruiken om de server te registreren.

Voer de volgende stappen uit om de module AZ of AzureRM te installeren op Power shell 5,1:

1. Typ **Power shell** vanaf een opdracht prompt met verhoogde bevoegdheid en druk op ENTER.
2. Installeer de nieuwste AZ-of AzureRM-module door de documentatie te volgen:
    - [AZ-module (hiervoor is .NET 4.7.2 vereist)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM-module]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Voer ServerRegistration. exe uit en voltooi de wizard om de server te registreren bij een opslag synchronisatie service.

<a id="server-already-registered"></a>**Bij Server registratie wordt het volgende bericht weer gegeven: "This server is already registered"** (Deze server is al geregistreerd) 

![Een scherm afbeelding van het dialoog venster Server registratie met het fout bericht ' server is al geregistreerd '](media/storage-sync-files-troubleshoot/server-registration-1.png)

Dit bericht wordt weer gegeven als de server eerder is geregistreerd bij een opslag synchronisatie service. Als u de registratie van de server bij de huidige opslag synchronisatie service ongedaan wilt maken en vervolgens wilt registreren bij een nieuwe opslag synchronisatie service, voert u de stappen uit die worden beschreven in de [registratie van een server bij Azure file sync ongedaan](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)maken.

Als de server niet wordt vermeld onder **geregistreerde servers** in de opslag synchronisatie service, voert u de volgende Power shell-opdrachten uit op de server die u wilt verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Als de server deel uitmaakt van een cluster, kunt u de optionele para meter *Reset-StorageSyncServer-CleanClusterRegistration* gebruiken om ook de cluster registratie te verwijderen.

<a id="web-site-not-trusted"></a>**Wanneer ik een server registreer, zie ik talloze ' website-niet-vertrouwde ' antwoorden. Waarom is dat?**  
Dit probleem treedt op wanneer het **Verbeterde beveiligings beleid van Internet Explorer** is ingeschakeld tijdens de registratie van de server. Voor meer informatie over het correct uitschakelen van het **Verbeterde beveiligings beleid voor Internet Explorer** , Zie [Windows Server voorbereiden voor gebruik met Azure file sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) en [hoe u Azure file sync implementeert](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**De server wordt niet vermeld onder geregistreerde servers in de Azure Portal**  
Als een server niet wordt vermeld onder **geregistreerde servers** voor een opslag synchronisatie service:
1. Meld u aan bij de server die u wilt registreren.
2. Open bestanden Verkenner en ga naar de installatiemap van de opslag synchronisatie agent (de standaard locatie is C:\Program Files\Azure\StorageSyncAgent). 
3. Voer ServerRegistration. exe uit en voltooi de wizard om de server te registreren bij een opslag synchronisatie service.

## <a name="sync-group-management"></a>Groeps beheer synchroniseren
<a id="cloud-endpoint-using-share"></a>**Het maken van een Cloud-eind punt is mislukt, met de volgende fout: 'De opgegeven Azure-bestandsshare is al in gebruik door een ander cloudeindpunt'**  
Deze fout treedt op als de Azure-bestands share al wordt gebruikt door een ander Cloud eindpunt. 

Als u dit bericht ziet en de Azure-bestands share momenteel niet wordt gebruikt door een Cloud eindpunt, voert u de volgende stappen uit om de Azure File Sync meta gegevens op de Azure-bestands share te wissen:

> [!Warning]  
> Het verwijderen van de meta gegevens van een Azure-bestands share die momenteel wordt gebruikt door een Cloud-eind punt zorgt ervoor dat Azure File Sync bewerkingen mislukken. 

1. Ga in het Azure Portal naar uw Azure-bestands share.  
2. Klik met de rechter muisknop op de Azure-bestands share en selecteer vervolgens **meta gegevens bewerken**.
3. Klik met de rechter muisknop op **SyncService**en selecteer **verwijderen**.

<a id="cloud-endpoint-authfailed"></a>**Het maken van een Cloud-eind punt is mislukt, met de volgende fout: AuthorizationFailed**  
Deze fout treedt op als uw gebruikers account niet voldoende rechten heeft voor het maken van een Cloud eindpunt. 

Als u een Cloud eindpunt wilt maken, moet uw gebruikers account over de volgende micro soft-autorisatie machtigingen beschikken:  
* Wie Roldefinitie ophalen
* Schrijven Aangepaste roldefinitie maken of bijwerken
* Wie Roltoewijzing ophalen
* Schrijven Roltoewijzing maken

De volgende ingebouwde rollen hebben de vereiste micro soft-autorisatie machtigingen:  
* Eigenaar
* Administrator voor gebruikerstoegang

Om te bepalen of de rol van uw gebruikers account over de vereiste machtigingen beschikt:  
1. Selecteer **resource groepen**In het Azure Portal.
2. Selecteer de resource groep waar het opslag account zich bevindt en selecteer vervolgens **toegangs beheer (IAM)** .
3. Selecteer het tabblad **roltoewijzingen** .
4. Selecteer de **rol** (bijvoorbeeld eigenaar of Inzender) voor uw gebruikers account.
5. Selecteer in de lijst **resource provider** de optie **micro soft-autorisatie**. 
    * De **roltoewijzing** moet **Lees** -en **Schrijf** machtigingen hebben.
    * De **roldefinitie** moet **Lees** -en **Schrijf** machtigingen hebben.

<a id="-2134375898"></a>**Het server-eind punt kan niet worden gemaakt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134375898 of 0x80c80226)**  
Deze fout treedt op als het pad naar het server eindpunt zich op het systeem volume bevindt en de Cloud Tiering is ingeschakeld. Cloud lagen worden niet ondersteund op het systeem volume. Als u een server eindpunt op het systeem volume wilt maken, schakelt u Cloud lagen uit bij het maken van het server eindpunt.

<a id="-2147024894"></a>**Het server-eind punt kan niet worden gemaakt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2147024894 of 0x80070002)**  
Deze fout treedt op als het opgegeven pad naar het server eindpunt niet geldig is. Controleer of het opgegeven pad van de server eindpunt een lokaal gekoppeld NTFS-volume is. Opmerking: Azure File Sync biedt geen ondersteuning voor toegewezen stations als een pad naar een server eindpunt.

<a id="-2134347507"></a>**Het server-eind punt kan niet worden gemaakt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134347507 of 0x80c8710d)**  
Deze fout treedt op omdat Azure File Sync geen server eindpunten ondersteunt op volumes die een map met informatie over een gecomprimeerd systeem volume bevatten. Om dit probleem op te lossen, decomprimeert u de map System Volume Information. Als de map System Volume Information de enige map is die op het volume is gecomprimeerd, voert u de volgende stappen uit:

1. Down load het [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) -hulp programma.
2. Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om een opdracht prompt te starten die wordt uitgevoerd onder het systeem account: **PsExec. exe-i-s-d-cmd**
3. Voer de volgende opdrachten uit vanaf de opdracht regel die wordt uitgevoerd onder het systeem account en druk op ENTER:   
    **cd/d "stationsletter: \ System Volume Information"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Het server-eind punt kan niet worden gemaakt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134376345 of 0x80C80067)**  
Deze fout treedt op als de limiet voor Server eindpunten per server wordt bereikt. Azure File Sync ondersteunt momenteel Maxi maal 30 server eindpunten per server. Zie [Azure file sync Scale-doelen](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets)voor meer informatie.

<a id="-2134376427"></a>**Het server-eind punt kan niet worden gemaakt, met de volgende fout: "MgmtServerJobFailed" (fout code:-2134376427 of 0x80c80015)**  
Deze fout treedt op als er al een ander server eindpunt wordt gesynchroniseerd met het opgegeven pad naar de server eindpunt. Azure File Sync biedt geen ondersteuning voor meerdere server eindpunten die dezelfde map of hetzelfde volume synchroniseren.

<a id="-2134347757"></a>**Het verwijderen van het server eindpunt is mislukt, met de volgende fout: "MgmtServerJobExpired" (fout code:-2134347757 of 0x80c87013)**  
Deze fout treedt op als de server offline is of geen verbinding met het netwerk heeft. Als de server niet langer beschikbaar is, moet u de registratie van de server in de portal opheffen, waardoor de server eindpunten worden verwijderd. Als u de server eindpunten wilt verwijderen, volgt u de stappen die worden beschreven in de [registratie van een server bij Azure file sync ongedaan maken](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Kan de eigenschappen pagina van het server eindpunt niet openen of het beleid voor Cloud lagen niet bijwerken**  
Dit probleem kan optreden als een beheer bewerking op het server eindpunt mislukt. Als de pagina eindpunt eigenschappen van de server niet wordt geopend in de Azure Portal, kan het server eindpunt bijwerken met behulp van Power shell-opdrachten van de server dit probleem mogelijk oplossen. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Server-eind punt heeft de status ' geen activiteit ' of ' in behandeling ' en de status van de server op de Blade geregistreerde servers is ' offline weer gegeven '**  

Dit probleem kan optreden als het controle proces voor opslag synchronisatie niet wordt uitgevoerd of als de server niet kan communiceren met de Azure File Sync-Service vanwege een proxy of firewall.

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Open Taakbeheer op de server en controleer of het Storage Sync Monitor-proces (AzureStorageSyncMonitor.exe) actief is. Als het proces niet wordt uitgevoerd, start u om te beginnen de server opnieuw op. Als het probleem niet wordt verholpen bij het opnieuw opstarten van de server, voert u een upgrade naar de laatste versie van de Azure File Sync-[agent](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) uit.
2. Controleer of de firewall-en proxy-instellingen juist zijn geconfigureerd:
    - Als de server zich achter een firewall bevindt, controleert u of uitgaand verkeer via poort 443 is toegestaan. Als de firewall het verkeer naar specifieke domeinen beperkt, moet u controleren of de domeinen die worden vermeld in de firewall [documentatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) toegankelijk zijn.
    - Als de-server zich achter een proxy bevindt, configureert u de computer-of app-specifieke proxy-instellingen door de stappen in de proxy- [documentatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)te volgen.

<a id="endpoint-noactivity-sync"></a>**Server-eind punt heeft de status ' geen activiteit ' en de status van de server op de Blade geregistreerde servers is ' online '**  

De status van het server eindpunt van ' geen activiteit ' betekent dat het server-eind punt in de afgelopen twee uur geen synchronisatie activiteiten heeft geregistreerd.

Een server eindpunt kan geen synchronisatie activiteiten registreren om de volgende redenen:

- De agent versie 4.3.0.0 of ouder is geïnstalleerd en de server heeft een actieve VSS-synchronisatie sessie (SnapshotSync). Wanneer een VSS-synchronisatie sessie actief is voor een server eindpunt, kunnen andere server eindpunten op hetzelfde volume geen Start synchronisatie sessie starten totdat de VSS-synchronisatie sessie is voltooid. U kunt dit probleem oplossen door Agent versie 5.0.2.0 of nieuwer te installeren die ondersteuning biedt voor meerdere server eindpunten die op een volume worden gesynchroniseerd wanneer een VSS-synchronisatie sessie actief is.

    Zie [Hoe kan ik de voortgang van een huidige synchronisatie sessie controleren](#how-do-i-monitor-the-progress-of-a-current-sync-session)als u de huidige synchronisatie activiteit op een server wilt controleren.

- De server heeft het maximum aantal gelijktijdige synchronisatie sessies bereikt. 
    - Agent versie 4. x en nieuwer: De limiet is afhankelijk van de beschik bare systeem resources.
    - Agent versie 3. x: 2 actieve synchronisatie sessies per processor of Maxi maal 8 actieve synchronisatie sessies per server.

> [!Note]  
> Als de status van de server op de Blade geregistreerde servers ' offline weer gegeven ' is, voert u de stappen uit die worden beschreven in het [Server-eind punt heeft de status ' geen activiteit ' of ' in behandeling ' en de server status op de Blade geregistreerde servers is ' offline weer gegeven '](#server-endpoint-noactivity) .

## <a name="sync"></a>Synchroniseren
<a id="afs-change-detection"></a>**Hoe lang duurt het om het bestand te synchroniseren met servers in de synchronisatie groep als ik een bestand rechtstreeks in mijn Azure-bestands share heb gemaakt via SMB of via de portal?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**De status van het server eindpunt is gedurende enkele uren nog in behandeling**  
Dit probleem wordt verwacht als u een Cloud-eind punt maakt en een Azure-bestands share gebruikt die gegevens bevat. De wijzigings inventarisatie taak waarmee wordt gescand op wijzigingen in de Azure-bestands share moet zijn voltooid voordat bestanden kunnen worden gesynchroniseerd tussen de Cloud en server eindpunten. De tijd voor het volt ooien van de taak is afhankelijk van de grootte van de naam ruimte in de Azure-bestands share. De status van het server eindpunt moet worden bijgewerkt zodra de inventarisatie taak is gewijzigd.

### <a id="broken-sync"></a>Synchronisatie status Hoe kan ik controleren?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Binnen elke synchronisatie groep kunt u inzoomen op de afzonderlijke server eindpunten om de status van de laatst voltooide synchronisatie sessies weer te geven. Een groene status kolom en een bestand met een niet-gesynchroniseerde waarde van 0 geven aan dat de synchronisatie werkt zoals verwacht. Als dit niet het geval is, raadpleegt u hieronder voor een lijst met algemene synchronisatie fouten en het afhandelen van bestanden die niet worden gesynchroniseerd. 

![Een scherm opname van de Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Ga naar de telemetrie-logboeken van de server, die u kunt vinden `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`in de logboeken op. Gebeurtenis 9102 komt overeen met een voltooide synchronisatie sessie. Zoek naar de meest recente gebeurtenis met ID 9102 voor de laatste synchronisatie status. SyncDirection vertelt u of deze sessie een upload-of download bewerking is. Als HResult 0 is, is de synchronisatie sessie geslaagd. Een niet-nul HResult houdt in dat er een fout is opgetreden tijdens de synchronisatie. Hieronder vindt u een lijst met veelvoorkomende fouten. Als de PerItemErrorCount groter is dan 0, betekent dit dat sommige bestanden of mappen niet juist zijn gesynchroniseerd. Het is mogelijk een HResult van 0 te hebben, maar een PerItemErrorCount die groter is dan 0.

Hieronder ziet u een voor beeld van een geslaagde upload. Voor het omwille van de boogën worden slechts enkele van de waarden in elke 9102-gebeurtenis hieronder weer gegeven. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Een mislukte upload kan er echter als volgt uitzien:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Soms mislukken synchronisatie sessies volledig of hebben ze een PerItemErrorCount die niet gelijk zijn aan nul, maar worden er nog steeds een andere bewerking uitgevoerd, waarbij sommige bestanden met succes worden gesynchroniseerd. Dit kan worden weer gegeven in de velden toegepast * (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes), waarin u kunt zien hoeveel van de sessie slaagt. Als er meerdere synchronisatie sessies in een rij worden weer geven die niet werken, maar wel een oplopend toegepast aantal hebben, moet u de synchronisatie tijd opgeven om het opnieuw te proberen voordat een ondersteunings ticket wordt geopend.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>De voortgang van een huidige synchronisatie sessie Hoe kan ik controleren?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Ga in de synchronisatie groep naar het server eindpunt in kwestie en Bekijk de sectie synchronisatie activiteit om het aantal bestanden te zien dat in de huidige synchronisatie sessie is geüpload of gedownload. Houd er rekening mee dat deze status ongeveer 5 minuten wordt uitgesteld. als uw synchronisatie sessie binnen deze periode klein genoeg is om te worden voltooid, wordt deze mogelijk niet gerapporteerd in de portal. 

# <a name="servertabserver"></a>[Server](#tab/server)
Bekijk de meest recente 9302-gebeurtenis in het logboek van de telemetrie op de server (Ga in het Logboeken naar toepassingen en services Logs\Microsoft\FileSync\Agent\Telemetry). Deze gebeurtenis geeft de status van de huidige synchronisatie sessie aan. TotalItemCount geeft aan hoeveel bestanden moeten worden gesynchroniseerd, AppliedItemCount het aantal bestanden dat tot nu toe is gesynchroniseerd en PerItemErrorCount het aantal bestanden dat niet kan worden gesynchroniseerd (zie hieronder voor meer informatie over hoe u dit kunt doen).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hoe kan ik weet of mijn servers synchroon zijn met elkaar?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Voor elke server in een bepaalde synchronisatie groep moet u het volgende doen:
- De tijds tempels voor de laatste synchronisatie poging voor zowel uploaden als downloaden zijn recent.
- De status is groen voor uploaden en downloaden.
- In het veld synchronisatie activiteit worden weinig of geen bestanden weer gegeven om te synchroniseren.
- Het veld bestanden niet synchroniseren is 0 voor uploaden en downloaden.

# <a name="servertabserver"></a>[Server](#tab/server)
Bekijk de voltooide synchronisatie sessies, die zijn gemarkeerd door 9102-gebeurtenissen in het logboek voor telemetrie voor elke server (in de Logboeken, gaat `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`u naar). 

1. Op elke wille keurige server moet u ervoor zorgen dat de nieuwste upload-en download sessies zijn voltooid. Om dit te doen, controleert u of HResult en PerItemErrorCount 0 zijn voor uploaden en downloaden (het veld SyncDirection geeft aan of een bepaalde sessie een upload-of download sessie is). Houd er rekening mee dat als u geen recent voltooide synchronisatie sessie ziet, er waarschijnlijk een synchronisatie sessie wordt uitgevoerd. dit wordt verwacht als u een grote hoeveelheid gegevens hebt toegevoegd of gewijzigd.
2. Wanneer een server volledig up-to-date is met de Cloud en er geen wijzigingen in een van beide richtingen worden gesynchroniseerd, worden er lege synchronisatie sessies weer geven. Deze worden aangegeven door upload-en download gebeurtenissen waarin alle Sync *-velden (SyncFileCount, SyncDirCount, SyncTombstoneCount en SyncSizeBytes) nul zijn, wat betekent dat er niets kan worden gesynchroniseerd. Houd er rekening mee dat deze lege synchronisatie sessies mogelijk niet worden uitgevoerd op servers met een hoge verloop tijd omdat er altijd iets nieuws is om te synchroniseren. Als er geen synchronisatie activiteit is, moeten deze elke 30 minuten worden uitgevoerd. 
3. Als alle servers up-to-date zijn met de Cloud, wat betekent dat de recente upload-en download sessies lege synchronisatie sessies zijn, kunt u met redelijke zekerheid zeggen dat het systeem als geheel is gesynchroniseerd. 
    
Houd er rekening mee dat als u wijzigingen rechtstreeks in uw Azure-bestands share hebt aangebracht, deze wijziging pas door Azure File Sync wordt gedetecteerd nadat de inventarisatie wordt uitgevoerd, die elke 24 uur plaatsvindt. Het is mogelijk dat een server het is bijgewerkt met de Cloud wanneer de recente wijzigingen die rechtstreeks in de Azure-bestands share zijn aangebracht, ontbreken. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hoe kan ik zien of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?
Als uw PerItemErrorCount op de server of bestanden die niet zijn gesynchroniseerd in de portal groter zijn dan 0 voor een bepaalde synchronisatie sessie, betekent dit dat sommige items niet kunnen worden gesynchroniseerd. Bestanden en mappen kunnen kenmerken hebben die voor komen dat ze worden gesynchroniseerd. Deze kenmerken kunnen permanent zijn en vereisen expliciete acties om de synchronisatie te hervatten, bijvoorbeeld het verwijderen van niet-ondersteunde tekens uit de naam van het bestand of de map. Ze kunnen ook tijdelijk zijn, wat betekent dat het bestand of de map automatisch wordt hervat. bestanden met open ingangen worden bijvoorbeeld automatisch hervat wanneer het bestand wordt gesloten. Wanneer de Azure File Sync-Engine een dergelijk probleem detecteert, wordt er een fouten logboek gegenereerd dat kan worden geparseerd om de items weer te geven die momenteel niet worden gesynchroniseerd.

Als u deze fouten wilt zien, voert u het Power shell-script **FileSyncErrorsReport. ps1** (in de installatiemap van de agent van de Azure file sync agent) uit om te bepalen welke bestanden niet kunnen worden gesynchroniseerd vanwege open ingangen, niet-ondersteunde tekens of andere problemen. Het veld het itempad vertelt u de locatie van het bestand ten opzichte van de hoofdmap voor synchronisatie. Zie de lijst met veelvoorkomende synchronisatie fouten voor herstels tappen.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Problemen oplossen per bestand/directory-synchronisatie fouten
**ItemResults-logboek: synchronisatie fouten per item**  

| HRESULT | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Het gelaagde bestand op de server is niet toegankelijk. Dit probleem treedt op als het gelaagde bestand niet is ingetrokken voordat een server eindpunt werd verwijderd. | Zie gelaagde [bestanden niet toegankelijk op de server nadat u een server eindpunt hebt verwijderd](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | De wijziging van het bestand of de map kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | Geen actie vereist. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | De naam van het bestand of de map is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie het [verwerken van niet-ondersteunde tekens](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | De naam van het bestand of de map is ongeldig. | Wijzig de naam van het bestand of de map in kwestie. Zie het [verwerken van niet-ondersteunde tekens](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet langer in gebruik is. | Geen actie vereist. Azure File Sync maakt een tijdelijke VSS-moment opname eenmaal per dag op de server om bestanden met geopende ingangen te synchroniseren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Het bestand is gewijzigd, maar de wijziging is nog niet gedetecteerd door de synchronisatie. De synchronisatie wordt hersteld nadat deze wijziging is gedetecteerd. | Geen actie vereist. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Het bestand is verwijderd en de synchronisatie is niet op de hoogte van de wijziging. | Geen actie vereist. Deze fout wordt niet meer in de logboek registratie vastgelegd zodra de detectie van wijzigingen detecteert dat het bestand is verwijderd. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Het bestand is overgeslagen, maar wordt gesynchroniseerd tijdens de volgende synchronisatie sessie. | Geen actie vereist. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Het bestand kan niet worden gesynchroniseerd omdat de limiet voor Azure-bestands shares is bereikt. | Zie [de sectie opslag limiet van Azure file share](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) in de hand leiding voor probleem oplossing om dit probleem op te lossen. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Het bestand is versleuteld met een niet-ondersteunde oplossing (zoals NTFS EFS). | Het bestand ontsleutelen en een ondersteunde versleutelings oplossing gebruiken. Zie de sectie [oplossingen voor versleuteling](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) in de plannings handleiding voor een lijst met ondersteunings oplossingen. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Het bestand bevindt zich in een gerepliceerde DFS-R-map met het kenmerk alleen-lezen. | Het bestand bevindt zich in een gerepliceerde DFS-R-map met het kenmerk alleen-lezen. Azure Files-synchronisatie biedt geen ondersteuning voor Server eindpunten in DFS-R-alleen-lezen replicatie mappen. Zie de [plannings handleiding](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) voor meer informatie. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand heeft de status verwijderen in behandeling. | Geen actie vereist. Het bestand wordt verwijderd zodra alle geopende bestands ingangen zijn gesloten. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Het bestand kan niet worden gesynchroniseerd omdat de instellingen voor de firewall en het virtuele netwerk van het opslag account zijn ingeschakeld en de server geen toegang heeft tot het opslag account. | Voeg het IP-adres of het virtuele netwerk van de server toe aan de hand van de stappen die worden beschreven in de sectie [instellingen voor firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatie handleiding. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Het bestand kan niet worden gesynchroniseerd omdat de grootte van de security descriptor groter is dan de limiet van 64 KiB. | U kunt dit probleem oplossen door ACE (Access Control Entries) op het bestand te verwijderen om de security descriptor grootte te verminderen. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Het bestand kan niet worden gesynchroniseerd vanwege een onverwachte fout. | Als de fout gedurende enkele dagen blijft bestaan, kunt u een ondersteunings aanvraag openen. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet langer in gebruik is. | Geen actie vereist. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand is tijdens de synchronisatie gewijzigd, dus het moet opnieuw worden gesynchroniseerd. | Geen actie vereist. |

#### <a name="handling-unsupported-characters"></a>Niet-ondersteunde tekens verwerken
Als het Power shell-script **FileSyncErrorsReport. ps1** fouten weergeeft als gevolg van niet-ondersteunde tekens (fout code 0x8007007b of 0x80c80255), moet u de tekens bij fout verwijderen of de naam ervan wijzigen. In Power shell worden deze tekens waarschijnlijk afgedrukt als vraag tekens of lege rechthoeken omdat de meeste van deze tekens geen standaard-visuele code ring hebben. Het [evaluatie hulpprogramma](storage-sync-files-planning.md#evaluation-cmdlet) kan worden gebruikt om tekens te identificeren die niet worden ondersteund.

De onderstaande tabel bevat alle Unicode-tekens Azure File Sync nog niet wordt ondersteund.

| Tekenset | Aantal tekens |
|---------------|-----------------|
| <ul><li>0x0000009D (opdracht osc-besturings systeem)</li><li>0x00000090 (DCS-besturings element voor Apparaatbeheer)</li><li>0x0000008F (ss3 één ploeg drie)</li><li>0x00000081 (definitie hoge octetten)</li><li>0x0000007F (del verwijderen)</li><li>0x0000008D (RI-omgekeerde regel invoer)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Arabische presentatie vormen-a) | 32 |
| 0x0000FFF0-0x0000FFFF (specials) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (niet-teken)</li><li>0x0002FFFE-0x0002FFFF = 2 (niet-teken)</li><li>0x0003FFFE-0x0003FFFF = 2 (niet-teken)</li><li>0x0004FFFE-0x0004FFFF = 2 (niet-teken)</li><li>0x0005FFFE-0x0005FFFF = 2 (niet-teken)</li><li>0x0006FFFE-0x0006FFFF = 2 (niet-teken)</li><li>0x0007FFFE-0x0007FFFF = 2 (niet-teken)</li><li>0x0008FFFE-0x0008FFFF = 2 (niet-teken)</li><li>0x0009FFFE-0x0009FFFF = 2 (niet-teken)</li><li>0x000AFFFE-0x000AFFFF = 2 (niet-teken)</li><li>0x000BFFFE-0x000BFFFF = 2 (niet-teken)</li><li>0x000CFFFE-0x000CFFFF = 2 (niet-teken)</li><li>0x000DFFFE-0x000DFFFF = 2 (niet-teken)</li><li>0x000EFFFE-0x000EFFFF = 2 (niet gedefinieerd)</li><li>0x000FFFFE-0x000FFFFF = 2 (aanvullend privé gebruik-gebied)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Algemene synchronisatie fouten
<a id="-2147023673"></a>**De synchronisatie sessie is geannuleerd.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimaal)** | -2147023673 | 
| **Fout reeks** | ERROR_CANCELLED |
| **Herstel vereist** | Nee |

Synchronisatie sessies kunnen om verschillende redenen mislukken, zoals de server die opnieuw wordt opgestart of bijgewerkt, VSS-moment opnamen, enzovoort. Hoewel deze fout lijkt op opvolgen, is het veilig om deze fout te negeren, tenzij deze gedurende een periode van enkele uren persistent is.

<a id="-2147012889"></a>**Er kan geen verbinding worden gemaakt met de service.**    

| | |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (decimaal)** | -2147012889 | 
| **Fout reeks** | WININET_E_NAME_NOT_RESOLVED |
| **Herstel vereist** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**De gebruikers aanvraag is beperkt door de service.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimaal)** | -2134376372 |
| **Fout reeks** | ECS_E_USER_REQUEST_THROTTLED |
| **Herstel vereist** | Nee |

Er is geen actie vereist; de server zal het opnieuw proberen. Als deze fout gedurende enkele uren blijft bestaan, maakt u een ondersteunings aanvraag.

<a id="-2134364043"></a>**Synchronisatie wordt geblokkeerd totdat de wijzigings detectie is voltooid na het herstellen**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimaal)** | -2134364043 |
| **Fout reeks** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Herstel vereist** | Nee |

Geen actie vereist. Wanneer een bestand of bestands share (Cloud-eind punt) wordt hersteld met behulp van Azure Backup, wordt de synchronisatie geblokkeerd totdat de wijzigings detectie is voltooid op de Azure-bestands share. De detectie van wijzigingen wordt onmiddellijk uitgevoerd zodra het herstellen is voltooid en de duur is gebaseerd op het aantal bestanden in de bestands share.

<a id="-2147216747"></a>**De synchronisatie is mislukt, omdat de synchronisatie database is verwijderd.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimaal)** | -2147216747 |
| **Fout reeks** | SYNC_E_METADATA_INVALID_OPERATION |
| **Herstel vereist** | Nee |

Deze fout treedt doorgaans op wanneer een back-uptoepassing een VSS-moment opname maakt en de gesynchroniseerde data base uit het geheugen is verwijderd. Als deze fout gedurende enkele uren blijft bestaan, maakt u een ondersteunings aanvraag.

<a id="-2134364065"></a>**Sync heeft geen toegang tot de Azure-bestands share die is opgegeven in het Cloud eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimaal)** | -2134364065 |
| **Fout reeks** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang kan krijgen tot de Azure-bestands share. Dit kan zijn omdat de Azure-bestands share of het opslag account dat wordt gehost, niet meer bestaat. U kunt deze fout oplossen door de volgende stappen te door lopen:

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)
3. [Zorg ervoor dat Azure File Sync toegang heeft tot het opslag account.](#troubleshoot-rbac)
4. [Controleer of de instellingen voor de firewall en het virtuele netwerk op het opslag account correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**De gebruikte naam van het opslag account kan niet worden omgezet.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimaal)** | -2134364064 |
| **Fout reeks** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Herstel vereist** | Ja |

1. Controleer of u de DNS-naam van de opslag van de server kunt omzetten.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
3. [Controleer of de instellingen voor de firewall en het virtuele netwerk op het opslag account correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-1906441138"></a>**De synchronisatie is mislukt vanwege een probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimaal)** | -1906441138 |
| **Fout reeks** | JET_errWriteConflict |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer er een probleem is met de interne data base die wordt gebruikt door Azure File Sync. Als dit probleem zich voordoet, maakt u een ondersteunings aanvraag en neemt u contact met u op om dit probleem op te lossen.

<a id="-2134364053"></a>**De versie van de Azure File Sync-agent die op de server is geïnstalleerd, wordt niet ondersteund.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimaal)** | -2134364053 |
| **Fout reeks** | ECS_E_AGENT_VERSION_BLOCKED |
| **Herstel vereist** | Ja |

Deze fout treedt op als de Azure File Sync agent versie die op de server is geïnstalleerd, niet wordt ondersteund. U kunt dit probleem oplossen door een [upgrade uit]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) te voeren naar een [ondersteunde agent versie]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**U hebt de opslag limiet voor Azure file share bereikt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimaal)** | -2134351810 |
| **Fout reeks** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de opslag limiet van de Azure-bestands share is bereikt. Dit kan gebeuren als er een quotum voor een Azure-bestands share wordt toegepast, of als het gebruik de limieten voor een Azure-bestands share overschrijdt. Zie de [huidige limieten voor een Azure-bestands share](storage-files-scale-targets.md)voor meer informatie.

1. Ga naar de synchronisatie groep binnen de opslag synchronisatie service.
2. Selecteer het eind punt van de cloud in de synchronisatie groep.
3. Noteer de naam van de Azure-bestands share in het geopende deel venster.
4. Selecteer het gekoppelde opslag account. Als deze koppeling mislukt, is het opslag account waarnaar wordt verwezen, verwijderd.

    ![Een scherm afbeelding met het detail venster van het Cloud eindpunt met een koppeling naar het opslag account.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecteer **bestanden** om de lijst met bestands shares weer te geven.
6. Klik op de drie punten aan het einde van de rij voor de Azure-bestands share waarnaar wordt verwezen door het Cloud eindpunt.
7. Controleer of het **gebruik** lager is dan het **quotum**. Opmerking tenzij er een alternatieve quotum is opgegeven, komt het quotum overeen met de [maximale grootte van de Azure-bestands share](storage-files-scale-targets.md).

    ![Een scherm afbeelding van de eigenschappen van de Azure-bestands share.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Als de share vol is en er geen quotum is ingesteld, is het mogelijk om dit probleem op te lossen door elke submap van het huidige server eindpunt te maken in een eigen server eindpunt in hun eigen afzonderlijke synchronisatie groepen. Op deze manier wordt elke submap gesynchroniseerd met de afzonderlijke Azure-bestands shares.

<a id="-2134351824"></a>**De Azure-bestands share is niet gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimaal)** | -2134351824 |
| **Fout reeks** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de Azure-bestands share niet toegankelijk is. Problemen oplossen:

1. [Controleer of het opslag account bestaat.](#troubleshoot-storage-account)
2. [Zorg ervoor dat de Azure-bestands share bestaat.](#troubleshoot-azure-file-share)

Als de Azure-bestands share is verwijderd, moet u een nieuwe bestands share maken en de synchronisatie groep vervolgens opnieuw maken. 

<a id="-2134364042"></a>**De synchronisatie is onderbroken terwijl dit Azure-abonnement is onderbroken.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimaal)** | -2134364042 |
| **Fout reeks** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer het Azure-abonnement is onderbroken. Synchronisatie wordt opnieuw ingeschakeld wanneer het Azure-abonnement wordt hersteld. Zie [Waarom is mijn Azure-abonnement uitgeschakeld en hoe kan ik het opnieuw activeren?](../../billing/billing-subscription-become-disable.md) voor meer informatie.

<a id="-2134364052"></a>**Er is een firewall of virtuele netwerken geconfigureerd voor het opslag account.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimaal)** | -2134364052 |
| **Fout reeks** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de Azure-bestands share niet toegankelijk is vanwege een firewall van een opslag account of omdat het opslag account deel uitmaakt van een virtueel netwerk. Controleer of de instellingen voor de firewall en het virtuele netwerk van het opslag account correct zijn geconfigureerd. Zie [instellingen voor Firewall en virtueel netwerk configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)voor meer informatie. 

<a id="-2134375911"></a>**De synchronisatie is mislukt vanwege een probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimaal)** | -2134375911 |
| **Fout reeks** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Herstel vereist** | Nee |

Deze fout wordt meestal vanzelf opgelost en kan optreden als:

* Een groot aantal bestands wijzigingen op de servers in de synchronisatie groep.
* Een groot aantal fouten op afzonderlijke bestanden en mappen.

Als deze fout langer dan een paar uur blijft bestaan, maakt u een ondersteunings aanvraag en neemt u contact met u op om dit probleem op te lossen.

<a id="-2146762487"></a>**De server kan geen beveiligde verbinding tot stand brengen. De Cloud service heeft een onverwacht certificaat ontvangen.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimaal)** | -2146762487 |
| **Fout reeks** | CERT_E_UNTRUSTEDROOT |
| **Herstel vereist** | Ja |

Deze fout kan optreden als uw organisatie gebruikmaakt van een SSL-beëindigings proxy of als een schadelijke entiteit het verkeer tussen uw server en de Azure File Sync-service onderschept. Als u zeker weet dat dit wordt verwacht (omdat uw organisatie een SSL-beëindigings proxy gebruikt), slaat u de verificatie van het certificaat over met een overschrijving van het REGI ster.

1. Maak de register waarde SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Start de synchronisatie service op de geregistreerde server opnieuw op.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Door deze register waarde in te stellen, accepteert de Azure File Sync-agent alle lokaal vertrouwde SSL-certificaten bij het overbrengen van gegevens tussen de server en de Cloud service.

<a id="-2147012894"></a>**Er kan geen verbinding worden gemaakt met de service.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimaal)** | -2147012894 |
| **Fout reeks** | WININET_E_TIMEOUT |
| **Herstel vereist** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**De synchronisatie is mislukt vanwege een probleem met de verificatie.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimaal)** | -2134375680 |
| **Fout reeks** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Herstel vereist** | Ja |

Deze fout treedt doorgaans op omdat de server tijd onjuist is. Als de server wordt uitgevoerd op een virtuele machine, controleert u of de tijd op de host juist is.

<a id="-2134364040"></a>**De synchronisatie is mislukt vanwege een verlopen certificaat.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimaal)** | -2134364040 |
| **Fout reeks** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor authenticatie, is verlopen.

Voer de volgende stappen uit om te bevestigen dat het certificaat is verlopen:  
1. Open de MMC-module Certificaten, selecteer computer account en navigeer naar certificaten (lokale computer) \Personal\Certificates.
2. Controleer of het certificaat voor client verificatie is verlopen.

Als het certificaat voor client verificatie is verlopen, voert u de volgende stappen uit om het probleem op te lossen:

1. Controleer of de Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende Power shell-opdracht uit op de-server:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**De synchronisatie is mislukt vanwege een verificatie certificaat dat niet is gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimaal)** | -2134375896 |
| **Fout reeks** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor authenticatie, niet is gevonden.

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Controleer of de Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende Power shell-opdracht uit op de-server:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**De synchronisatie is mislukt omdat de verificatie-identiteit niet is gevonden.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimaal)** | -2134364039 |
| **Fout reeks** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het server eindpunt is verwijderd en het eind punt zich nu in een gedeeltelijk verwijderde staat bevindt. U kunt dit probleem oplossen door het server eindpunt opnieuw te verwijderen.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Het volume waar het server eindpunt zich bevindt, heeft weinig schijf ruimte.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimaal)** | -1906441711 |
| **Fout reeks** | JET_errLogDiskFull |
| **Herstel vereist** | Ja |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimaal)** | -2134375654 |
| **Fout reeks** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het volume is opgevuld. Deze fout treedt doorgaans op omdat bestanden buiten het server eindpunt ruimte op het volume gebruiken. Maak ruimte vrij op het volume door extra server eindpunten toe te voegen, bestanden te verplaatsen naar een ander volume of de grootte van het volume te verg Roten waarop het server eindpunt zich bevindt.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**De service is nog niet gereed om te synchroniseren met dit server eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimaal)** | -2134364145 |
| **Fout reeks** | ECS_E_REPLICA_NOT_READY |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat er rechtstreeks wijzigingen zijn aangebracht in de Azure-bestands share en de wijzigings detectie wordt uitgevoerd. De synchronisatie wordt begonnen wanneer de wijzigings detectie is voltooid.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**De synchronisatie is mislukt vanwege problemen met een groot aantal afzonderlijke bestanden.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimaal)** | -2134375877 |
| **Fout reeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Herstel vereist** | Ja |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimaal)** | -2134375908 |
| **Fout reeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Herstel vereist** | Ja |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimaal)** | -2134375853 |
| **Fout reeks** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Herstel vereist** | Ja |

Als er veel per bestands synchronisatie fouten zijn, kunnen synchronisatie sessies mislukken. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync maakt een tijdelijke VSS-moment opname eenmaal per dag op de server om bestanden met geopende ingangen te synchroniseren.

<a id="-2134376423"></a>**De synchronisatie is mislukt vanwege een probleem met het pad naar het server eindpunt.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimaal)** | -2134376423 |
| **Fout reeks** | ECS_E_SYNC_INVALID_PATH |
| **Herstel vereist** | Ja |

Zorg ervoor dat het pad bestaat, zich op een lokaal NTFS-volume bevindt en geen reparsepunt of bestaand server eindpunt is.

<a id="-2134375817"></a>**De synchronisatie is mislukt, omdat de versie van het filter stuur programma niet compatibel is met de agent versie**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimaal)** | -2134375817 |
| **Fout reeks** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat de geladen versie van het filter stuur programma voor Cloud lagen (StorageSync. sys) niet compatibel is met de FileSyncSvc-service (Storage Sync agent). Als de Azure File Sync-agent is bijgewerkt, start u de server opnieuw op om de installatie te volt ooien. Als de fout blijft optreden, verwijdert u de agent, start u de server opnieuw op en installeert u de Azure File Sync agent opnieuw.

<a id="-2134376373"></a>**De service is momenteel niet beschikbaar.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimaal)** | -2134376373 |
| **Fout reeks** | ECS_E_SERVICE_UNAVAILABLE |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat de Azure File Sync-Service niet beschikbaar is. Deze fout wordt automatisch opgelost wanneer de Azure File Sync-service weer beschikbaar is.

<a id="-2146233088"></a>**De synchronisatie is mislukt vanwege een uitzonde ring.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimaal)** | -2146233088 |
| **Fout reeks** | COR_E_EXCEPTION |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat de synchronisatie is mislukt vanwege een uitzonde ring. Als de fout gedurende enkele uren blijft bestaan, moet u een ondersteunings aanvraag maken.

<a id="-2134364045"></a>**De synchronisatie is mislukt, omdat er een failover voor het opslag account is uitgevoerd naar een andere regio.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimaal)** | -2134364045 |
| **Fout reeks** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het opslag account is overgeschakeld naar een andere regio. Azure File Sync biedt geen ondersteuning voor de functie Failover van het opslag account. Er mag geen failover worden uitgevoerd voor opslag accounts met Azure-bestands shares die worden gebruikt als Cloud-eind punten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwachte gegevens verlies in het geval van nieuwe gelaagde bestanden. U kunt dit probleem oplossen door het opslag account te verplaatsen naar de primaire regio.

<a id="-2134375922"></a>**De synchronisatie is mislukt vanwege een tijdelijk probleem met de synchronisatie database.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimaal)** | -2134375922 |
| **Fout reeks** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Herstel vereist** | Nee |

Deze fout treedt op vanwege een intern probleem met de synchronisatie database. Deze fout wordt automatisch opgelost wanneer de Azure File Sync wanneer de synchronisatie wordt uitgevoerd. Als deze fout gedurende een verlengde periode blijft bestaan, kunt u een ondersteunings aanvraag maken. we nemen contact met u op om dit probleem op te lossen.

<a id="-2134364024"></a>**De synchronisatie is mislukt vanwege een wijziging in de Azure Active Directory Tenant**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimaal)** | -2134364024 | 
| **Fout reeks** | ECS_E_INVALID_AAD_TENANT |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat Azure File Sync op dit moment geen ondersteuning biedt voor het verplaatsen van het abonnement naar een andere Azure Active Directory-Tenant.
 
Voer een van de volgende opties uit om dit probleem op te lossen:

- **Optie 1 (aanbevolen)** : Het abonnement opnieuw naar de oorspronkelijke Azure Active Directory Tenant verplaatsen
- **Optie 2**: De huidige synchronisatie groep verwijderen en opnieuw maken. Als Cloud lagen zijn ingeschakeld op het server eindpunt, verwijdert u de synchronisatie groep en voert u de stappen uit die worden beschreven in de [sectie Cloud lagen]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) om de zwevende gelaagde bestanden te verwijderen voordat u de synchronisatie groepen opnieuw maakt. 

<a id="-2134364010"></a>**De synchronisatie is mislukt omdat de firewall en de uitzonde ring voor het virtuele netwerk niet zijn geconfigureerd**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimaal)** | -2134364010 | 
| **Fout reeks** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Herstel vereist** | Ja |

Deze fout treedt op als de instellingen voor de firewall en het virtuele netwerk zijn ingeschakeld voor het opslag account en de uitzonde ring ' vertrouwde micro soft-Services voor toegang tot dit opslag account toestaan ' niet is ingeschakeld. Volg de stappen die worden beschreven in de sectie instellingen voor [firewall en virtueel netwerk configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatie handleiding om dit probleem op te lossen.

<a id="-2147024891"></a>**De synchronisatie is mislukt, omdat de machtigingen voor de map System Volume Information onjuist zijn.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimaal)** | -2147024891 |
| **Fout reeks** | ERROR_ACCESS_DENIED |
| **Herstel vereist** | Ja |

Deze fout kan optreden als het NT AUTHORITY\SYSTEM-account geen machtigingen heeft voor de map System Volume Information op het volume waar het server eindpunt zich bevindt. Opmerking: als afzonderlijke bestanden niet kunnen worden gesynchroniseerd met ERROR_ACCESS_DENIED, voert u de stappen uit die worden beschreven in de sectie [problemen oplossen per bestand/directory-synchronisatie fouten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Down load het [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) -hulp programma.
2. Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om een opdracht prompt te starten met het systeem account: **PsExec. exe-i-s-d-cmd** 
3. Voer vanaf de opdracht prompt die wordt uitgevoerd onder het systeem-account de volgende opdracht uit om te bevestigen dat het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map systeem volumegegevens: **cacls "stationsletter: \ System Volume Information"/T/c**
4. Als het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information, voert u de volgende opdracht uit: **cacls "stationsletter: \ System Volume Information"/T/E/g "NT AUTHORITY\SYSTEM: F"**
    - Als stap #4 mislukt met geweigerde toegang, voert u de volgende opdracht uit om eigenaar te worden van de map System Volume Information en herhaalt u stap #4: **takeown/A/R/f "stationsletter: \ System Volume Information"**

<a id="-2134375810"></a>**De synchronisatie is mislukt, omdat de Azure-bestands share is verwijderd en opnieuw is gemaakt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimaal)** | -2134375810 |
| **Fout reeks** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat Azure File Sync geen ondersteuning biedt voor het verwijderen en opnieuw maken van een Azure-bestands share in dezelfde synchronisatie groep. 

U kunt dit probleem oplossen door de volgende stappen uit te voeren om de synchronisatie groep te verwijderen en opnieuw te maken:

1. Verwijder alle server eindpunten in de synchronisatie groep.
2. Verwijder het Cloud eindpunt. 
3. De synchronisatie groep verwijderen.
4. Als Cloud lagen zijn ingeschakeld op een server eindpunt, verwijdert u de zwevende gelaagde bestanden op de server door de stappen uit te voeren die worden beschreven in de gelaagde [bestanden niet toegankelijk zijn op de server na het verwijderen van een server eindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) sectie.
5. Maak de synchronisatie groep opnieuw.

### <a name="common-troubleshooting-steps"></a>Veelvoorkomende stappen voor probleem oplossing
<a id="troubleshoot-storage-account"></a>**Controleer of het opslag account bestaat.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Ga naar de synchronisatie groep binnen de opslag synchronisatie service.
2. Selecteer het eind punt van de cloud in de synchronisatie groep.
3. Noteer de naam van de Azure-bestands share in het geopende deel venster.
4. Selecteer het gekoppelde opslag account. Als deze koppeling mislukt, is het opslag account waarnaar wordt verwezen, verwijderd.
    ![Een scherm afbeelding met het detail venster van het Cloud eindpunt met een koppeling naar het opslag account.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Zorg ervoor dat de Azure-bestands share bestaat.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klik op **overzicht** in de linkerkolom met inhoud om terug te keren naar de pagina voor het hoofd-opslag account.
2. Selecteer **bestanden** om de lijst met bestands shares weer te geven.
3. Controleer of de bestands share waarnaar wordt verwezen door het Cloud-eind punt wordt weer gegeven in de lijst met bestands shares (u hebt dit in stap 1 hierboven vermeld).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Zorg ervoor dat Azure File Sync toegang heeft tot het opslag account.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klik op **toegangs beheer (IAM)** in de linker inhouds opgave.
1. Klik op het tabblad roltoewijzingen aan de lijst met gebruikers en toepassingen (*service*-principals) die toegang hebben tot uw opslag account.
1. Controleer of **Hybrid File Sync Service** wordt weer gegeven in de lijst met de rol **lezer en gegevens toegang** . 

    ![Een scherm afbeelding van de Service-Principal Hybrid File Sync Service op het tabblad toegangs beheer van het opslag account](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Als de **Hybrid File Sync-Service** niet wordt weer gegeven in de lijst, voert u de volgende stappen uit:

    - Klik op **Toevoegen**.
    - Selecteer in het veld **rol** de optie **lezer en gegevens toegang**.
    - In het veld **selecteren** typt u **Hybrid File Sync Service**, selecteert u de rol en klikt u op **Opslaan**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hoe kan ik voor komen dat gebruikers bestanden met niet-ondersteunde tekens op de server maken?
U kunt bestands [Server bron beheer (FSRM)-bestands controles](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) gebruiken om bestanden te blok keren waarbij niet-ondersteunde tekens in hun namen worden gemaakt op de server. Mogelijk moet u dit doen met behulp van Power shell als het meren deel van de niet-ondersteunde tekens niet kan worden afgedrukt en dus moet u de hexadecimale weer gaven als tekens eerst converteren.

Maak eerst een bestands groep FSRM met de [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). In dit voor beeld wordt de groep zodanig gedefinieerd dat deze slechts twee van de niet-ondersteunde tekens bevat, maar u kunt zo veel van de tekens opnemen die nodig zijn in uw bestands groep.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Wanneer u een FSRM-bestands groep hebt gedefinieerd, kunt u een FSRM-bestands scherm maken met de cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Houd er rekening mee dat bestands controles alleen kunnen worden gebruikt om het maken van tekens te blok keren die niet door Azure File Sync worden ondersteund. Als bestands controles in andere scenario's worden gebruikt, probeert synchronisatie voortdurend de bestanden te downloaden van de Azure-bestands share naar de server en wordt deze geblokkeerd als gevolg van de bestands controle, wat resulteert in hoge gegevens uitvoer. 

## <a name="cloud-tiering"></a>Cloudopslaglagen 
Er zijn twee paden voor fouten in Cloud lagen:

- Bestanden kunnen niet op de juiste manier worden gelaagd. Dit betekent dat Azure File Sync geen geslaagde poging om een bestand te tierren naar Azure Files.
- Bestanden kunnen niet worden ingetrokken. Dit betekent dat de Azure File Sync bestandssysteem filter (StorageSync. sys) geen gegevens kan downloaden wanneer een gebruiker toegang probeert te krijgen tot een bestand dat is gelaagd.

Er zijn twee hoofd klassen van fouten die kunnen optreden via een fout-pad:

- Fouten in de Cloud opslag
    - *Problemen met de beschik baarheid van tijdelijke opslag Services*. Zie voor meer informatie de [Service Level Agreement (Sla) voor Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - De *Azure-bestands share*is niet toegankelijk. Deze fout treedt doorgaans op wanneer u de Azure-bestands share verwijdert wanneer deze nog steeds een Cloud-eind punt in een synchronisatie groep is.
    - Het *opslag account*is niet toegankelijk. Deze fout treedt doorgaans op wanneer u het opslag account verwijdert terwijl het nog een Azure-bestands share is die een Cloud-eind punt in een synchronisatie groep is. 
- Server fouten 
  - *Azure file sync bestandssysteem filter (StorageSync. sys) is niet geladen*. Als u wilt reageren op aanvragen voor lagen/terughalen, moet het Azure File Sync bestandssysteem filter worden geladen. Het filter dat niet wordt geladen, kan om verschillende redenen plaatsvinden, maar de meest voorkomende reden is dat een beheerder deze hand matig heeft verwijderd. Het Azure File Sync-bestandssysteem filter moet altijd worden geladen om Azure File Sync goed te kunnen functioneren.
  - *Ontbrekend, beschadigd of anderszins gebroken*reparsepunt. Een reparsepunt is een speciale gegevens structuur voor een bestand dat bestaat uit twee delen:
    1. Een reparse-code, die het besturings systeem aangeeft dat het Azure File Sync bestandssysteem filter (StorageSync. sys) een bepaalde actie moet uitvoeren op IO naar het bestand. 
    2. Reparsegegevens, waarmee wordt aangegeven dat het bestandssysteem de URI van het bestand op het gekoppelde cloud eindpunt (de Azure-bestands share) wordt gefilterd. 
        
       De meest voorkomende manier waarop een reparsepunt beschadigd kan raken, is als een beheerder de tag of de gegevens ervan probeert te wijzigen. 
  - *Problemen met de netwerk verbinding*. Als u een bestand wilt ordenen of intrekken, moet de server verbinding hebben met internet.

De volgende secties geven informatie over het oplossen van problemen met Cloud lagen en bepalen of een probleem een probleem met de Cloud opslag of een server probleem is.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>De activiteit voor het bijhouden van lagen op een server bewaken  
Gebruik gebeurtenis-ID 9003, 9016 en 9029 in het telemetrie-gebeurtenis logboek (bevindt zich onder toepassingen en Services\Microsoft\FileSync\Agent in Logboeken) om de activiteit voor het bijhouden van lagen op een server te bewaken.

- Gebeurtenis-ID 9003 biedt fout distributie voor een server eindpunt. Bijvoorbeeld totaal aantal fouten, error code, enzovoort. Opmerking: er wordt één gebeurtenis geregistreerd per fout code.
- Gebeurtenis-ID 9016 bevat spook resultaten voor een volume. Bijvoorbeeld: percentage vrije ruimte is het aantal bestanden dat is gedupliceerd in de sessie, het aantal bestanden dat niet kan worden Ghost, enzovoort.
- Gebeurtenis-ID 9029 bevat informatie over ghost-sessies voor een server eindpunt. Bijvoorbeeld het aantal bestanden dat is geprobeerd in de sessie, het aantal bestanden dat is gelaagd in de sessie, het aantal bestanden dat al is gelaagd, enzovoort.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Inhaal activiteiten bewaken op een server
Gebruik gebeurtenis-ID 9005, 9006, 9009 en 9059 in het telemetrie-gebeurtenis logboek (onder toepassingen en Services\Microsoft\FileSync\Agent in Logboeken) voor het bewaken van inhaal activiteiten op een server.

- Gebeurtenis-ID 9005 biedt betrouw baarheid van een server eindpunt. Bijvoorbeeld: totale aantal geopende bestanden, unieke bestanden met mislukte toegang, enzovoort.
- Gebeurtenis-ID 9006 biedt fout bij het intrekken van fouten voor een server eindpunt. Bijvoorbeeld totaal aantal mislukte aanvragen, error code, enzovoort. Opmerking: er wordt één gebeurtenis geregistreerd per fout code.
- Gebeurtenis-ID 9009 biedt informatie over het intrekken van sessies voor een server eindpunt. Bijvoorbeeld, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- Met gebeurtenis-ID 9059 kan de toepassing worden gedistribueerd voor een server eindpunt. Bijvoorbeeld ShareId, toepassings naam en TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Problemen oplossen met bestanden die niet kunnen worden getierd
Als bestanden niet naar Azure Files kunnen worden gelaagd:

1. Bekijk in Logboeken de telemetrie-, operationele en diagnostische gebeurtenis logboeken, die zich bevinden onder toepassingen en Services\Microsoft\FileSync\Agent. 
   1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.

      > [!NOTE]
      > Een bestand moet worden gesynchroniseerd met een Azure-bestands share voordat het kan worden getierd.

   2. Controleer of de server verbinding met internet heeft. 
   3. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync. sys en StorageSyncGuard. sys) worden uitgevoerd:
       - Voer `fltmc`uit vanaf een opdracht prompt met verhoogde bevoegdheid. Controleer of de bestandssysteem filter Stuur Programma's StorageSync. sys en StorageSyncGuard. sys worden weer gegeven.

> [!NOTE]
> Gebeurtenis-ID 9003 wordt eenmaal per uur vastgelegd in het logboek voor telemetrie als een bestand niet kan worden gelaagd (één gebeurtenis wordt per fout code geregistreerd). De operationele en diagnostische gebeurtenis logboeken moeten worden gebruikt als er aanvullende informatie nodig is om een probleem op te sporen.

### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Problemen oplossen met bestanden die niet worden ingetrokken  
Als bestanden niet kunnen worden ingetrokken:
1. Bekijk in Logboeken de telemetrie-, operationele en diagnostische gebeurtenis logboeken, die zich bevinden onder toepassingen en Services\Microsoft\FileSync\Agent.
    1. Controleer of de bestanden aanwezig zijn in de Azure-bestands share.
    2. Controleer of de server verbinding met internet heeft. 
    3. Open de MMC-module Services en controleer of de opslag synchronisatie Agent-service (FileSyncSvc) wordt uitgevoerd.
    4. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync. sys en StorageSyncGuard. sys) worden uitgevoerd:
        - Voer `fltmc`uit vanaf een opdracht prompt met verhoogde bevoegdheid. Controleer of de bestandssysteem filter Stuur Programma's StorageSync. sys en StorageSyncGuard. sys worden weer gegeven.

> [!NOTE]
> Gebeurtenis-ID 9006 wordt eenmaal per uur vastgelegd in het logboek voor telemetrie als een bestand niet kan worden ingetrokken (één gebeurtenis wordt per fout code geregistreerd). De operationele en diagnostische gebeurtenis logboeken moeten worden gebruikt als er aanvullende informatie nodig is om een probleem op te sporen.

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een server eindpunt
Gelaagde bestanden op een server worden ontoegankelijk als de bestanden niet worden ingetrokken voordat een server eindpunt wordt verwijderd.

Fouten die zijn vastgelegd als gelaagde bestanden niet toegankelijk zijn
- Bij het synchroniseren van een bestand wordt fout code-2147942467 (0x80070043-ERROR_BAD_NET_NAME) vastgelegd in het gebeurtenis logboek van ItemResults
- Bij het terugroepen van een bestand wordt de fout code 2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) vastgelegd in het gebeurtenis logboek van RecallResults

U kunt de toegang tot uw gelaagde bestanden mogelijk herstellen als aan de volgende voor waarden wordt voldaan:
- Server eindpunt is verwijderd in de afgelopen 30 dagen
- Het Cloud-eind punt is niet verwijderd 
- De bestands share is niet verwijderd
- De synchronisatie groep is niet verwijderd

Als aan de bovenstaande voor waarden wordt voldaan, kunt u de toegang tot de bestanden op de server herstellen door het server eindpunt opnieuw te maken op hetzelfde pad op de server binnen 30 dagen in dezelfde synchronisatie groep. 

Als niet aan deze voor waarden wordt voldaan, is het herstellen van de toegang niet mogelijk omdat deze gelaagde bestanden op de server nu zwevend zijn. Volg de onderstaande instructies om de zwevende gelaagde bestanden te verwijderen.

**Opmerkingen**
- Wanneer gelaagde bestanden niet toegankelijk zijn op de server, moet het volledige bestand nog steeds toegankelijk zijn als u rechtstreeks toegang hebt tot de Azure-bestands share.
- Als u zwevende gelaagde bestanden in de toekomst wilt voor komen, volgt u de stappen in [een server eindpunt verwijderen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) bij het verwijderen van een server eindpunt.

<a id="get-orphaned"></a>**De lijst met zwevende gelaagde bestanden ophalen** 

1. Controleer of Azure File Sync agent versie v 5.1 of hoger is geïnstalleerd.
2. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sla het uitvoer bestand OrphanTieredFiles. txt op in case bestanden moeten worden hersteld na het verwijderen van de back-up.

<a id="remove-orphaned"></a>**Zwevende gelaagde bestanden verwijderen** 

*Optie 1: De zwevende gelaagde bestanden verwijderen*

Met deze optie worden de zwevende gelaagde bestanden op de Windows-Server verwijderd, maar moet het server eindpunt worden verwijderd als dit bestaat als gevolg van recreatie na 30 dagen of is verbonden met een andere synchronisatie groep. Er treden bestands conflicten op als bestanden worden bijgewerkt op de Windows-Server of de Azure-bestands share voordat het server-eind punt opnieuw wordt gemaakt.

1. Controleer of Azure File Sync agent versie v 5.1 of hoger is geïnstalleerd.
2. Back-up maken van de Azure-bestands share en de locatie van het server eindpunt.
3. Verwijder het server eindpunt in de synchronisatie groep (indien aanwezig) door de stappen te volgen die worden beschreven in [een server eindpunt verwijderen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Als het server-eind punt niet is verwijderd voordat u de cmdlet Remove-StorageSyncOrphanedTieredFiles gebruikt, wordt het volledige bestand in de Azure-bestands share verwijderd als u het zwevende gelaagte bestand op de server verwijdert. 

4. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Sla het uitvoer bestand OrphanTieredFiles. txt op in case bestanden moeten worden hersteld na het verwijderen van de back-up.
6. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden te verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Opmerkingen** 
- Gelaagde bestanden die op de server zijn gewijzigd en niet zijn gesynchroniseerd met de Azure-bestands share, worden verwijderd.
- Gelaagde bestanden die toegankelijk zijn (niet zwevend), worden niet verwijderd.
- Niet-gelaagde bestanden blijven op de server.

7. Optioneel: Maak het server eindpunt opnieuw als u dit hebt verwijderd in stap 3.

*Optie 2: Koppel de Azure-bestands share en kopieer de bestanden die zich lokaal op de server bevinden*

Voor deze optie moet het server eindpunt niet worden verwijderd, maar er is voldoende schijf ruimte nodig om de volledige bestanden lokaal te kopiëren.

1. [Koppel](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) de Azure-bestands share op de Windows-Server met zwevende gelaagde bestanden.
2. Voer de volgende Power shell-opdrachten uit om zwevende gelaagde bestanden weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Gebruik het uitvoer bestand OrphanTieredFiles. txt om zwevende gelaagde bestanden op de server te identificeren.
4. De zwevende gelaagde bestanden overschrijven door het volledige bestand van de Azure-bestands share te kopiëren naar de Windows-Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Problemen oplossen met bestanden die onverwacht worden ingetrokken op een server  
Anti virus-, back-up-en andere toepassingen die grote aantallen bestanden lezen, veroorzaken onbedoelde aanroepen, tenzij ze het kenmerk offline overs Laan respecteren en het lezen van de inhoud van die bestanden overs Laan. Als u offlinebestanden overslaat voor producten die ondersteuning bieden voor deze optie, voorkomt u dat er onbedoeld wordt teruggehaald tijdens bijvoorbeeld antivirusscans en back-uptaken.

Vraag uw softwareleverancier hoe de oplossing kan worden geconfigureerd zodat offlinebestanden niet meer worden gelezen.

Onbedoelde oproepen kunnen ook in andere scenario's optreden, zoals wanneer u bestanden bekijkt in Verkenner. Als u in Verkenner op de server een map opent met bestanden in cloudlagen, kan dat resulteren in onbedoeld terughalen. Dit is nog waarschijnlijker als er een antivirusoplossing is ingeschakeld op de server.

> [!NOTE]
>Gebruik gebeurtenis-ID 9059 in het logboek voor telemetrie om te bepalen welke toepassing wordt gebruikt voor het terughalen. Deze gebeurtenis voorziet in het terughalen van toepassingen voor een server eindpunt en wordt eenmaal per uur geregistreerd.

## <a name="general-troubleshooting"></a>Algemene probleem oplossing
Als u problemen ondervindt met Azure File Sync op een server, moet u eerst de volgende stappen uitvoeren:
1. In Logboeken raadpleegt u de logboeken met betrekking tot telemetrie, operationele en diagnose.
    - Problemen met synchronisatie, lagen en intrekken worden vastgelegd in de telemetrie-, diagnostische en operationele gebeurtenis Logboeken onder toepassingen en Services\Microsoft\FileSync\Agent.
    - Problemen met betrekking tot het beheren van een server (bijvoorbeeld configuratie-instellingen) worden vastgelegd in de operationele en diagnostische gebeurtenis Logboeken onder toepassingen en Services\Microsoft\FileSync\Management.
2. Controleer of de Azure File Sync-Service wordt uitgevoerd op de server:
    - Open de MMC-module Services en controleer of de opslag synchronisatie Agent-service (FileSyncSvc) wordt uitgevoerd.
3. Controleer of de Azure File Sync-filter Stuur programma's (StorageSync. sys en StorageSyncGuard. sys) worden uitgevoerd:
    - Voer `fltmc`uit vanaf een opdracht prompt met verhoogde bevoegdheid. Controleer of de bestandssysteem filter Stuur Programma's StorageSync. sys en StorageSyncGuard. sys worden weer gegeven.

Als het probleem niet is opgelost, voert u het hulp programma AFSDiag uit:
1. Maak een map waar de uitvoer van de AFSDiag wordt opgeslagen (bijvoorbeeld C:\Output).
    > [!NOTE]
    >AFSDiag verwijdert alle inhoud in de uitvoermap voordat logboeken worden verzameld. Geef een uitvoer locatie op die geen gegevens bevat.
2. Open een Power shell-venster met verhoogde bevoegdheden en voer de volgende opdrachten uit (druk na elke opdracht op ENTER):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Voer voor het traceer niveau van de Azure File Sync-kernelmodus **1** in (tenzij anders opgegeven, om meer uitgebreide traceringen te maken) en druk vervolgens op ENTER.
4. Voor de Azure File Sync traceer niveau van de gebruikers modus voert u **1** in (tenzij anders opgegeven, om meer uitgebreide traceringen te maken) en druk vervolgens op ENTER.
5. Reproduceer het probleem. Wanneer u klaar bent, voert u **D**in.
6. Een zip-bestand dat Logboeken en tracerings bestanden bevat, wordt opgeslagen in de uitvoermap die u hebt opgegeven.

## <a name="see-also"></a>Zie ook
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Veelgestelde vragen over Azure Files](storage-files-faq.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
