---
title: Problemen met Azure File Sync oplossen | Microsoft Documenten
description: Veelvoorkomende problemen met Azure File Sync oplossen.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: d46f513fccf9921d4cf47835bc9d5be4c6ffe241
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607496"
---
# <a name="troubleshoot-azure-file-sync"></a>Problemen met Azure Files Sync oplossen
Gebruik Azure File Sync om de bestandsshares van uw organisatie in Azure-bestanden te centraliseren, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, NFS en FTPS. Je zoveel caches hebben als je nodig hebt over de hele wereld.

Dit artikel is ontworpen om u te helpen problemen op te lossen en op te lossen die u mogelijk tegenkomt met uw Azure File Sync-implementatie. We beschrijven ook hoe belangrijke logboeken van het systeem kunnen worden verzameld als een dieper onderzoek naar het probleem vereist is. Als u het antwoord op uw vraag niet ziet, u contact met ons opnemen via de volgende kanalen (in escalerende volgorde):

1. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [UserVoice van Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft-ondersteuning. Als u een nieuw ondersteuningsverzoek wilt maken, selecteert u in de Azure-portal op het tabblad **Help** de knop **Help + ondersteuning** en selecteert u Vervolgens Nieuw **ondersteuningsverzoek**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, cloudtiering, enz.). Moet ik mijn servereindpunt verwijderen en opnieuw maken?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Installatie van agent en serverregistratie
<a id="agent-installation-failures"></a>**Problemen met de installatievan agentproblemen oplossen**  
Als de installatie van de Azure File Sync-agent mislukt, voert u bij een opdrachtprompt met verhoogde bevoegdheid de volgende opdracht uit om logboekregistratie tijdens de installatie van de agent in te schakelen:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Controleer installer.log om de oorzaak van de installatiefout te bepalen.

<a id="agent-installation-on-DC"></a>**Agent-installatie mislukt op Active Directory Domain Controller**  
Als u de synchronisatieagent probeert te installeren op een Active Directory-domeincontroller waarbij de eigenaar van de PDC-rol zich bevindt op een Windows Server 2008 R2- of lager-versie van het besturingssysteem, u het probleem raken waarbij de synchronisatieagent niet wordt geïnstalleerd.

Als u de PDC-rol wilt overbrengen naar een andere domeincontroller met Windows Server 2012 R2 of recenter, installeert u vervolgens synchronisatie.

<a id="parameter-is-incorrect"></a>**Toegang tot een volume op Windows Server 2012 R2 mislukt met fout: de parameter is onjuist**  
Na het maken van een servereindpunt op Windows Server 2012 R2 treedt de volgende fout op bij het openen van het volume:

driveletter:\ is niet toegankelijk.  
De parameter is onjuist.

Als u het wilt oplossen, installeert u de nieuwste updates voor Windows Server 2012 R2 en start u de server opnieuw.

<a id="server-registration-missing-subscriptions"></a>**Serverregistratie vermeldt niet alle Azure-abonnementen**  
Bij het registreren van een server met ServerRegistration.exe ontbreken abonnementen wanneer u op de vervolgkeuzelijst Azure-abonnement klikt.

Dit probleem treedt op omdat ServerRegistration.exe momenteel geen multitenant-omgevingen ondersteunt. Dit probleem wordt opgelost in een toekomstige Azure File Sync-agent-update.

Als u dit probleem wilt oplossen, gebruikt u de volgende PowerShell-opdrachten om de server te registreren:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Serverregistratie geeft het volgende bericht weer: "Vereisten ontbreken"**  
Dit bericht wordt weergegeven als de Az- of AzureRM PowerShell-module niet is geïnstalleerd op PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe ondersteunt PowerShell 6.x niet. U de cmdlet Register-AzStorageServer op PowerShell 6.x gebruiken om de server te registreren.

Voer de volgende stappen uit om de Az- of AzureRM-module op PowerShell 5.1 te installeren:

1. Typ **powershell** van een verhoogde opdrachtprompt en druk op enter.
2. Installeer de nieuwste Az- of AzureRM-module door de documentatie te volgen:
    - [Az-module (vereist .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM-module]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren met een opslagsynchronisatieservice.

<a id="server-already-registered"></a>**Serverregistratie geeft het volgende bericht weer: "Deze server is al geregistreerd"** 

![Een schermafbeelding van het dialoogvenster Serverregistratie met het foutbericht 'server is al geregistreerd'](media/storage-sync-files-troubleshoot/server-registration-1.png)

Dit bericht wordt weergegeven als de server eerder is geregistreerd bij een Storage Sync Service. Als u de server wilt uitschrijven van de huidige Opslagsynchronisatieservice en vervolgens wilt registreren bij een nieuwe Opslagsynchronisatieservice, voert u de stappen uit die worden beschreven in [Het registreren van een server met Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Als de server niet wordt vermeld onder **Geregistreerde servers** in de Opslagsynchronisatieservice, voert u op de server die u wilt uitschrijven uit de volgende PowerShell-opdrachten uit:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Als de server deel uitmaakt van een cluster, u de optionele parameter *Reset-StorageSyncServer -CleanClusterRegistration* gebruiken om ook de clusterregistratie te verwijderen.

<a id="web-site-not-trusted"></a>**Wanneer ik een server registreer, zie ik tal van "website niet vertrouwde" reacties. Waarom?**  
Dit probleem treedt op wanneer het **beveiligingsbeleid voor verbeterde internetexplorer** is ingeschakeld tijdens de serverregistratie. Zie [Windows Server voorbereiden met Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) en Azure File [Sync](storage-sync-files-deployment-guide.md)voor meer informatie over het correct uitschakelen van het **beveiligingsbeleid voor Verbeterde Internet Explorer.**

<a id="server-registration-missing"></a>**Server wordt niet vermeld onder geregistreerde servers in de Azure-portal**  
Als een server niet wordt vermeld onder **Geregistreerde servers** voor een opslagsynchronisatieservice:
1. Meld u aan bij de server die u wilt registreren.
2. Open Verkenner en ga naar de installatiemap voor Storage Sync Agent (de standaardlocatie is C:\Program Files\Azure\StorageSyncAgent). 
3. Voer ServerRegistration.exe uit en voltooi de wizard om de server te registreren met een opslagsynchronisatieservice.

## <a name="sync-group-management"></a>Groepsbeheer synchroniseren
<a id="cloud-endpoint-using-share"></a>**Cloudendpoint-creatie mislukt met deze fout: "De opgegeven Azure FileShare wordt al gebruikt door een ander CloudEndpoint"**  
Deze fout treedt op wanneer de Azure-bestandsshare al wordt gebruikt door een ander cloudeindpunt. 

Als u dit bericht ziet en de Azure-bestandsshare momenteel niet wordt gebruikt door een eindpunt in de cloud, voert u de volgende stappen uit om de metagegevens van Azure File Sync in het Azure-bestandsshare te wissen:

> [!Warning]  
> Als u de metagegevens van een Azure-bestandsshare verwijderen die momenteel wordt gebruikt door een eindpunt in de cloud, worden azure-bestandssynchronisatiebewerkingen mislukt. 

1. Ga in de Azure-portal naar uw Azure-bestandsshare.  
2. Klik met de rechtermuisknop op de Azure-bestandsshare en selecteer **metagegevens bewerken**.
3. Klik met de rechtermuisknop op **Synchronisatieservice**en selecteer **Verwijderen**.

<a id="cloud-endpoint-authfailed"></a>**Cloud endpoint creation mislukt, met deze fout: "Autorisatiemislukt"**  
Deze fout treedt op als uw gebruikersaccount niet voldoende rechten heeft om een eindpunt in de cloud te maken. 

Als u een eindpunt in de cloud wilt maken, moet uw gebruikersaccount de volgende machtigingen voor Microsoft Autorisatie hebben:  
* Lezen: Roldefinitie krijgen
* Schrijven: aangepaste roldefinitie maken of bijwerken
* Lezen: Roltoewijzing krijgen
* Schrijven: Roltoewijzing maken

De volgende ingebouwde rollen hebben de vereiste machtigingen voor Microsoft Autorisatie:  
* Eigenaar
* Beheerder van gebruikerstoegang

Ga als volgt te werk om te bepalen of uw gebruikersaccountrol over de vereiste machtigingen beschikt:  
1. Selecteer **resourcegroepen**in de Azure-portal .
2. Selecteer de brongroep waarin het opslagaccount zich bevindt en selecteer **vervolgens Toegangsbeheer (IAM).**
3. Selecteer het tabblad **Roltoewijzingen.**
4. Selecteer de **rol** (bijvoorbeeld Eigenaar of Bijdrager) voor uw gebruikersaccount.
5. Selecteer **Microsoft Authorization**in de lijst **Resourceprovider** . 
    * **Roltoewijzing** moet **lees-** en **schrijfmachtigingen** hebben.
    * **Roldefinitie** moet **lees-** en **schrijfmachtigingen** hebben.

<a id="-2134375898"></a>**Serverendpoint creation mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2134375898 of 0x80c80226)**  
Deze fout treedt op als het pad naar het servereindpunt zich op het systeemvolume bevindt en opslag in cloudlagen is ingeschakeld. Cloud-opslaglagen worden niet ondersteund op het systeemvolume. Als u een servereindpunt op het systeemvolume wilt maken, schakelt u opslag in cloudlagen uit bij het maken van het servereindpunt.

<a id="-2147024894"></a>**Serverendpoint creatie mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2147024894 of 0x80070002)**  
Deze fout treedt op als het opgegeven pad naar het servereindpunt niet geldig is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Houd er rekening mee dat Azure File Sync geen toegewezen stations ondersteunt als een eindpuntpad voor servers.

<a id="-2134375640"></a>**Serverendpoint creatie mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2134375640 of 0x80c80328)**  
Deze fout treedt op als het opgegeven servereindpuntpad geen NTFS-volume is. Controleer of het opgegeven pad naar het servereindpunt een lokaal gekoppeld NTFS-volume is. Houd er rekening mee dat Azure File Sync geen toegewezen stations ondersteunt als een eindpuntpad voor servers.

<a id="-2134347507"></a>**Serverendpoint creatie mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2134347507 of 0x80c8710d)**  
Deze fout treedt op omdat Azure File Sync geen servereindpunten ondersteunt op volumes die een gecomprimeerde map met informatie over systeemvolumes hebben. Om dit probleem op te lossen, decomprimeert u de map met informatie over het systeemvolume. Als de map met informatie over het systeemvolume de enige map is die op het volume is gecomprimeerd, voert u de volgende stappen uit:

