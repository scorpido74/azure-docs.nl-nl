---
title: Azure-bestandenproblemen in Windows oplossen | Microsoft Documenten
description: Azure Files-problemen oplossen in Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 17ecc80fee3b024c334b8d36533663f1f3cebe4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136902"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Problemen met Azure Files in Windows oplossen

In dit artikel worden veelvoorkomende problemen weergegeven die verband houden met Microsoft Azure Files wanneer u verbinding maakt met Windows-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. Naast de stappen voor het oplossen van problemen in dit artikel, u [azfilediagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ook gebruiken om ervoor te zorgen dat de Windows-clientomgeving de juiste vereisten heeft. AzFileDiagnostics automatiseert de detectie van de meeste symptomen die in dit artikel worden genoemd en helpt bij het instellen van uw omgeving om optimale prestaties te krijgen. U deze informatie ook vinden in de [probleemoplosser voor azure files-shares,](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) die stappen biedt om u te helpen bij problemen met het verbinden/toewijzen/monteren van Azure Files-shares.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fout 5 bij het monteren van een Azure-bestandsshare

Wanneer u een bestandsshare probeert te monteren, ontvangt u mogelijk de volgende fout:

- Systeemfout 5 heeft zich voorgedaan. Toegang wordt geweigerd.

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: Ongecodeerd communicatiekanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Voor Windows 8, Windows Server 2012 en latere versies van elk systeem wordt onderhandeld over aanvragen die SMB 3.0 omvatten, wat ondersteuning biedt voor versleuteling.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Maak verbinding vanaf een client die SMB-versleuteling ondersteunt (Windows 8, Windows Server 2012 of hoger) of maak verbinding vanaf een virtuele machine in hetzelfde datacenter als het Azure-opslagaccount dat wordt gebruikt voor het Azure-bestandsshare.
2. Controleer of de vereiste instelling [Secure transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is uitgeschakeld op het opslagaccount als de client geen SMB-versleuteling ondersteunt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: Virtuele netwerk- of firewallregels zijn ingeschakeld op het opslagaccount 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security) voor meer informatie.

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Oorzaak 3: Machtigingen op shareniveau zijn onjuist bij het gebruik van verificatie op basis van identiteit

Als gebruikers toegang hebben tot de Azure-bestandsshare met Active Directory (AD) of Azure Active Directory Domain Services (Azure AD DS)-verificatie, mislukt de toegang tot het bestandsaandeel met de fout 'Toegang wordt geweigerd' als machtigingen op share-level onjuist zijn. 

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3

Zie [Toegangsmachtigingen toewijzen aan een identiteit](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#assign-access-permissions-to-an-identity)als u de machtigingen op shareniveau wilt bijwerken.

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fout 53, Fout 67 of Fout 87 wanneer u een Azure-bestandsshare monteert of ongedaan maakt

Wanneer u een bestandsshare probeert te monteren vanuit on-premises of vanuit een ander datacenter, ontvangt u mogelijk de volgende fouten:

- Systeemfout 53 heeft zich voorgedaan. Het netwerkpad is niet gevonden.
- Systeemfout 67 heeft zich voorgedaan. De naam van het netwerk kan niet worden gevonden.
- Systeemfout 87 heeft zich voorgedaan. De parameter is onjuist.

### <a name="cause-1-port-445-is-blocked"></a>Oorzaak 1: Poort 445 is geblokkeerd

Systeemfout 53 of systeemfout 67 kan optreden als poort 445 uitgaande communicatie naar een Azure Files-datacenter is geblokkeerd. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Als u wilt controleren of uw firewall of ISP poort 445 blokkeert, gebruikt u het gereedschap [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) of `Test-NetConnection` de cmdlet. 

Als u `Test-NetConnection` de cmdlet wilt gebruiken, moet de Azure PowerShell-module zijn geïnstalleerd, zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps) voor meer informatie. Vergeet niet om `<your-storage-account-name>` en `<your-resource-group-name>` te vervangen door de betreffende namen van uw opslagaccount.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Als de verbinding is geslaagd, hoort u de volgende uitvoer te zien:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> De bovenstaande opdracht retourneert het huidige IP-adres van het opslagaccount. Dit IP-adres blijft niet noodzakelijkerwijs hetzelfde en kan op elk moment veranderen. Neem dit IP-adres niet op in scripts of in de firewallconfiguratie.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

#### <a name="solution-1---use-azure-file-sync"></a>Oplossing 1: Azure File Sync gebruiken
Azure File Sync kan uw on-premises Windows Server omzetten in een snelle cache van uw Azure-bestandsshare. U elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, NFS en FTPS. Azure File Sync werkt via poort 443 en kan dus worden gebruikt als tijdelijke oplossing om toegang te krijgen tot Azure Files van clients die poort 445 hebben geblokkeerd. [Meer informatie over het instellen van Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Oplossing 2: VPN gebruiken
Door het instellen van een VPN op uw specifieke opslagaccount, zal het verkeer gaan door een veilige tunnel in tegenstelling tot via het internet. Volg de [instructies voor het instellen van VPN](storage-files-configure-p2s-vpn-windows.md) om toegang te krijgen tot Azure Files vanuit Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Oplossing 3: Poort 445 deblokkeren met behulp van uw internetprovider/IT-beheerder
Werk samen met uw IT-afdeling of ISP om poort 445 uitgaande naar [Azure IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)te openen.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Oplossing 4: Hulpprogramma’s op basis van REST API gebruiken, zoals Storage Explorer/PowerShell
Azure Files ondersteunt naast SMB ook REST. REST-toegang werkt via poort 443 (standaard tcp). Er zijn verschillende tools die zijn geschreven met behulp van REST API die rijke UI-ervaring mogelijk te maken. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) is een van hen. [Download en installeer Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met uw bestandsshare dat wordt ondersteund door Azure Files. U ook [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) gebruiken die ook rest API van de gebruiker gebruikt.

### <a name="cause-2-ntlmv1-is-enabled"></a>Oorzaak 2: NTLMv1 is ingeschakeld

Systeemfout 53 of systeemfout 87 kan optreden als NTLMv1-communicatie is ingeschakeld op de client. Azure Files biedt alleen ondersteuning voor NTLMv2-verificatie. Als NTLMv1 is ingeschakeld, is de client minder veilig. Om deze reden wordt communicatie geblokkeerd voor Azure Files. 

Als u wilt vaststellen of dit de oorzaak van de fout is, controleert u of de volgende registersubsleutel is ingesteld op de waarde 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Zie het onderwerp [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) in TechNet voor meer informatie.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Zet de waarde **LmCompatibilityLevel** terug naar de standaardwaarde 3 in de volgende registersubsleutel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fout 1816 "Onvoldoende quotum is beschikbaar om deze opdracht te verwerken" wanneer u kopieert naar een Azure-bestandsshare

### <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer u de bovengrens van gelijktijdige open handgrepen bereikt die zijn toegestaan voor een bestand op de computer waar de bestandsshare wordt gemonteerd.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige open handgrepen door sommige handgrepen te sluiten en probeer het opnieuw. Zie checklist [microsoft Azure Storage-prestaties en schaalbaarheid](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

Als u geopende handgrepen voor een bestandsshare, map of bestand wilt weergeven, gebruikt u de PowerShell-cmdlet [Get-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle)  

Als u open handgrepen voor een bestandsshare, map of bestand wilt sluiten, gebruikt u de PowerShell-cmdlet [Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in Az PowerShell-moduleversie 2.4 of hoger. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u de nieuwste Az PowerShell-module wilt installeren.

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
Wanneer u een bestand probeert te verwijderen, ontvangt u mogelijk de volgende fout:

De opgegeven bron is gemarkeerd voor verwijdering door een SMB-client.

### <a name="cause"></a>Oorzaak
Dit probleem treedt meestal op als het bestand of de map een open greep heeft. 

### <a name="solution"></a>Oplossing

Als de SMB-clients alle geopende handgrepen hebben gesloten en het probleem zich blijft voordoen, voert u het volgende uit:

- Gebruik de [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-cmdlet om open handgrepen weer te geven.

- Gebruik de [PowerShell-cmdlet Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) om open handgrepen te sluiten. 

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in Az PowerShell-moduleversie 2.4 of hoger. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u de nieuwste Az PowerShell-module wilt installeren.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Bestanden worden langzaam gekopieerd van en naar Azure Files in Windows

Mogelijk ziet u trage prestaties wanneer u bestanden probeert over te zetten naar de Azure File-service.

- Als u geen specifieke i/o-groottevereiste hebt, raden we u aan 1 MiB als I/O-maat te gebruiken voor optimale prestaties.
-   Als u de uiteindelijke grootte van een bestand weet dat u uitbreidt met schrijft, en uw software geen compatibiliteitsproblemen heeft wanneer de ongeschreven staart op het bestand nullen bevat, stelt u de bestandsgrootte van tevoren in in plaats van elke schrijf een uitschuifbare schrijven.
-   Gebruik de juiste kopieermethode:
    -   Gebruik [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor elke overdracht tussen twee bestandsshares.
    -   Gebruik [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) tussen bestandsshares op een on-premises computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Overwegingen voor Windows 8.1 of Windows Server 2012 R2

Voor clients waarop Windows 8.1 of Windows Server 2012 R2 wordt uitgevoerd, moet u ervoor zorgen dat de [KB3114025-hotfix](https://support.microsoft.com/help/3114025) is geïnstalleerd. Deze hotfix verbetert de prestaties van het maken en sluiten van handgrepen.

U het volgende script uitvoeren om te controleren of de hotfix is geïnstalleerd:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Als hotfix is geïnstalleerd, wordt de volgende uitvoer weergegeven:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2-afbeeldingen in Azure Marketplace hebben hotfix KB3114025 standaard geïnstalleerd, vanaf december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Geen map met een stationsletter in 'Mijn computer' of 'Deze pc'

Als u een Azure-bestandsshare als beheerder in kaart brengt met behulp van nettogebruik, lijkt het aandeel te ontbreken.

### <a name="cause"></a>Oorzaak

Standaard wordt Windows Verkenner niet als beheerder uitgevoerd. Als u nettogebruik uitvoert via een opdrachtprompt voor beheerders, brengt u het netwerkstation in kaart als beheerder. Omdat toegewezen stations gebruikersgericht zijn, geeft het gebruikersaccount dat is aangemeld de stations niet weer als ze onder een ander gebruikersaccount zijn gemonteerd.

### <a name="solution"></a>Oplossing
Monteer het aandeel vanaf een opdrachtregel die niet door de beheerder is gewijzigd. U ook [dit TechNet-onderwerp](https://technet.microsoft.com/library/ee844140.aspx) volgen om de registerwaarde **EnableLinkedConnections** te configureren.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Opdracht Voor netgebruik mislukt als het opslagaccount een slash doorsturen bevat

### <a name="cause"></a>Oorzaak

De opdracht Netgebruik interpreteert een voorwaartse slash (/) als een opdrachtregeloptie. Als de naam van uw gebruikersaccount begint met een slash doorsturen, mislukt de toewijzing van het station.

### <a name="solution"></a>Oplossing

U een van de volgende stappen gebruiken om het probleem te omzeilen:

- Voer de volgende PowerShell-opdracht uit:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Vanuit een batchbestand u de opdracht op deze manier uitvoeren:

  `Echo new-smbMapping ... | powershell -command –`

- Zet dubbele aanhalingstekens rond de sleutel om te werken rond dit probleem - tenzij de voorwaartse slash is het eerste teken. Als dit het wel is, gebruik dan de interactieve modus en voer uw wachtwoord afzonderlijk in of regenereert uw sleutels om een sleutel te krijgen die niet begint met een voorwaartse slash.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Toepassing of service heeft geen toegang tot een gemonteerd Azure Files-station

### <a name="cause"></a>Oorzaak

Schijven worden per gebruiker gemonteerd. Als uw toepassing of service wordt uitgevoerd onder een ander gebruikersaccount dan het account dat het station heeft gemonteerd, ziet de toepassing het station niet.

### <a name="solution"></a>Oplossing

Gebruik een van de volgende oplossingen:

-   Monteer het station vanaf hetzelfde gebruikersaccount dat de toepassing bevat. U een tool zoals PsExec gebruiken.
- Geef de naam en sleutel van het opslagaccount door in de gebruikersnaam- en wachtwoordparameters van de opdracht nettogebruik.
- Gebruik de opdracht cmdkey om de referenties toe te voegen aan Referentiebeheer. Voer dit uit vanaf een opdrachtregel onder de context van `runas`het serviceaccount, hetzij via een interactieve aanmelding, hetzij via .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Breng het aandeel rechtstreeks in kaart zonder een toegewezen stationsletter te gebruiken. Sommige toepassingen kunnen niet opnieuw verbinding maken met de drive letter goed, dus met behulp van de volledige UNC pad kan betrouwbaarder zijn. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Nadat u deze instructies hebt opgevolgd, ontvangt u mogelijk het volgende foutbericht wanneer u het nettogebruik voor het systeem-/netwerkserviceaccount uitvoert: "Systeemfout 1312 is opgetreden. Een opgegeven aanmeldingssessie bestaat niet. Het kan al zijn beëindigd." Als dit gebeurt, moet u ervoor zorgen dat de gebruikersnaam die wordt doorgegeven aan nettogebruik domeingegevens bevat (bijvoorbeeld: "[naam van het opslagaccount].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fout "U kopieert een bestand naar een bestemming die geen versleuteling ondersteunt"

Wanneer een bestand via het netwerk wordt gekopieerd, wordt het bestand gedecodeerd op de broncomputer, in plaintext verzonden en opnieuw versleuteld op de bestemming. Het is echter mogelijk dat u de volgende fout ziet wanneer u een versleuteld bestand probeert te kopiëren: "U kopieert het bestand naar een bestemming die geen versleuteling ondersteunt."

### <a name="cause"></a>Oorzaak
Dit probleem kan optreden als u EFS (Encrypting File System) gebruikt. Door BitLocker versleutelde bestanden kunnen worden gekopieerd naar Azure Files. Azure Files biedt echter geen ondersteuning voor NTFS EFS.

### <a name="workaround"></a>Tijdelijke oplossing
Als u een bestand via het netwerk wilt kopiëren, moet u het eerst decoderen. Gebruik een van de volgende methoden:

- Gebruik de opdracht **kopiëren /d.** Hiermee kunnen de versleutelde bestanden worden opgeslagen als gedecodeerde bestanden op de bestemming.
- Stel de volgende registersleutel in:
  - Pad = HKLM\Software\Policies\Microsoft\Windows\System
  - Waardetype = DWORD
  - Naam = CopyFileAllowDecryptedRemoteDestination
  - Waarde = 1

Houd er rekening mee dat het instellen van de registersleutel van invloed is op alle kopieerbewerkingen die zijn uitgevoerd op netwerkshares.

## <a name="slow-enumeration-of-files-and-folders"></a>Langzame opsomming van bestanden en mappen

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als er niet genoeg cache op client machine voor grote mappen.

### <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, past u de registerwaarde **directoryCacheEntrySizeMax** aan om het plaatsen van grotere mapvermeldingen in de clientmachine toe te staan:

- Locatie: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Waarde manen: DirectoryCacheEntrySizeMax 
- Waardetype:DWORD
 
 
U deze bijvoorbeeld instellen op 0x100000 en kijken of de prestaties beter worden.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fout AadDsTenantNotFound bij het inschakelen van Azure Active Directory Domain Service (AAD DS) verificatie voor Azure Files "Kan actieve tenants met tenant-id aad-tenant-id niet vinden"

### <a name="cause"></a>Oorzaak

Fout AadDsTenantNotFound treedt op wanneer u [Azure Active Directory Domain Services (Azure AD DS)-verificatie op Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) probeert in te schakelen op een opslagaccount waar AAD Domain Service [(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) niet is gemaakt op de AAD-tenant van het gekoppelde abonnement.  

### <a name="solution"></a>Oplossing

Aad DS inschakelen op de AAD-tenant van het abonnement waarop uw opslagaccount is geïmplementeerd. U hebt beheerdersbevoegdheden van de AAD-tenant nodig om een beheerd domein te maken. Als u niet de beheerder van de Azure AD-tenant bent, neemt u contact op met de beheerder en volgt u de stapsgewijze richtlijnen om Azure Active Directory Domain Services in te [schakelen met behulp van de Azure-portal.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Fout 'Systeemfout 1359 is opgetreden. Een interne fout die is ontvangen over SMB-toegang tot bestandsshares met Azure Active Directory Domain Service (AAD DS)-verificatie ingeschakeld

### <a name="cause"></a>Oorzaak

Fout 'Systeemfout 1359 is opgetreden. Er treedt een interne fout op wanneer u probeert verbinding te maken met uw bestandsshare met AAD DS-verificatie die is ingeschakeld tegen een AAD DS met dns-domeinnaam die begint met een numeriek teken. Als uw DNS-naam aad ds-domein bijvoorbeeld "1domein" is, krijgt u deze fout wanneer u probeert de bestandsshare te monteren met AAD-referenties. 

### <a name="solution"></a>Oplossing

Momenteel u overwegen uw AAD DS opnieuw te implementeren met behulp van een nieuwe dns-domeinnaam die van toepassing is met de onderstaande regels:
- Namen kunnen niet beginnen met een numeriek teken.
- Namen moeten 3 tot 63 tekens lang zijn.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