1. Download [de Tool PsExec.](https://docs.microsoft.com/sysinternals/downloads/psexec)
2. Voer de volgende opdracht uit vanuit een opdrachtprompt met verhoogde bevoegdheid om een opdrachtprompt te starten die wordt uitgevoerd onder het systeemaccount: **PsExec.exe -i -s -d cmd**
3. Typ in de opdrachtprompt die onder het systeemaccount wordt uitgevoerd, de volgende commando's in en druk op enter:   
    **cd /d "drive letter:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**Serverendpoint creation mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2134376345 of 0x80C80067)**  
Deze fout treedt op als de limiet voor het aantal servereindpunten per server is bereikt. Azure File Sync ondersteunt momenteel maximaal 30 servereindpunten per server. Zie [Azure File Sync-schaaldoelen voor](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets)meer informatie .

<a id="-2134376427"></a>**Serverendpoint creatie mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2134376427 of 0x80c80015)**  
Deze fout treedt op als er al een ander servereindpunt wordt gesynchroniseerd met het opgegeven pad naar het servereindpunt. Azure File Sync biedt geen ondersteuning voor meerdere servereindpunten die dezelfde map of hetzelfde volume synchroniseren.

<a id="-2160590967"></a>**Serverendpoint creation mislukt, met deze fout: "MgmtServerJobFailed" (Foutcode: -2160590967 of 0x80c80077)**  
Deze fout treedt op als het servereindpuntpad zwevende gelaagde bestanden bevat. Als onlangs een servereindpunt is verwijderd, wacht u tot het opschonen van zwevende bestanden met zwevende bestanden is voltooid. Een gebeurtenis-id 6662 wordt geregistreerd bij het gebeurtenislogboek Telemetrie zodra de opschoning van zwevende bestanden is gestart. Een gebeurtenis-id 6661 wordt geregistreerd zodra de opgeschoonte zwevende bestanden met zwevende bestanden is voltooid en een servereindpunt kan opnieuw worden gemaakt met behulp van het pad. Als het maken van servereindpunten mislukt nadat een gebeurtenis-id 6661 is geregistreerd, verwijdert u de zwevende gelaagde bestanden door de stappen uit te voeren die zijn gedocumenteerd in de [gelaagde bestanden, zijn niet toegankelijk op de server nadat een servereindpuntsectie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) is verwijderd.

<a id="-2134347757"></a>**Serverendpoint verwijderen mislukt, met deze fout: "MgmtServerJobExpired" (Foutcode: -2134347757 of 0x80c87013)**  
Deze fout treedt op als de server offline is of geen netwerkverbinding heeft. Als de server niet meer beschikbaar is, moet u de registratie van de server in het portaal opheffen, waardoor de servereindpunten worden verwijderd. Als u de servereindpunten wilt verwijderen, voert u de stappen uit die worden beschreven in [Het registreren van een server met Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Kan de pagina eigenschappen van servereindpunt en het beleid voor cloudlagen niet openen**  
Dit probleem kan optreden als een beheerbewerking op het servereindpunt mislukt. Als de pagina met de eigenschappen van het servereindpunt niet wordt geopend in de Azure-portal, kan het bijwerken van servereindpunt met PowerShell-opdrachten van de server dit probleem oplossen. 

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
<a id="server-endpoint-noactivity"></a>**Servereindpunt heeft de status 'Geen activiteit' of 'In behandeling' en de serverstatus op het beheer van de geregistreerde servers is 'Verschijnt offline'.**  

Dit probleem kan optreden als het proces voor synchronisatievan opslag (AzureStorageSyncMonitor.exe) niet wordt uitgevoerd of als de server geen toegang heeft tot de Azure File Sync-service.

Bekijk gebeurtenis-id 9301 op de server die wordt weergegeven als 'Wordt weergegeven offline' in de portal om te bepalen waarom de server geen toegang heeft tot de Azure File Sync-service. 

- Als **GetNextJob is voltooid met status: 0** is geregistreerd, kan de server communiceren met de Azure File Sync-service. 
    - Open Taakbeheer op de server en controleer of het Storage Sync Monitor-proces (AzureStorageSyncMonitor.exe) actief is. Als het proces niet wordt uitgevoerd, start u om te beginnen de server opnieuw op. Als het probleem niet wordt verholpen bij het opnieuw opstarten van de server, voert u een upgrade naar de laatste versie van de Azure File Sync-[agent](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) uit. 

- Als **GetNextJob de status heeft: -2134347756** is geregistreerd, kan de server niet communiceren met de Azure File Sync-service vanwege een firewall of proxy. 
    - Als de server zich achter een firewall bevindt, controleert u of uitgaand verkeer via poort 443 is toegestaan. Als de firewall verkeer beperkt tot specifieke domeinen, controleert u of de domeinen die in de [firewalldocumentatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) worden vermeld, toegankelijk zijn.
    - Als de server zich achter een proxy bevindt, configureert u de machinebrede of app-specifieke proxy-instellingen door de stappen in de [proxydocumentatie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)te volgen.
    - Gebruik de cmdlet Test-StorageSyncNetworkConnectivity om de netwerkverbinding met de serviceeindpunten te controleren. Zie [Netwerkconnectiviteit testen met serviceeindpunten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)voor meer informatie.

- Als **GetNextJob de status heeft: -2134347764** is geregistreerd, kan de server niet communiceren met de Azure File Sync-service vanwege een verlopen of verwijderd certificaat.  
    - Voer de volgende PowerShell-opdracht uit op de server om het certificaat dat wordt gebruikt voor verificatie opnieuw in te stellen:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Servereindpunt heeft de status 'Geen activiteit' en de serverstatus op het beheer van de geregistreerde servers is 'Online'**  

Een status van servereindpunt van 'Geen activiteit' betekent dat het eindpunt van de server de synchronisatieactiviteit in de afgelopen twee uur niet heeft geregistreerd.

Zie Hoe kan ik de [voortgang van een huidige synchronisatiesessie controleren als](#how-do-i-monitor-the-progress-of-a-current-sync-session)u de huidige synchronisatieactiviteit op een server wilt controleren? .

Een servereindpunt kan enkele uren geen synchronisatieactiviteit registreren vanwege een bug of onvoldoende systeembronnen. Controleer of de nieuwste Azure File [Sync-agentversie](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) is geïnstalleerd. Als het probleem blijft bestaan, opent u een ondersteuningsverzoek.

> [!Note]  
> Als de serverstatus op het beheer van de geregistreerde servers 'Wordt offline weergegeven' is, voert u de stappen uit die zijn gedocumenteerd in het [servereindpunt en heeft de status 'Geen activiteit' of 'In behandeling' en is de serverstatus op het beheer van de geregistreerde servers 'Wordt weergegeven'](#server-endpoint-noactivity) de sectie 'Wordt weergegeven'.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Hoe lang duurt het voordat het bestand wordt gesynchroniseerd met servers in de synchronisatiegroep als ik een bestand rechtstreeks in mijn Azure-bestandsshare via SMB of via de portal heb gemaakt?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Servereindpuntstatus is enkele uren in behandeling**  
Dit probleem wordt verwacht als u een eindpunt in de cloud maakt en een Azure-bestandsshare gebruikt dat gegevens bevat. De wijzigingstaak die scant op wijzigingen in de Azure-bestandsshare moet worden voltooid voordat bestanden kunnen worden gesynchroniseerd tussen de eindpunten van de cloud en de server. De tijd om de taak te voltooien is afhankelijk van de grootte van de naamruimte in het Azure-bestandsaandeel. De status van het servereindpunt moet worden bijgewerkt zodra de taak voor wijzigingsopsomming is voltooid.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hoe controleer ik de synchronisatiestatus?
# <a name="portal"></a>[Portal](#tab/portal1)
Binnen elke synchronisatiegroep u inzoomen op de afzonderlijke servereindpunten om de status van de laatst voltooide synchronisatiesessies te zien. Een groene kolom Status en een waarde voor het niet synchroniseren van bestanden van 0 geven aan dat de synchronisatie werkt zoals verwacht. Als dit niet het geval is, zie hieronder voor een lijst met veelvoorkomende synchronisatiefouten en hoe u bestanden verwerkt die niet worden gesynchroniseerd. 

![Een schermafbeelding van de Azure-portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Ga naar de telemetrielogboeken van de server, `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`die te vinden zijn in de Logboeken op . Gebeurtenis 9102 komt overeen met een voltooide synchronisatiesessie; Voor de laatste status van synchronisatie, kijk voor de meest recente gebeurtenis met ID 9102. SyncDirection vertelt u of deze sessie een upload of download was. Als de HResult 0 is, is de synchronisatiesessie geslaagd. Een niet-nul HResult betekent dat er een fout is opgetreden tijdens het synchroniseren; zie hieronder voor een lijst met veelvoorkomende fouten. Als het Aantal PerItemError's groter is dan 0, betekent dit dat sommige bestanden of mappen niet goed zijn gesynchroniseerd. Het is mogelijk om een HResult van 0 maar een PerItemErrorCount te hebben dat groter is dan 0.

Hieronder vindt u een voorbeeld van een succesvolle upload. Omwille van de beknoptheid, slechts enkele van de waarden in elke 9102 gebeurtenis zijn hieronder vermeld. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Omgekeerd kan een mislukte upload er als volgt uitzien:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Soms mislukken synchronisatiesessies over het algemeen of hebben een niet-nul PerItemErrorCount, maar maken ze nog steeds vooruitgang, waarbij sommige bestanden met succes worden gesynchroniseerd. Dit is te zien in de velden Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes), die u vertellen hoeveel van de sessie wordt mislukt. Als u meerdere synchronisatiesessies op een rij ziet die mislukken, maar een steeds groter aantal toegepaste* hebben, moet u synchronisatietijd geven om het opnieuw te proberen voordat u een ondersteuningsticket opent.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hoe controleer ik de voortgang van een synchronisatiesessie?
# <a name="portal"></a>[Portal](#tab/portal1)
Ga binnen uw synchronisatiegroep naar het servereindpunt in kwestie en bekijk de sectie Activiteit synchroniseren om het aantal bestanden te zien dat is geüpload of gedownload in de huidige synchronisatiesessie. Houd er rekening mee dat deze status ongeveer 5 minuten wordt vertraagd en als uw synchronisatiesessie klein genoeg is om binnen deze periode te worden voltooid, wordt deze mogelijk niet gemeld in de portal. 

# <a name="server"></a>[Server](#tab/server)
Bekijk de meest recente gebeurtenis 9302 in het telemetrielogboek op de server (ga in de Logboeken van Logboeken naar Logboeken voor toepassingen en services\Microsoft\FileSync\Agent\Telemetry). Deze gebeurtenis geeft de status van de huidige synchronisatiesessie aan. TotalItemCount geeft aan hoeveel bestanden moeten worden gesynchroniseerd, AppliedCount het aantal bestanden dat tot nu toe is gesynchroniseerd en PerItemErrorCount het aantal bestanden dat niet wordt gesynchroniseerd (zie hieronder voor hoe hiermee om te gaan).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hoe weet ik of mijn servers met elkaar zijn gesynchroniseerd?
# <a name="portal"></a>[Portal](#tab/portal1)
Controleer voor elke server in een bepaalde synchronisatiegroep:
- De tijdstempels voor de laatste poging tot synchronisatie voor zowel uploaden als downloaden zijn recent.
- De status is groen voor zowel uploaden als downloaden.
- In het veld Activiteit synchroniseren worden weinig of geen bestanden weergegeven die nog worden gesynchroniseerd.
- Het veld Bestanden niet synchroniseren is 0 voor zowel uploaden als downloaden.

# <a name="server"></a>[Server](#tab/server)
Bekijk de voltooide synchronisatiesessies, die worden gekenmerkt door 9102-gebeurtenissen in het telemetriegebeurtenislogboek voor elke server (ga in de logboeken naar `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Op een bepaalde server wilt u ervoor zorgen dat de laatste upload- en downloadsessies met succes zijn voltooid. Controleer hiervoor of de HResult en PerItemErrorCount 0 zijn voor zowel uploaden als downloaden (het veld SyncDirection geeft aan of een bepaalde sessie een upload- of downloadsessie is). Houd er rekening mee dat als u een onlangs voltooide synchronisatiesessie niet ziet, er waarschijnlijk momenteel een synchronisatiesessie aan de gang is, wat te verwachten is als u zojuist een grote hoeveelheid gegevens hebt toegevoegd of gewijzigd.
2. Wanneer een server volledig up-to-date is met de cloud en geen wijzigingen heeft om in beide richtingen te synchroniseren, ziet u lege synchronisatiesessies. Deze worden aangegeven door upload- en downloadgebeurtenissen waarin alle Sync*-velden (SyncFileCount, SyncDirCount, SyncTombstoneCount en SyncSizeBytes) nul zijn, wat betekent dat er niets was om te synchroniseren. Houd er rekening mee dat deze lege synchronisatiesessies mogelijk niet plaatsvinden op servers met hoge churn omdat er altijd iets nieuws is om te synchroniseren. Als er geen synchronisatieactiviteit is, moeten ze elke 30 minuten plaatsvinden. 
3. Als alle servers up-to-date zijn met de cloud, wat betekent dat hun recente upload- en downloadsessies lege synchronisatiesessies zijn, u met redelijke zekerheid zeggen dat het systeem als geheel synchroon loopt. 
    
Houd er rekening mee dat als u wijzigingen rechtstreeks in uw Azure-bestandsshare hebt aangebracht, Azure File Sync deze wijziging niet detecteert totdat de opsomming van wijzigingen wordt uitgevoerd, wat eenmaal per 24 uur gebeurt. Het is mogelijk dat een server zal zeggen dat het up-to-date is met de cloud wanneer deze in feite recente wijzigingen mist die rechtstreeks in het Azure-bestandsaandeel zijn aangebracht. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hoe zie ik of er specifieke bestanden of mappen zijn die niet worden gesynchroniseerd?
Als het aantal PerItemErrorCount op de server of het aantal bestanden niet synchroniseren in de portal groter is dan 0 voor een bepaalde synchronisatiesessie, betekent dit dat sommige items niet worden gesynchroniseerd. Bestanden en mappen kunnen kenmerken hebben die voorkomen dat ze worden gesynchroniseerd. Deze kenmerken kunnen blijvend zijn en vereisen expliciete actie om de synchronisatie te hervatten, bijvoorbeeld het verwijderen van niet-ondersteunde tekens uit de bestands- of mapnaam. Ze kunnen ook van voorbijgaande aard zijn, wat betekent dat het bestand of de map de synchronisatie automatisch hervat; Bestanden met geopende handgrepen worden bijvoorbeeld automatisch gesynchroniseerd wanneer het bestand wordt gesloten. Wanneer de Azure File Sync-engine een dergelijk probleem detecteert, wordt een foutlogboek gemaakt dat kan worden ontleed om de items weer te geven die momenteel niet goed worden gesynchroniseerd.

Als u deze fouten wilt zien, voert u het **PowerShell-script van FileSyncErrorsReport.ps1** uit (in de agentinstallatiemap van de Azure File Sync-agent) om bestanden te identificeren die niet zijn gesynchroniseerd vanwege geopende handgrepen, niet-ondersteunde tekens of andere problemen. In het veld ItemPath ziet u de locatie van het bestand ten opzichte van de map met hoofdsynchronisatie. Zie de lijst met veelvoorkomende synchronisatiefouten hieronder voor herstelstappen.

> [!Note]  
> Als het Script FileSyncErrorsReport.ps1 wordt geretourneerd als 'Er zijn geen bestandsfouten gevonden' of geen fouten per item voor de synchronisatiegroep vermeldt, is de oorzaak:
>
>- Oorzaak 1: De laatst voltooide synchronisatiesessie heeft geen fouten per item. De portal moet binnenkort worden bijgewerkt om 0 Bestanden niet synchroniseren weer te geven. 
>   - Controleer de [gebeurtenis-id 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) in het gebeurtenislogboek Telemetrie om te bevestigen dat het aantal PerItemErrorCount 0 is. 
>
>- Oorzaak 2: Het gebeurtenislogboek ItemResults op de server die is ingepakt vanwege te veel fouten per item en het gebeurtenislogboek bevat geen fouten meer voor deze synchronisatiegroep.
>   - Als u dit probleem wilt voorkomen, vergroot u de grootte van het gebeurtenislogboek ItemResults. Het gebeurtenislogboek ItemResults is te vinden onder 'Logboeken en services\Microsoft\FileSync\Agent' in Logboeken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Problemen oplossen per bestand/map synchronisatiefouten
**ItemResults-logboek - synchronisatiefouten per item**  

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Het gelaagde bestand op de server is niet toegankelijk. Dit probleem treedt op als het gelaagde bestand niet is ingetrokken voordat een servereindpunt is verwijderd. | Zie [Trapsgewijze bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | De bestands- of mapwijziging kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout meerdere dagen blijft bestaan, gebruikt u het PowerShell-script fileSyncErrorsReport.ps1 om te bepalen waarom de afhankelijke map nog niet is gesynchroniseerd. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | De bestands- of mapwijziging kan nog niet worden gesynchroniseerd omdat een afhankelijke map nog niet is gesynchroniseerd en de synchronisatiesessie is mislukt. Dit item wordt gesynchroniseerd nadat de afhankelijke wijzigingen zijn gesynchroniseerd. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, onderzoekt u de synchronisatiesessiefout. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | De bestands- of mapnaam is ongeldig. | Wijzig de naam van het betreffende bestand of de map. Zie [Niet-ondersteunde tekens verwerken](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | De bestands- of mapnaam is ongeldig. | Wijzig de naam van het betreffende bestand of de map. Zie [Niet-ondersteunde tekens verwerken](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. Azure File Sync maakt één keer per dag een tijdelijke VSS-momentopname op de server om bestanden met geopende handgrepen te synchroniseren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Het bestand is gewijzigd, maar de wijziging is nog niet gedetecteerd door synchronisatie. Synchronisatie herstelt nadat deze wijziging is gedetecteerd. | U hoeft geen actie te ondernemen. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Het bestand is verwijderd en synchronisatie is niet op de hoogte van de wijziging. | U hoeft geen actie te ondernemen. Synchronisatie stopt met het registreren van deze fout zodra de detectie van wijzigingen detecteert dat het bestand is verwijderd. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Het verwijderen van een bestand of map kan niet worden gesynchroniseerd omdat het item al in de bestemming is verwijderd en de synchronisatie niet op de hoogte is van de wijziging. | U hoeft geen actie te ondernemen. Synchronisatie stopt met het registreren van deze fout zodra de wijzigingsdetectie wordt uitgevoerd op de bestemming en synchronisatie detecteert dat het item is verwijderd. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Het bestand of de map is overgeslagen, maar wordt tijdens de volgende synchronisatiesessie gesynchroniseerd. Als deze fout wordt gerapporteerd bij het downloaden van het item, is de bestands- of mapnaam waarschijnlijk ongeldig. | Er is geen actie vereist als deze fout wordt gerapporteerd bij het uploaden van het bestand. Als de fout wordt gerapporteerd bij het downloaden van het bestand, wijzigt u de naam van het betreffende bestand of de map. Zie [Niet-ondersteunde tekens verwerken](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) voor meer informatie. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Het maken van een bestand of map kan niet worden gesynchroniseerd omdat het item al in de bestemming bestaat en de synchronisatie niet op de hoogte is van de wijziging. | U hoeft geen actie te ondernemen. Synchronisatie stopt met het registreren van deze fout zodra de wijzigingsdetectie wordt uitgevoerd op de bestemming en de synchronisatie op de hoogte is van dit nieuwe item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Het bestand kan niet worden gesynchroniseerd omdat de limiet voor Azure-bestandsshares is bereikt. | Zie U hebt de sectie [opslaglimiet voor Azure-bestandendelen bereikt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) in de handleiding voor het oplossen van problemen om dit probleem op te lossen. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Het bestand wordt versleuteld door een niet-ondersteunde oplossing (zoals NTFS EFS). | Decoderen van het bestand en gebruik een ondersteunde versleutelingsoplossing. Zie [Oplossingen voor versleuteling](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption) in de planningshandleiding voor een lijst met ondersteunde oplossingen. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Het bestand bevindt zich in een alleen-lezen replicatiemap met DFS-R. | Het bestand bevindt zich in een alleen-lezen replicatiemap met DFS-R. Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie [planningsgids](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) voor meer informatie. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand heeft een status verwijderen in behandeling. | U hoeft geen actie te ondernemen. Het bestand wordt verwijderd zodra alle geopende bestandsgrepen zijn gesloten. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Het bestand kan niet worden gesynchroniseerd omdat de firewall- en virtuele netwerkinstellingen op het opslagaccount zijn ingeschakeld en de server geen toegang heeft tot het opslagaccount. | Voeg het IP-adres van server of virtueel netwerk toe door de stappen te volgen die zijn gedocumenteerd in de sectie [Firewall configureren en virtuele netwerkinstellingen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatiehandleiding. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Het bestand kan niet worden gesynchroniseerd omdat de grootte van de beveiligingsbeschrijving de 64 KiB-limiet overschrijdt. | U kunt dit probleem oplossen door ACE’s (Access Control Entries) voor het bestand te verwijderen om de grootte van de security descriptor te verkleinen. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Het bestand kan niet worden gesynchroniseerd als gevolg van een onverwachte fout. | Als de fout meerdere dagen blijft bestaan, opent u een ondersteuningsaanvraag. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Het bestand kan niet worden gesynchroniseerd omdat het in gebruik is. Het bestand wordt gesynchroniseerd wanneer het niet meer in gebruik is. | U hoeft geen actie te ondernemen. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand is tijdens de synchronisatie gewijzigd, dus het moet opnieuw worden gesynchroniseerd. | U hoeft geen actie te ondernemen. |
| 0x80070017 | -2147024873 | ERROR_CRC | Het bestand kan niet worden gesynchroniseerd vanwege crc-fouten. Deze fout kan optreden als een gelaagd bestand niet is teruggeroepen voordat een servereindpunt wordt verwijderd of als het bestand beschadigd is. | Zie [Gelaagde bestanden die zijn verwijderd om een servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) te verwijderen om gelaagde bestanden te verwijderen die wees zijn, om dit probleem op te lossen. Als de fout blijft optreden na het verwijderen van oprhaned gelaagde bestanden, voert u [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) uit op het volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Het bestand kan niet worden gesynchroniseerd omdat het maximum aantal conflictbestanden is bereikt. Azure File Sync ondersteunt 100 conflictbestanden per bestand. Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)over Azure File Sync voor meer informatie over bestandsconflicten. | Als u dit probleem wilt oplossen, vermindert u het aantal conflictbestanden. Het bestand wordt gesynchroniseerd zodra het aantal conflictbestanden minder dan 100 is. |

#### <a name="handling-unsupported-characters"></a>Niet-ondersteunde tekens verwerken
Als het **PowerShell-script van FileSyncErrorsReport.PS1** fouten vertoont als gevolg van niet-ondersteunde tekens (foutcode 0x8007007b of 0x80c80255), moet u de tekens die fout hebben gemaakt verwijderen of hernoemen uit de respectievelijke bestandsnamen. PowerShell zal waarschijnlijk deze tekens afdrukken als vraagtekens of lege rechthoeken, omdat de meeste van deze tekens geen standaard visuele codering hebben. De [evaluatietool](storage-sync-files-planning.md#evaluation-cmdlet) kan worden gebruikt om tekens te identificeren die niet worden ondersteund.

De onderstaande tabel bevat alle unicode-tekens die Azure File Sync nog niet ondersteunt.

| Tekenset | Aantal tekens |
|---------------|-----------------|
| <ul><li>0x0000009D (opdracht OSC-besturingssysteem)</li><li>0x00000090 (dcs-apparaatbesturingstekenreeks)</li><li>0x000008F (ss3 single shift drie)</li><li>0x00000081 (hoge octet preset)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (Arabische presentatieformulieren-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (specials) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (niet-teken)</li><li>0x0002FFFE - 0x0002FFFF = 2 (niet-teken)</li><li>0x0003FFFE - 0x0003FFFF = 2 (niet-teken)</li><li>0x0004FFFE - 0x0004FFFF = 2 (niet-teken)</li><li>0x0005FFFE - 0x0005FFFF = 2 (niet-teken)</li><li>0x0006FFFE - 0x0006FFFF = 2 (niet-teken)</li><li>0x0007FFFE - 0x0007FFFF = 2 (niet-teken)</li><li>0x0008FFFE - 0x0008FFFF = 2 (niet-teken)</li><li>0x0009FFFE - 0x0009FFFF = 2 (niet-teken)</li><li>0x000AFFFE - 0x000AFFFF = 2 (niet-teken)</li><li>0x000BFFFE - 0x000BFFFF = 2 (niet-teken)</li><li>0x000CFFFE - 0x000CFFFF = 2 (niet-teken)</li><li>0x000DFFFE - 0x000DFFFF = 2 (niet-teken)</li><li>0x000EFFFE - 0x000EFFFF = 2 (ongedefinieerd)</li><li>0x000FFFFE - 0x000FFFFF = 2 (aanvullende privé-gebruiksruimte)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Veelvoorkomende synchronisatiefouten
<a id="-2147023673"></a>**De synchronisatiesessie is geannuleerd.**  

| | |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimaal)** | -2147023673 | 
| **Fouttekenreeks** | ERROR_CANCELLED |
| **Herstel vereist** | Nee |

Synchronisatiesessies kunnen mislukken om verschillende redenen, waaronder de server die opnieuw wordt opgestart of bijgewerkt, VSS-momentopnamen, enz. Hoewel deze fout eruit ziet alsof deze follow-up vereist, is het veilig om deze fout te negeren, tenzij deze gedurende een periode van enkele uren blijft bestaan.

<a id="-2147012889"></a>**Er kon geen verbinding met de service worden gelegd.**    

| | |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimaal)** | -2147012889 | 
| **Fouttekenreeks** | WININET_E_NAME_NOT_RESOLVED |
| **Herstel vereist** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Het verzoek van de gebruiker werd beperkt door de service.**  

| | |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimaal)** | -2134376372 |
| **Fouttekenreeks** | ECS_E_USER_REQUEST_THROTTLED |
| **Herstel vereist** | Nee |

Er is geen actie vereist; de server zal het opnieuw proberen. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364043"></a>**Synchronisatie wordt geblokkeerd totdat wijzigingsdetectie het herstel van berichten voltooit**  

| | |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimaal)** | -2134364043 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Herstel vereist** | Nee |

Geen actie vereist. Wanneer een bestands- of bestandsshare (cloudeindpunt) wordt hersteld met Azure Backup, wordt de synchronisatie geblokkeerd totdat de wijzigingsdetectie is voltooid in het Azure-bestandsshare. Wijzigingsdetectie wordt onmiddellijk uitgevoerd zodra het herstellen is voltooid. Hoe lang dit duurt is afhankelijk van het aantal bestanden in de bestandsshare.

<a id="-2147216747"></a>**Synchronisatie is mislukt omdat de synchronisatiedatabase is gelost.**  

| | |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimaal)** | -2147216747 |
| **Fouttekenreeks** | SYNC_E_METADATA_INVALID_OPERATION |
| **Herstel vereist** | Nee |

Deze fout treedt doorgaans op wanneer met een back-uptoepassing een VSS-momentopname wordt gemaakt en de Sync-database is verwijderd. Als deze fout na enkele uren nog steeds optreedt, maakt u een ondersteuningsaanvraag.

<a id="-2134364065"></a>**Synchronisatie heeft geen toegang tot het Azure-bestandsaandeel dat is opgegeven in het eindpunt van de cloud.**  

| | |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimaal)** | -2134364065 |
| **Fouttekenreeks** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang kan krijgen tot de Azure-bestandsshare. Dit kan komen doordat de Azure-bestandsshare of het opslagaccount waar deze wordt gehost, niet meer bestaat. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Controleer of de Azure-bestandsshare bestaat.](#troubleshoot-azure-file-share)
3. [Controleer of Azure File Sync toegang heeft tot het opslagaccount.](#troubleshoot-rbac)
4. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Synchronisatie is mislukt omdat de aanvraag niet is gemachtigd om deze bewerking uit te voeren.**  

| | |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimaal)** | -2134351804 |
| **Fouttekenreeks** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat de Azure File Sync-agent geen toegang heeft tot het Azure-bestandsaandeel. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Controleer of de Azure-bestandsshare bestaat.](#troubleshoot-azure-file-share)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Controleer of Azure File Sync toegang heeft tot het opslagaccount.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**De gebruikte naam van het opslagaccount kan niet worden opgelost.**  

| | |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimaal)** | -2134364064 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Herstel vereist** | Ja |

1. Controleer of u de DNS-naam van de opslag van de server oplossen.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
3. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Er is een onbekende fout opgetreden tijdens het openen van het opslagaccount.**  

| | |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimaal)** | -2134364022 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Herstel vereist** | Ja |

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Controleren of de instellingen voor de firewall en het virtuele netwerk op het opslagaccount correct zijn geconfigureerd (indien ingeschakeld)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synchronisatie is mislukt omdat het opslagaccount is vergrendeld.**  

| | |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimaal)** | -2134364014 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het opslagaccount een alleen-lezen [bronvergrendeling](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)heeft. Als u dit probleem wilt oplossen, verwijdert u het alleen-lezen bronvergrendeling op het opslagaccount. 

<a id="-1906441138"></a>**Synchronisatie is mislukt vanwege een probleem met de synchronisatiedatabase.**  

| | |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimaal)** | -1906441138 |
| **Fouttekenreeks** | JET_errWriteConflict |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer er een probleem is met de interne database die wordt gebruikt door Azure File Sync. Wanneer dit probleem zich voordoet, maakt u een ondersteuningsverzoek en nemen we contact met u op om u te helpen dit probleem op te lossen.

<a id="-2134364053"></a>**De azure file sync-agentversie die op de server is geïnstalleerd, wordt niet ondersteund.**  

| | |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimaal)** | -2134364053 |
| **Fouttekenreeks** | ECS_E_AGENT_VERSION_BLOCKED |
| **Herstel vereist** | Ja |

Deze fout treedt op als de versie van de Azure File Sync-agent op de server wordt niet ondersteund. Als u dit probleem wilt oplossen, [kunt u upgraden]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) naar een ondersteunde [agentversie.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**U hebt de opslaglimiet voor Azure-bestanden bereikt.**  

| | |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimaal)** | -2134351810 |
| **Fouttekenreeks** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de opslaglimiet van de Azure-bestandsshare is bereikt. Dit kan gebeuren als er een quotum voor een Azure-bestandsshare is ingesteld of als een gebruikslimiet voor een Azure-bestandsshare is overschreden. Zie de huidige [limieten voor een Azure-bestandsshare voor](storage-files-scale-targets.md)meer informatie.

1. Navigeer naar de synchronisatiegroep binnen de Opslagsynchronisatieservice.
2. Selecteer het eindpunt van de cloud in de synchronisatiegroep.
3. Let op de naam azure-bestandsshare in het geopende deelvenster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslagaccount waarnaar wordt verwezen verwijderd.

    ![Een schermafbeelding van het detailvenster voor eindpunt in de cloud met een koppeling naar het opslagaccount.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecteer **Bestanden** om de lijst met bestandsshares weer te geven.
6. Klik op de drie puntjes aan het einde van de rij voor het Azure-bestandsshare waarnaar het eindpunt van de cloud wordt verwezen.
7. Controleer of het **gebruik** lager is dan het **quotum**. Opmerking, tenzij een alternatief quotum is opgegeven, komt het quotum overeen met de [maximale grootte van de Azure-bestandsshare](storage-files-scale-targets.md).

    ![Een schermafbeelding van de eigenschappen van Azure-bestandsshare.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Als de share vol is en er geen quotum is ingesteld, kunt u dit probleem oplossen door elke submap van het huidige servereindpunt te maken in een eigen servereindpunt en een eigen synchronisatiegroep. Zo wordt de verschillende submappen gesynchroniseerd met verschillende Azure-bestandsshares.

<a id="-2134351824"></a>**Het Azure-bestandsaandeel kan niet worden gevonden.**  

| | |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimaal)** | -2134351824 |
| **Fouttekenreeks** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is. Ga als volgt te werk om het probleem op te lossen:

1. [Controleer of het opslagaccount bestaat.](#troubleshoot-storage-account)
2. [Controleer of de Azure-bestandsshare bestaat.](#troubleshoot-azure-file-share)

Als de Azure-bestandsshare is verwijderd, moet u een nieuwe bestandsshare maken en vervolgens de synchronisatiegroep opnieuw maken. 

<a id="-2134364042"></a>**Synchronisatie wordt onderbroken terwijl dit Azure-abonnement wordt opgeschort.**  

| | |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimaal)** | -2134364042 |
| **Fouttekenreeks** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer het Azure-abonnement is onderbroken. Synchronisatie wordt opnieuw ingeschakeld wanneer het Azure-abonnement wordt hersteld. Zie [Waarom is mijn Azure-abonnement uitgeschakeld en hoe kan ik het opnieuw activeren?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134364052"></a>**Het opslagaccount heeft een firewall of virtuele netwerken geconfigureerd.**  

| | |
|-|-|
| **Hresult** | 0x80c8306c |
| **HRESULT (decimaal)** | -2134364052 |
| **Fouttekenreeks** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Herstel vereist** | Ja |

Deze fout treedt op wanneer de Azure-bestandsshare niet toegankelijk is vanwege een firewall bij een opslagaccount, of omdat het opslagaccount deel uitmaakt van een virtueel netwerk. Controleer of de firewall- en virtuele netwerkinstellingen op het opslagaccount correct zijn geconfigureerd. Zie [Firewall- en virtuele netwerkinstellingen configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)voor meer informatie. 

<a id="-2134375911"></a>**Synchronisatie is mislukt vanwege een probleem met de synchronisatiedatabase.**  

| | |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimaal)** | -2134375911 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Herstel vereist** | Nee |

Deze fout wordt meestal vanzelf opgelost, en kan optreden wanneer:

* Een groot aantal bestandswijzigingen tussen de servers in de synchronisatiegroep.
* Een groot aantal fouten op individuele bestanden en mappen.

Als deze fout langer dan een paar uur duurt, maakt u een ondersteuningsverzoek en nemen we contact met u op om u te helpen dit probleem op te lossen.

<a id="-2146762487"></a>**De server kan geen beveiligde verbinding tot stand brengen. De cloudservice heeft een onverwacht certificaat ontvangen.**  

| | |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimaal)** | -2146762487 |
| **Fouttekenreeks** | CERT_E_UNTRUSTEDROOT |
| **Herstel vereist** | Ja |

Deze fout kan optreden als uw organisatie een TLS-beëindigingsproxy gebruikt of als een kwaadwillende entiteit het verkeer tussen uw server en de Azure File Sync-service onderschept. Als u zeker weet dat dit wordt verwacht (omdat uw organisatie een TLS-beëindigingsproxy gebruikt), slaat u certificaatverificatie over met een registeroverschrijving.

1. Maak de registerwaarde Voor het verifiëren van vastgedraaide basiscertificaat.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Start de synchronisatieservice opnieuw op de geregistreerde server.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Door deze registerwaarde in te stellen, accepteert de Azure File Sync-agent elk lokaal vertrouwd TLS/SSL-certificaat bij het overbrengen van gegevens tussen de server en de cloudservice.

<a id="-2147012894"></a>**Er kon geen verbinding met de service worden gelegd.**  

| | |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimaal)** | -2147012894 |
| **Fouttekenreeks** | WININET_E_TIMEOUT |
| **Herstel vereist** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronisatie is mislukt vanwege een probleem met de verificatie.**  

| | |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimaal)** | -2134375680 |
| **Fouttekenreeks** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Herstel vereist** | Ja |

Deze fout wordt meestal veroorzaakt door een onjuiste servertijd. Als de server in een virtuele machine wordt uitgevoerd, controleert u of de tijd op de host correct is.

<a id="-2134364040"></a>**Synchronisatie is mislukt vanwege het verlopen van het certificaat.**  

| | |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimaal)** | -2134364040 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, is verlopen.

Voer de volgende stappen uit om te controleren of het certificaat is verlopen:  
1. Open de MMC-module Certificaten, selecteer Computeraccount en navigeer naar Certificaten (Lokale computer)\Personal\Certificates.
2. Controleer of het clientverificatiecertificaat is verlopen.

Als het certificaat voor clientverificatie is verlopen, voert u de volgende stappen uit om het probleem op te lossen:

1. Controleer of Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronisatie is mislukt omdat het verificatiecertificaat niet is gevonden.**  

| | |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimaal)** | -2134375896 |
| **Fouttekenreeks** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het certificaat dat wordt gebruikt voor verificatie, niet is gevonden.

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Controleer of Azure File Sync agent versie 4.0.1.0 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdracht uit op de server: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronisatie is mislukt omdat de verificatie-identiteit niet is gevonden.**  

| | |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimaal)** | -2134364039 |
| **Fouttekenreeks** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het verwijderen van het servereindpunt is mislukt en het eindpunt nu een gedeeltelijk verwijderde status heeft. Probeer het servereindpunt opnieuw te verwijderen om dit probleem op te lossen.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Het volume waarin het servereindpunt zich bevindt, heeft weinig schijfruimte.**  

| | |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimaal)** | -1906441711 |
| **Fouttekenreeks** | JET_errLogDiskFull |
| **Herstel vereist** | Ja |
| | |
| **Hresult** | 0x80c8031a |
| **HRESULT (decimaal)** | -2134375654 |
| **Fouttekenreeks** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het volume vol raakt. Deze fout treedt doorgaans op omdat bestanden buiten het servereindpunt ruimte gebruiken op het volume. Maak ruimte vrij op het volume door extra servereindpunten toe te voegen, bestanden naar een ander volume te verplaatsen of de grootte van het volume waarop het servereindpunt is ingeschakeld te vergroten.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**De service is nog niet klaar om te synchroniseren met dit servereindpunt.**  

| | |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimaal)** | -2134364145 |
| **Fouttekenreeks** | ECS_E_REPLICA_NOT_READY |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat het eindpunt van de cloud is gemaakt met inhoud die al bestaat in het Azure-bestandsshare. Azure File Sync moet de Azure-bestandsshare scannen voor alle inhoud voordat het servereindpunt doorkan gaan met de eerste synchronisatie.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronisatie is mislukt vanwege problemen met veel afzonderlijke bestanden.**  

| | |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimaal)** | -2134375877 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Herstel vereist** | Ja |
| | |
| **Hresult** | 0x80c8021c |
| **HRESULT (decimaal)** | -2134375908 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Herstel vereist** | Ja |
| | |
| **Hresult** | 0x80c80253 |
| **HRESULT (decimaal)** | -2134375853 |
| **Fouttekenreeks** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Herstel vereist** | Ja |

In gevallen waarin er veel synchronisatiefouten per bestand zijn, kunnen synchronisatiesessies beginnen te mislukken. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync maakt één keer per dag een tijdelijke VSS-momentopname op de server om bestanden met geopende handgrepen te synchroniseren.

<a id="-2134376423"></a>**Synchronisatie is mislukt vanwege een probleem met het eindpuntpad van de server.**  

| | |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimaal)** | -2134376423 |
| **Fouttekenreeks** | ECS_E_SYNC_INVALID_PATH |
| **Herstel vereist** | Ja |

Controleer of het pad bestaat, zich op een lokaal NTFS-volume bevindt en geen reparseorepunt of bestaand servereindpunt is.

<a id="-2134375817"></a>**Synchronisatie is mislukt omdat de versie van het filterstuurprogramma niet compatibel is met de agentversie**  

| | |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimaal)** | -2134375817 |
| **Fouttekenreeks** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Herstel vereist** | Ja |

Deze fout komt doordat de geladen versie van het filterstuurprogramma voor opslag in cloudlagen (StorageSync.sys) niet compatibel is met de Storage Sync Agent-service (FileSyncSvc). Als de Azure File Sync-agent is bijgewerkt, start u de server opnieuw op om de installatie te voltooien. Als de fout blijft optreden, verwijdert u de agent, start u de server opnieuw op en installeert u de Azure File Sync-agent opnieuw.

<a id="-2134376373"></a>**De service is momenteel niet beschikbaar.**  

| | |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimaal)** | -2134376373 |
| **Fouttekenreeks** | ECS_E_SERVICE_UNAVAILABLE |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat de Azure File Sync-service niet beschikbaar is. Deze fout wordt automatisch opgelost wanneer de Azure File Sync-service weer beschikbaar is.

<a id="-2146233088"></a>**Synchronisatie is mislukt vanwege een uitzondering.**  

| | |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimaal)** | -2146233088 |
| **Fouttekenreeks** | COR_E_EXCEPTION |
| **Herstel vereist** | Nee |

Deze fout treedt op omdat zich in Sync een uitzondering voordoet. Als de fout enkele uren blijft bestaan, maakt u een ondersteuningsverzoek.

<a id="-2134364045"></a>**Synchronisatie is mislukt omdat het opslagaccount niet is overgegaan naar een andere regio.**  

| | |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimaal)** | -2134364045 |
| **Fouttekenreeks** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat het opslagaccount is overgeschakeld naar een andere regio. Failover-overschakeling van het opslagaccount wordt niet ondersteund in Azure File Sync. Er mag geen failover-overschakeling worden uitgevoerd voor opslagaccounts met Azure-bestandsshares die worden gebruikt als cloudeindpunten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen. U kunt dit probleem oplossen door het opslagaccount te verplaatsen naar de primaire regio.

<a id="-2134375922"></a>**Synchronisatie is mislukt vanwege een tijdelijk probleem met de synchronisatiedatabase.**  

| | |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimaal)** | -2134375922 |
| **Fouttekenreeks** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Herstel vereist** | Nee |

Deze fout treedt op vanwege een intern probleem met de synchronisatiedatabase. Deze fout wordt automatisch opgelost bij nieuwe synchronisatiepogingen. Als deze fout gedurende een langere periode blijft bestaan, maakt u een ondersteuningsverzoek en nemen we contact met u op om u te helpen dit probleem op te lossen.

<a id="-2134364024"></a>**Synchronisatie is mislukt als gevolg van wijzigingen in Azure Active Directory-tenant**  

| | |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimaal)** | -2134364024 | 
| **Fouttekenreeks** | ECS_E_INVALID_AAD_TENANT |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat Azure File Sync op dit moment geen ondersteuning biedt voor het verplaatsen van het abonnement naar een andere Azure Active Directory-tenant.
 
Kies een of meer van de volgende opties om dit probleem op te lossen:

- **Optie 1 (aanbevolen)**: Het abonnement terugverplaatsen naar de oorspronkelijke Azure Active Directory-tenant
- **Optie 2**: De huidige synchronisatiegroep verwijderen en opnieuw maken. Als opslag in cloudlagen zijn ingeschakeld op het servereindpunt, verwijdert u de synchronisatiegroep en voert u vervolgens de stappen uit die worden beschreven in de sectie [Opslag in cloudlagen]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) om de zwevende bestanden uit de cloudlagen te verwijderen voordat u de synchronisatiegroepen opnieuw maakt. 

<a id="-2134364010"></a>**Synchronisatie is mislukt omdat firewall- en virtuele netwerkuitzondering niet is geconfigureerd**  

| | |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimaal)** | -2134364010 | 
| **Fouttekenreeks** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Herstel vereist** | Ja |

Deze fout treedt op als de firewall- en virtuele netwerkinstellingen zijn ingeschakeld op het opslagaccount en de uitzondering 'Vertrouwde Microsoft-services toestaan toegang te krijgen tot dit opslagaccount' niet is ingeschakeld. U kunt dit probleem oplossen door de stappen te volgen die worden beschreven in de sectie [Instellingen voor de firewall en het virtuele netwerk configureren](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatiehandleiding.

<a id="-2147024891"></a>**Synchronisatie is mislukt omdat machtigingen voor de map Systeemvolumegegevens onjuist zijn.**  

| | |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimaal)** | -2147024891 |
| **Fouttekenreeks** | ERROR_ACCESS_DENIED |
| **Herstel vereist** | Ja |

Deze fout kan optreden als het NT AUTHORITY\SYSTEM-account geen machtigingen heeft voor de map System Volume Information op het volume waar het servereindpunt zich bevindt. Houd er rekening mee dat als afzonderlijke bestanden niet worden gesynchroniseerd met ERROR_ACCESS_DENIED, de stappen uitvoert die zijn gedocumenteerd in de sectie [Probleemoplossing per bestand/mapsynchronisatiefouten.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Gebruik een of meer van de volgende stappen om dit probleem op te lossen:

1. Download het hulpprogramma [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Voer de volgende opdracht uit een opdrachtprompt met verhoogde bevoegdheid om een opdrachtprompt te starten met het systeemaccount: **PsExec.exe -i -s -d cmd** 
3. Voer de volgende opdracht uit vanaf de opdrachtprompt die wordt uitgevoerd met het systeemaccount om te controleren of het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information: **cacls "stationsletter:\system volume information" /T /C**
4. Als het NT AUTHORITY\SYSTEM-account geen toegang heeft tot de map System Volume Information, voert u de volgende opdracht uit: **cacls "stationsletter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM: F"**
    - Als stap 4 mislukt omdat de toegang wordt geweigerd, voert u de volgende opdracht uit om eigenaar van de map System Volume Information te worden en herhaalt u vervolgens stap 4: **takeown /A /R /F "stationsletter:\System Volume Information"**

<a id="-2134375810"></a>**Synchronisatie is mislukt omdat de Azure-bestandsshare is verwijderd en opnieuw is gemaakt.**  

| | |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimaal)** | -2134375810 |
| **Fouttekenreeks** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat Azure File Sync geen ondersteuning biedt voor het verwijderen en opnieuw maken van een Azure-bestandsshare in dezelfde synchronisatiegroep. 

U kunt dit probleem oplossen door de synchronisatiegroep te verwijderen en opnieuw te maken door de volgende stappen uit te voeren:

1. Verwijder alle servereindpunten in de synchronisatiegroep.
2. Verwijder het eindpunt van de cloud. 
3. Verwijder de synchronisatiegroep.
4. Als cloudtiering is ingeschakeld op een servereindpunt, verwijdert u de zwevende gelaagde bestanden op de server door de stappen uit te voeren die zijn gedocumenteerd in de [gelaagde bestanden, niet toegankelijk op de server nadat een servereindpuntsectie](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) is verwijderd.
5. Maak de synchronisatiegroep opnieuw.

<a id="-2145844941"></a>**Synchronisatie is mislukt omdat de HTTP-aanvraag is omgeleid**  

| | |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimaal)** | -2145844941 |
| **Fouttekenreeks** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Herstel vereist** | Ja |

Deze fout treedt op omdat Azure File Sync geen http-omleiding (3xx-statuscode) ondersteunt. Als u dit probleem wilt oplossen, schakelt u HTTP-omleiding uit op uw proxyserver of netwerkapparaat.

<a id="-2134364027"></a>**Er is een time-out opgetreden tijdens offline gegevensoverdracht, maar deze is nog in volle gang.**  

| | |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimaal)** | -2134364027 |
| **Fouttekenreeks** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Herstel vereist** | Nee |

Deze fout treedt op wanneer een gegevensopnamebewerking de time-out overschrijdt. Deze fout kan worden genegeerd als de synchronisatie vooruitgang boekt (AppliedItemCount is groter dan 0). Zie [Hoe kan ik de voortgang van een huidige synchronisatiesessie controleren?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Algemene stappen voor het oplossen van problemen
<a id="troubleshoot-storage-account"></a>**Controleer of het opslagaccount bestaat.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer naar de synchronisatiegroep binnen de Opslagsynchronisatieservice.
2. Selecteer het eindpunt van de cloud in de synchronisatiegroep.
3. Let op de naam azure-bestandsshare in het geopende deelvenster.
4. Selecteer het gekoppelde opslagaccount. Als deze koppeling mislukt, is het opslagaccount waarnaar wordt verwezen verwijderd.
    ![Een schermafbeelding van het detailvenster voor eindpunt in de cloud met een koppeling naar het opslagaccount.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-azure-file-share"></a>**Controleer of de Azure-bestandsshare bestaat.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klik op **Overzicht** op de inhoudsopgave aan de linkerkant om terug te keren naar de pagina met hoofdopslagaccount.
2. Selecteer **Bestanden** om de lijst met bestandsshares weer te geven.
3. Controleer of het bestandsaandeel waarnaar wordt verwezen door het cloudeindpunt wordt weergegeven in de lijst met bestandsshares (u had dit in stap 1 hierboven moeten noteren).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-rbac"></a>**Controleer of Azure File Sync toegang heeft tot het opslagaccount.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klik op **Toegangsbeheer (IAM)** op de inhoudsopgave aan de linkerkant.
1. Klik op het tabblad **Toewijzingen van rollen** om de lijst de gebruikers en toepassingen *(serviceprincipals)* die toegang hebben tot uw opslagaccount.
1. Controleer **microsoft.storageSync** of **Hybrid File Sync Service** (oude toepassingsnaam) wordt weergegeven in de lijst met de rol Reader en Data **Access.** 

    ![Een schermafbeelding van de principal van de Hybrid File Sync Service-service op het tabblad toegangsbeheer van het opslagaccount](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Als **Microsoft.StorageSync** of **Hybrid File Sync Service** niet in de lijst wordt weergegeven, voert u de volgende stappen uit:

    - Klik op**toevoegen**.
    - Selecteer **in** het veld Rol de optie **Reader en Data Access**.
    - Typ **microsoft.storagesync**in het veld **Selecteren,** selecteer de rol en klik op **Opslaan**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hoe voorkom ik dat gebruikers bestanden maken die niet-ondersteunde tekens op de server bevatten?
U [FSRM-bestandsschermen (File Server Resource Manager)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) gebruiken om te voorkomen dat bestanden met niet-ondersteunde tekens in hun naam op de server worden gemaakt. Het kan zijn dat u dit moet doen met PowerShell, omdat de meeste niet-ondersteunde tekens niet afdrukbaar zijn en u hun hexadecimale weergaven eerst als tekens moet casten.

Maak eerst een FSRM-bestandsgroep met de [cmdlet Nieuw-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). In dit voorbeeld wordt de groep gedefinieerd als slechts twee van de niet-ondersteunde tekens, maar u zoveel tekens opnemen als nodig is in uw bestandsgroep.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Zodra u een FSRM-bestandsgroep hebt gedefinieerd, u een FSRM-bestandsscherm maken met de cmdlet Nieuw-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Houd er rekening mee dat bestandsschermen alleen mogen worden gebruikt om het maken van tekens te blokkeren die niet worden ondersteund door Azure File Sync. Als bestandsschermen in andere scenario's worden gebruikt, wordt er voortdurend geprobeerd om de bestanden van het Azure-bestandsaandeel naar de server te downloaden en wordt deze geblokkeerd vanwege het bestandsscherm, wat resulteert in een hoge gegevensuitgang. 

## <a name="cloud-tiering"></a>Cloudopslaglagen 
Er zijn twee paden voor fouten in cloudlagen:

- Bestanden kunnen niet laag, wat betekent dat Azure File Sync tevergeefs pogingen om een bestand tier naar Azure Files.
- Bestanden kunnen zich niet terugroepen, wat betekent dat het azure file sync-bestandssysteemfilter (StorageSync.sys) geen gegevens downloadt wanneer een gebruiker toegang probeert te krijgen tot een bestand dat is gelaagd.

Er zijn twee hoofdklassen van storingen die kunnen gebeuren via een van beide mislukkingpad:

- Fouten in cloudopslag
    - *Problemen met de beschikbaarheid van tijdelijke opslagservice*. Zie de SLA [(Service Level Agreement) voor Azure Storage voor](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)meer informatie.
    - *Ontoegankelijke Azure-bestandsshare*. Deze fout treedt meestal op wanneer u de Azure-bestandsshare verwijdert wanneer het nog steeds een eindpunt in de cloud in een synchronisatiegroep is.
    - *Ontoegankelijk opslagaccount*. Deze fout treedt meestal op wanneer u het opslagaccount verwijdert terwijl het nog steeds een Azure-bestandsshare heeft, een eindpunt in de cloud in een synchronisatiegroep. 
- Serverfouten 
  - *Azure File Sync-bestandssysteemfilter (StorageSync.sys) wordt niet geladen.* Om te reageren op tiering/recall-aanvragen, moet het filter azure file sync-bestandssysteem worden geladen. Het filter dat niet wordt geladen, kan om verschillende redenen gebeuren, maar de meest voorkomende reden is dat een beheerder het handmatig heeft gelost. Het filter azure file sync-bestandssysteem moet te allen tijde worden geladen om Azure File Sync naar behoren te laten functioneren.
  - *Ontbrekende, corrupte, of anderszins gebroken reparse punt*. Een reparsepunt is een speciale gegevensstructuur op een bestand dat uit twee delen bestaat:
    1. Een reparsetag, die aan het besturingssysteem aangeeft dat het Azure File Sync-bestandssysteemfilter (StorageSync.sys) mogelijk enige actie op IO aan het bestand moet uitvoeren. 
    2. Reparse gegevens, die aan het bestandssysteem de URI van het bestand op het bijbehorende cloudeindpunt (het Azure-bestandsaandeel) filteren. 
        
       De meest voorkomende manier waarop een reparse punt beschadigd kan raken, is als een beheerder probeert de tag of de gegevens ervan te wijzigen. 
  - *Problemen met de netwerkconnectiviteit*. Om een bestand te kunnen tieren of terug te roepen, moet de server een internetverbinding hebben.

In de volgende secties wordt aangegeven hoe u problemen met cloudlagen oplossen en u bepalen of een probleem een probleem is met de cloudopslag of een serverprobleem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Activiteit van opslaglagen op een server bewaken  
Als u de gelaagdheidsactiviteit op een server wilt controleren, gebruikt u gebeurtenis-id 9003, 9016 en 9029 in het gebeurtenislogboek Telemetrie (bevindt zich onder Toepassingen en Services\Microsoft\FileSync\Agent in logboeken).

- Gebeurtenis-id 9003 biedt foutdistributie voor een servereindpunt. Bijvoorbeeld total error count, errorCode, etc. Let op, één gebeurtenis wordt per foutcode geregistreerd.
- Gebeurtenis-ID 9016 biedt ghosting resultaten voor een volume. Bijvoorbeeld, Vrije ruimte procent is, Aantal bestanden ghosted in sessie, Aantal bestanden niet ghost, enz.
- Gebeurtenis-ID 9029 biedt informatie over ghosting-sessies voor een servereindpunt. Bijvoorbeeld, Aantal bestanden geprobeerd in de sessie, Aantal bestanden gelaagd in de sessie, Aantal bestanden al gelaagd, enz.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Terughaalactiviteit op een server bewaken
Als u de terugroepactiviteit op een server wilt controleren, gebruikt u gebeurtenis-id 9005, 9006, 9009 en 9059 in het gebeurtenislogboek Telemetrie (bevindt zich onder Toepassingen en Services\Microsoft\FileSync\Agent in Logboeken).

- Gebeurtenis-ID 9005 biedt de betrouwbaarheid van een server voor een servereindpunt. Bijvoorbeeld, Total unieke bestanden geopend, Totaal unieke bestanden met mislukte toegang, enz.
- Gebeurtenis-id 9006 biedt de distributie van terugroepfouten voor een servereindpunt. Bijvoorbeeld total failed requests, errorCode, etc. Let op, één gebeurtenis wordt per foutcode geregistreerd.
- Gebeurtenis-ID 9009 biedt informatie over terugroepsessies voor een servereindpunt. Bijvoorbeeld, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, enz.
- Gebeurtenis-id 9059 biedt de distributie van toepassingsterugroepinformatie voor een servereindpunt. Bijvoorbeeld ShareId, Toepassingsnaam en TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Bestanden oplossen die niet op de laag komen
Als bestanden niet worden geklasseerd naar Azure-bestanden:

1. Controleer in Logboeken de telemetrie-, operationele en diagnostische gebeurtenislogboeken die zich onder Toepassingen en Services\Microsoft\FileSync\Agent bevinden. 
   1. Controleer of de bestanden in de Azure-bestandsshare bestaan.

      > [!NOTE]
      > Een bestand moet worden gesynchroniseerd met een Azure-bestandsshare voordat het kan worden gelaagd.

   2. Controleer of de server een internetverbinding heeft. 
   3. Controleer of de filterstuurprogramma's voor Azure File Sync (StorageSync.sys en StorageSyncGuard.sys) actief zijn:
       - Voer bij een opdrachtprompt `fltmc`met verhoogde bevoegdheid uit. Controleer of de filterstuurprogramma's StorageSync.sys en StorageSyncGuard.sys-bestandssysteem worden vermeld.

> [!NOTE]
> Een gebeurtenis-id 9003 wordt eenmaal per uur geregistreerd in het gebeurtenislogboek Telemetrie als een bestand niet wordt gelaagd (één gebeurtenis wordt per foutcode geregistreerd). Controleer de [sectie Laagfouten en herstel om](#tiering-errors-and-remediation) te zien of herstelstappen worden weergegeven voor de foutcode.

### <a name="tiering-errors-and-remediation"></a>Tiering-fouten en herstel

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Het bestand kan niet worden geklasseerd omdat het in gebruik is. | U hoeft geen actie te ondernemen. Het bestand wordt gelaagd wanneer het niet meer in gebruik is. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Het bestand kan niet worden geklasseerd omdat het is uitgesloten door synchronisatie. | U hoeft geen actie te ondernemen. Bestanden in de lijst met synchronisatieuitsluiting kunnen niet worden gegelaagdeerd. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Het bestand kan niet worden geklasseerd omdat het niet op de server is gevonden. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, controleert u of het bestand op de server bestaat. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Het bestand kan niet worden geklasseerd omdat het is verwijderd in de Azure-bestandsshare. | U hoeft geen actie te ondernemen. Het bestand moet worden verwijderd op de server wanneer de volgende downloadsynchronisatiesessie wordt uitgevoerd. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Het bestand kan niet worden geklasseerd vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, controleert u de netwerkverbinding met het Azure-bestandsshare. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Het bestand kan niet worden geklasseerd vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, controleert u de netwerkverbinding met het Azure-bestandsshare. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand kan niet worden geklasseerd vanwege de fout met de toegang geweigerd. Deze fout kan optreden als het bestand zich op een alleen-lezen MAP van DFS-R bevindt. | Azure Files Sync biedt geen ondersteuning voor servereindpunten in DFS-R-replicatiemappen met het kenmerk Alleen-lezen. Zie [planningsgids](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) voor meer informatie. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Het bestand kan niet worden geklasseerd vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, controleert u de netwerkverbinding met het Azure-bestandsshare. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Het bestand kan niet worden geklasseerd omdat de bestandsgrootte kleiner is dan de ondersteunde grootte. | Als de agentversie minder dan 9.0 is, is de minimale ondersteunde bestandsgrootte 64 kb. Als agentversie 9.0 en nieuwer is, is de minimaal ondersteunde bestandsgrootte gebaseerd op de clustergrootte van het bestandssysteem (dubbele clustergrootte van bestandssystemen). Als de clustergrootte van het bestandssysteem bijvoorbeeld 4 kb is, is de minimale bestandsgrootte 8kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Het bestand kan niet worden geklasseerd vanwege een Azure-opslagprobleem. | Als de fout blijft bestaan, opent u een ondersteuningsverzoek. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Het bestand kan niet worden geklasseerd omdat het tegelijkertijd werd teruggeroepen. | U hoeft geen actie te ondernemen. Het bestand wordt trapsgewijs weergegeven wanneer de terugroepactie is voltooid en het bestand niet meer wordt gebruikt. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Het bestand kan niet worden geklasseerd omdat het niet is gesynchroniseerd met de Azure-bestandsshare. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestandsshare. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Het bestand kan niet worden geklasseerd omdat het filterstuurprogramma voor cloudlagen (storagesync.sys) niet wordt uitgevoerd. | Als u dit probleem wilt oplossen, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u de volgende opdracht uit:`fltmc load storagesync`<br>Als het filterstuurprogramma voor storagesync niet wordt geladen wanneer de fltmc-opdracht wordt uitgevoerd, verwijdert u de Azure File Sync-agent, start u de server opnieuw en installeert u de Azure File Sync-agent opnieuw. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand kan niet worden geklasseerd vanwege onvoldoende schijfruimte op het volume waarin het servereindpunt zich bevindt. | Als u dit probleem wilt oplossen, u ten minste 100 MB schijfruimte vrijmaken op het volume waarin het eindpunt van de server zich bevindt. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Het bestand kan niet worden geklasseerd omdat het niet is gesynchroniseerd met de Azure-bestandsshare. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestandsshare. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Het bestand kan niet worden geklasseerd omdat het een niet-ondersteund reparse punt is. | Als het bestand een weerdubbel punt voor gegevensdeduplicatie is, volgt u de stappen in de [planningshandleiding](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) om ondersteuning voor gegevensdeduplicatie in te schakelen. Bestanden met andere rearse punten dan Data Deduplicatie worden niet ondersteund en worden niet trapsgewijs uitgevoerd.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Het bestand kan niet worden geklasseerd omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestandsshare. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Het bestand kan niet worden geklasseerd omdat het niet is gesynchroniseerd met de Azure-bestandsshare. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het is gesynchroniseerd met de Azure-bestandsshare. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Het bestand kan niet worden geklasseerd vanwege een netwerkprobleem. | U hoeft geen actie te ondernemen. Als de fout blijft bestaan, controleert u de netwerkverbinding met het Azure-bestandsshare. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Het bestand kan niet worden geklasseerd omdat het is gewijzigd. | U hoeft geen actie te ondernemen. Het bestand wordt laag zodra het gewijzigde bestand is gesynchroniseerd met de Azure-bestandsshare. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand kan niet worden geklasseerd vanwege onvoldoende systeembronnen. | Als de fout blijft bestaan, onderzoekt u welke toepassing of stuurprogramma in de kernelmodus systeembronnen uitputten. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Bestanden oplossen die niet worden teruggeroepen  
Als bestanden niet worden teruggeroepen:
1. Controleer in Logboeken de telemetrie-, operationele en diagnostische gebeurtenislogboeken die zich onder Toepassingen en Services\Microsoft\FileSync\Agent bevinden.
    1. Controleer of de bestanden in de Azure-bestandsshare bestaan.
    2. Controleer of de server een internetverbinding heeft. 
    3. Open de MMC-module services en controleer of de Storage Sync Agent-service (FileSyncSvc) wordt uitgevoerd.
    4. Controleer of de filterstuurprogramma's voor Azure File Sync (StorageSync.sys en StorageSyncGuard.sys) actief zijn:
        - Voer bij een opdrachtprompt `fltmc`met verhoogde bevoegdheid uit. Controleer of de filterstuurprogramma's StorageSync.sys en StorageSyncGuard.sys-bestandssysteem worden vermeld.

> [!NOTE]
> Een gebeurtenis-id 9006 wordt eenmaal per uur geregistreerd in het gebeurtenislogboek Telemetrie als een bestand zich niet terugroept (één gebeurtenis wordt per foutcode geregistreerd). Controleer de sectie [Terugroepen van fouten en herstel](#recall-errors-and-remediation) om te zien of herstelstappen worden weergegeven voor de foutcode.

### <a name="recall-errors-and-remediation"></a>Fouten terugroepen en herstellen

| Hresult | HRESULT (decimaal) | Fouttekenreeks | Probleem | Herstel |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Het bestand kan zich niet herinneren vanwege een I/O-time-out. Dit probleem kan om verschillende redenen optreden: beperkingen van serverbronnen, slechte netwerkconnectiviteit of een Azure-opslagprobleem (bijvoorbeeld beperking). | U hoeft geen actie te ondernemen. Als de fout gedurende enkele uren blijft bestaan, opent u een ondersteuningscase. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Het bestand kan zich niet terugroepen vanwege een netwerkprobleem.  | Als de fout blijft bestaan, controleert u de netwerkverbinding met het Azure-bestandsshare. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Het bestand kan zich niet herinneren omdat het eindpunt van de server is verwijderd. | Zie [Trapsgewijze bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)om dit probleem op te lossen. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Het bestand kan zich niet terugroepen vanwege een fout met geweigerde toegang. Dit probleem kan optreden als de firewall- en virtuele netwerkinstellingen op het opslagaccount zijn ingeschakeld en de server geen toegang heeft tot het opslagaccount. | Als u dit probleem wilt oplossen, voegt u het IP-adres van de server of het virtuele netwerk toe door de stappen te volgen die zijn gedocumenteerd in de sectie [Firewall configureren en virtuele netwerkinstellingen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) in de implementatiehandleiding. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Het bestand kan zich niet herinneren omdat het niet toegankelijk is in het Azure-bestandsshare. | Als u dit probleem wilt oplossen, controleert u of het bestand bestaat in de Azure-bestandsshare. Als het bestand bestaat in de Azure-bestandsshare, u upgraden naar de nieuwste versie van de Azure File [Sync-agent](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Het bestand kan niet worden teruggeroepen omdat de opslagaccount niet is autorisatie. | Als u dit probleem wilt oplossen, controleert u of [Azure File Sync toegang heeft tot het opslagaccount](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Het bestand kan zich niet terugroepen omdat de Azure-bestandsshare niet toegankelijk is. | Controleer of de bestandsshare bestaat en toegankelijk is. Als de bestandsshare is verwijderd en opnieuw is gemaakt, voert u de stappen uit die zijn gedocumenteerd in de [synchronisatie, omdat de azure-bestandsshare is verwijderd en opnieuw is gemaakt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) om de synchronisatiegroep te verwijderen en opnieuw te maken. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Het bestand kan niet worden teruggeroepen vanwege onvoldoende systeembronnen. | Als de fout blijft bestaan, onderzoekt u welke toepassing of stuurprogramma in de kernelmodus systeembronnen uitputten. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Het bestand kan niet worden teruggeroepen vanwege het insuffcient geheugen. | Als de fout blijft bestaan, onderzoekt u welke toepassing of kernel-modus stuurprogramma de lage geheugentoestand veroorzaakt. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Het bestand kan zich niet terugroepen vanwege onvoldoende schijfruimte. | Als u dit probleem wilt oplossen, maakt u ruimte vrij op het volume door bestanden naar een ander volume te verplaatsen, de grootte van het volume te vergroten of bestanden tot laag te dwingen met de cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Gelaagde bestanden zijn niet toegankelijk op de server na het verwijderen van een servereindpunt
Gelaagde bestanden op een server worden ontoegankelijk als de bestanden niet worden teruggeroepen voordat een servereindpunt wordt verwijderd.

Fouten die zijn geregistreerd als gelaagde bestanden niet toegankelijk zijn
- Bij het synchroniseren van een bestand wordt foutcode -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) geregistreerd in het gebeurtenislogboek ItemResults
- Bij het terugroepen van een bestand wordt foutcode -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) geregistreerd in het gebeurtenislogboek RecallResults

Toegang herstellen tot uw gelaagde bestanden is mogelijk als aan de volgende voorwaarden is voldaan:
- Het servereindpunt is verwijderd in de afgelopen 30 dagen
- Het cloudeindpunt is niet verwijderd 
- De bestandsshare is niet verwijderd
- De synchronisatiegroep is niet verwijderd

Als aan de bovenstaande voorwaarden is voldaan, kunt u de toegang tot de bestanden op de server herstellen door binnen 30 dagen het servereindpunt opnieuw te maken op hetzelfde serverpad binnen dezelfde synchronisatiegroep. 

Als niet aan de bovenstaande voorwaarden is voldaan, is het niet mogelijk om de toegang te herstellen, aangezien deze gelaagde bestanden op de server nu zwevend zijn. Volg de onderstaande instructies om de zwevende bestanden te verwijderen.

**Opmerkingen**
- Wanneer gelaagde bestanden niet toegankelijk zijn op de server, moet het volledige bestand nog steeds toegankelijk zijn als u rechtstreeks toegang hebt tot de Azure-bestandsshare.
- Als u in de toekomst zwevende gelaagde bestanden wilt voorkomen, voert u de stappen uit die zijn gedocumenteerd in [Een servereindpunt verwijderen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) bij het verwijderen van een servereindpunt.

<a id="get-orphaned"></a>**Hoe de lijst met zwevende bestanden met weesniveau te krijgen** 

1. Controleer of Azure File Sync agent versie v5.1 of hoger is geïnstalleerd.
2. Voer de volgende PowerShell-opdrachten uit om zwevende bestanden met zwevende gegevens weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sla het uitvoerbestand OrphanTieredFiles.txt op voor het geval bestanden moeten worden hersteld van back-up nadat ze zijn verwijderd.

<a id="remove-orphaned"></a>**Zwevende bestanden verwijderen** 

*Optie 1: De zwevende bestanden met zwevende ten hoogste niveau verwijderen*

Met deze optie worden de zwevende bestanden op de Windows Server verwijderd, maar moet het servereindpunt worden verwijderd als deze bestaat vanwege recreatie na 30 dagen of is verbonden met een andere synchronisatiegroep. Bestandsconflicten ontstaan als bestanden worden bijgewerkt op de Windows Server of Azure-bestandsshare voordat het servereindpunt opnieuw wordt gemaakt.

1. Controleer of Azure File Sync agent versie v5.1 of hoger is geïnstalleerd.
2. Maak een back-up van de azure-bestandsshare en de locatie van het eindpunt van de server.
3. Verwijder het servereindpunt in de synchronisatiegroep (indien aanwezig) door de stappen te volgen die zijn gedocumenteerd in [Een servereindpunt verwijderen.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)

> [!Warning]  
> Als het servereindpunt niet wordt verwijderd voordat het cmdlet Remove-StorageSyncOrphanedTieredFiles wordt gebruikt, wordt het verwijderen van het zwevende gelaagde bestand op de server het volledige bestand in de Azure-bestandsshare verwijderd. 

4. Voer de volgende PowerShell-opdrachten uit om zwevende bestanden met zwevende gegevens weer te geven:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Sla het uitvoerbestand OrphanTieredFiles.txt op voor het geval bestanden moeten worden hersteld van back-up nadat ze zijn verwijderd.
6. Voer de volgende PowerShell-opdrachten uit om zwevende bestanden met weesniveau te verwijderen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Opmerkingen** 
- Gelaagde bestanden die op de server zijn gewijzigd en die niet zijn gesynchroniseerd met de Azure-bestandsshare, worden verwijderd.
- Gelaagde bestanden die toegankelijk zijn (niet wees) worden niet verwijderd.
- Niet-gelaagde bestanden blijven op de server staan.

7. Optioneel: maak het servereindpunt opnieuw als het in stap 3 wordt verwijderd.

*Optie 2: De Azure-bestandsshare monteren en de bestanden lokaal kopiëren die op de server zijn verweesd*

Deze optie vereist niet dat het eindpunt van de server wordt verwijderd, maar dat er voldoende schijfruimte nodig is om de volledige bestanden lokaal te kopiëren.

1. [Monteer](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) het Azure-bestandsaandeel op de Windows Server met zwevende bestanden met wees.
2. Voer de volgende PowerShell-opdrachten uit om zwevende bestanden met zwevende gegevens weer te geven:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Gebruik het uitvoerbestand OrphanTieredFiles.txt om zwevende bestanden op de server te identificeren.
4. Overschrijf de zwevende gelaagde bestanden door het volledige bestand van het Azure-bestandsshare naar de Windows Server te kopiëren.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Problemen oplossen van bestanden die onverwacht worden teruggeroepen op een server  
Antivirus, back-up en andere toepassingen die grote aantallen bestanden lezen, veroorzaken onbedoelde recalls, tenzij ze het kenmerk offline overslaan respecteren en het lezen van de inhoud van die bestanden overslaan. Als u offlinebestanden overslaat voor producten die ondersteuning bieden voor deze optie, voorkomt u dat er onbedoeld wordt teruggehaald tijdens bijvoorbeeld antivirusscans en back-uptaken.

Vraag uw softwareleverancier hoe de oplossing kan worden geconfigureerd zodat offlinebestanden niet meer worden gelezen.

Onbedoelde terugroepingen kunnen ook voorkomen in andere scenario's, zoals wanneer u door bestanden bladert in Verkenner. Als u in Verkenner op de server een map opent met bestanden in cloudlagen, kan dat resulteren in onbedoeld terughalen. Dit is nog waarschijnlijker als er een antivirusoplossing is ingeschakeld op de server.

> [!NOTE]
>Gebruik gebeurtenis-id 9059 in het gebeurtenislogboek Telemetrie om te bepalen welke toepassing(en) terugroepacties veroorzaakt. Deze gebeurtenis biedt de distributie van het terugroepen van toepassingen voor een servereindpunt en wordt eenmaal per uur geregistreerd.

## <a name="general-troubleshooting"></a>Algemene probleemoplossing
Als u problemen ondervindt met Azure File Sync op een server, moet u de volgende stappen uitvoeren:
1. Bekijk in Logboeken de telemetrie-, operationele en diagnostische gebeurtenislogboeken.
    - Synchronisatie-, tiering- en terugroepproblemen worden geregistreerd in de telemetrie-, diagnostische en operationele gebeurtenislogboeken onder Toepassingen en Services\Microsoft\FileSync\Agent.
    - Problemen met betrekking tot het beheren van een server (bijvoorbeeld configuratie-instellingen) worden geregistreerd in de operationele en diagnostische gebeurtenislogboeken onder Toepassingen en Services\Microsoft\FileSync\Management.
2. Controleer of de Azure File Sync-service op de server wordt uitgevoerd:
    - Open de MMC-module services en controleer of de Storage Sync Agent-service (FileSyncSvc) wordt uitgevoerd.
3. Controleer of de filterstuurprogramma's voor Azure File Sync (StorageSync.sys en StorageSyncGuard.sys) actief zijn:
    - Voer bij een opdrachtprompt `fltmc`met verhoogde bevoegdheid uit. Controleer of de filterstuurprogramma's StorageSync.sys en StorageSyncGuard.sys-bestandssysteem worden vermeld.

Als het probleem niet is opgelost, voert u het gereedschap AFSDiag uit:
1. Maak een map waarin de AFSDiag-uitvoer wordt opgeslagen (bijvoorbeeld C:\Output).
    > [!NOTE]
    >AFSDiag verwijdert alle inhoud in de uitvoermap voordat logboeken worden verzameld. Geef een uitvoerlocatie op die geen gegevens bevat.
2. Open een verhoogd PowerShell-venster en voer de volgende opdrachten uit (druk op Enter na elke opdracht):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Voer voor het traceringsniveau van de Azure File Sync-kernelmodus **1** in (tenzij anders aangegeven, om meer verbosesporen te maken) en druk op Enter.
4. Voer voor het traceringsniveau van de gebruikersmodus Azure File Sync **1** in (tenzij anders aangegeven, om meer verbosesporen te maken) en druk op Enter.
5. Reproduceer het probleem. Als u klaar bent, voert u **D**in.
6. Een .zip-bestand met logboeken en traceerbestanden wordt opgeslagen in de uitvoermap die u hebt opgegeven.

## <a name="see-also"></a>Zie ook
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
- [Veelgestelde vragen in Azure Files](storage-files-faq.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
