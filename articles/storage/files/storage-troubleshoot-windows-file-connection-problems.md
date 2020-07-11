---
title: Problemen met Azure Files oplossen in Windows | Microsoft Docs
description: Problemen met Azure Files oplossen in Windows
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 8a8fff374edab7e307cd6dc8fb9aa4a4f974d09c
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224686"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Problemen met Azure Files in Windows oplossen

In dit artikel vindt u algemene problemen die betrekking hebben op Microsoft Azure-bestanden wanneer u verbinding maakt vanaf Windows-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. Naast de stappen voor probleem oplossing in dit artikel, kunt u ook [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)gebruiken   om ervoor te zorgen dat de Windows-client omgeving voldoet aan de vereisten. AzFileDiagnostics automatiseert de detectie van de meeste symptomen die in dit artikel worden genoemd en helpt u bij het instellen van uw omgeving om optimaal gebruik te maken van de prestaties. U kunt deze informatie ook vinden in de [probleem oplosser voor Azure files-shares](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) die stappen biedt om u te helpen bij het verbinden/koppelen van Azure files shares.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fout 5 bij het koppelen van een Azure-bestands share

Wanneer u een bestands share probeert te koppelen, wordt mogelijk de volgende fout weer gegeven:

- Systeemfout 5 heeft zich voorgedaan. Toegang wordt geweigerd.

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: niet-versleuteld communicatie kanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Voor Windows 8, Windows Server 2012 en latere versies van elk systeem wordt onderhandeld over aanvragen die SMB 3.0 omvatten, wat ondersteuning biedt voor versleuteling.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Verbinding maken vanaf een client die ondersteuning biedt voor SMB-versleuteling (Windows 8, Windows Server 2012 of hoger) of verbinding maken vanaf een virtuele machine in hetzelfde Data Center als het Azure-opslag account dat wordt gebruikt voor de Azure-bestands share.
2. Controleer of de instelling [beveiligde overdracht vereist](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is uitgeschakeld op het opslag account als de client geen SMB-versleuteling ondersteunt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security) voor meer informatie.

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Oorzaak 3: machtigingen op share niveau zijn onjuist bij het gebruik van verificatie op basis van identiteiten

Als gebruikers toegang hebben tot de Azure-bestands share met Active Directory (AD) of Azure Active Directory Domain Services-verificatie (Azure AD DS), mislukt de toegang tot de bestands share met de fout ' toegang geweigerd ' als machtigingen op share niveau onjuist zijn. 

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3

Zie [toegangs machtigingen toewijzen aan een identiteit](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)om de machtigingen op share niveau bij te werken.

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fout 53, fout 67 of fout 87 bij het koppelen of ontkoppelen van een Azure-bestands share

Wanneer u probeert een bestands share te koppelen vanuit een on-premises of vanuit een ander Data Center, kunnen de volgende fouten optreden:

- Systeemfout 53 heeft zich voorgedaan. Het netwerkpad is niet gevonden.
- Systeemfout 67 heeft zich voorgedaan. De naam van het netwerk kan niet worden gevonden.
- Systeemfout 87 heeft zich voorgedaan. De parameter is onjuist.

### <a name="cause-1-port-445-is-blocked"></a>Oorzaak 1: poort 445 is geblokkeerd

Systeem fout 53 of systeem fout 67 kan optreden als poort 445 uitgaande communicatie met een Azure Files Data Center wordt geblokkeerd. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Gebruik het hulp programma [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) of de cmdlet om te controleren of uw firewall of Internet provider poort 445 blokkeert `Test-NetConnection` . 

Als u de `Test-NetConnection` cmdlet wilt gebruiken, moet de module Azure PowerShell zijn geïnstalleerd. zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps) voor meer informatie. Vergeet niet om `<your-storage-account-name>` en `<your-resource-group-name>` te vervangen door de betreffende namen van uw opslagaccount.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Als de verbinding is geslaagd, hoort u de volgende uitvoer te zien:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> De bovenstaande opdracht retourneert het huidige IP-adres van het opslagaccount. Dit IP-adres blijft niet noodzakelijkerwijs hetzelfde en kan op elk moment veranderen. Neem dit IP-adres niet op in scripts of in de firewallconfiguratie.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

#### <a name="solution-1---use-azure-file-sync"></a>Oplossing 1: Azure File Sync gebruiken
Azure File Sync kunt uw on-premises Windows-Server omzetten in een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. Azure File Sync werkt via poort 443 en kan daarom worden gebruikt als tijdelijke oplossing voor toegang tot Azure Files vanaf clients waarbij poort 445 is geblokkeerd. [Meer informatie over het instellen van Azure file sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Oplossing 2: VPN gebruiken
Door een VPN-verbinding met uw specifieke opslag account in te stellen, zal het verkeer via internet een beveiligde tunnel passeren. Volg de [instructies voor het instellen van VPN](storage-files-configure-p2s-vpn-windows.md) om toegang te krijgen tot Azure Files vanuit Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Oplossing 3: Poort 445 deblokkeren met behulp van uw internetprovider/IT-beheerder
Werk samen met uw IT-afdeling of provider voor het openen van poort 445 uitgaand verkeer naar [Azure IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Oplossing 4: Hulpprogramma’s op basis van REST API gebruiken, zoals Storage Explorer/PowerShell
Azure Files biedt ook ondersteuning voor REST naast SMB. REST-toegang werkt via poort 443 (standaard TCP). Er zijn verschillende hulpprogramma's die zijn geschreven met REST API die een uitgebreide UI-ervaring bieden. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) is een van beide. [Download en installeer Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met de bestandsshare die door Azure Files wordt ondersteund. U kunt ook [Power shell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) gebruiken die ook gebruikers rest API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Oorzaak 2: NTLMv1 is ingeschakeld

Systeem fout 53 of systeem fout 87 kan optreden als NTLMv1-communicatie is ingeschakeld op de client. Azure Files biedt alleen ondersteuning voor NTLMv2-verificatie. Als NTLMv1 is ingeschakeld, is de client minder veilig. Om deze reden wordt communicatie geblokkeerd voor Azure Files. 

Als u wilt vaststellen of dit de oorzaak van de fout is, controleert u of de volgende registersubsleutel is ingesteld op de waarde 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Zie het onderwerp [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) in TechNet voor meer informatie.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Zet de waarde **LmCompatibilityLevel** terug naar de standaardwaarde 3 in de volgende registersubsleutel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Fout 1816-onvoldoende quotum beschikbaar om deze opdracht te verwerken

### <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer u de bovengrens van gelijktijdige open ingangen bereikt die zijn toegestaan voor een bestand of map op de Azure-bestands share. Zie [Azure Files-schaalbaarheidsdoelen](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) voor meer informatie.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige open ingangen door enkele grepen te sluiten en probeer het opnieuw. Zie [Microsoft Azure Storage controle lijst voor prestaties en schaal baarheid](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

Gebruik de Power shell [-cmdlet Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) om de geopende ingangen voor een bestands share, map of bestand weer te geven.  

Gebruik de Power shell [-cmdlet close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) om open ingangen voor een bestands share, map of bestand te sluiten.

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fout ' geen toegang ' wanneer u een Azure-bestands share probeert te openen of verwijderen  
Wanneer u probeert een Azure-bestands share in de portal te openen of verwijderen, wordt mogelijk de volgende fout weer gegeven:

Geen toegang  
Fout code: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 1: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security) voor meer informatie.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Oorzaak 2: uw gebruikers account heeft geen toegang tot het opslag account

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Blader naar het opslag account waar de Azure-bestands share zich bevindt, klik op **toegangs beheer (IAM)** en controleer of uw gebruikers account toegang heeft tot het opslag account. Zie [uw opslag account beveiligen met op rollen gebaseerd Access Control (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)voor meer informatie.

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Kan een bestand of map in een Azure-bestandsshare niet verwijderen
Wanneer u een bestand probeert te verwijderen, wordt mogelijk de volgende fout weer gegeven:

De opgegeven resource is gemarkeerd voor verwijdering door een SMB-client.

### <a name="cause"></a>Oorzaak
Dit probleem treedt doorgaans op als het bestand of de map een geopende ingang heeft. 

### <a name="solution"></a>Oplossing

Als de SMB-clients alle geopende ingangen hebben gesloten en het probleem blijft optreden, voert u de volgende handelingen uit:

- Gebruik de Power shell [-cmdlet Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) om open ingangen weer te geven.

- Gebruik de Power shell [-cmdlet close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) om open ingangen te sluiten. 

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Bestanden worden langzaam gekopieerd van en naar Azure Files in Windows

Mogelijk ziet u trage prestaties wanneer u bestanden probeert over te brengen naar de Azure File-Service.

- Als u geen specifieke minimale I/O-grootte vereiste hebt, raden we u aan om 1 MiB te gebruiken als de I/O-grootte voor optimale prestaties.
-   Als u de uiteindelijke grootte kent van een bestand dat u uitbreidt met schrijf bewerkingen, en uw software geen compatibiliteits problemen heeft wanneer de niet-genoteerde staart van het bestand nullen bevat, moet u de bestands grootte vooraf instellen in plaats van elke schrijf bewerking uit te voeren.
-   Gebruik de juiste Kopieer methode:
    -   Gebruik [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor elke overdracht tussen twee bestands shares.
    -   Gebruik [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) tussen bestands shares op een on-premises computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Overwegingen voor Windows 8,1 of Windows Server 2012 R2

Zorg ervoor dat de [KB3114025](https://support.microsoft.com/help/3114025) -hotfix is geïnstalleerd voor clients met Windows 8,1 of windows server 2012 R2. Deze hotfix verbetert de prestaties van het maken en sluiten van ingangen.

U kunt het volgende script uitvoeren om te controleren of de hotfix is geïnstalleerd:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Als de hotfix is geïnstalleerd, wordt de volgende uitvoer weer gegeven:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Voor installatie kopieën van Windows Server 2012 R2 in azure Marketplace is een hotfix-KB3114025 standaard geïnstalleerd, vanaf december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Geen map met een stationsletter in ' mijn computer ' of ' deze PC '

Als u een Azure-bestands share als beheerder toewijst met behulp van net use, lijkt het alsof de share ontbreekt.

### <a name="cause"></a>Oorzaak

Windows Verkenner wordt standaard niet uitgevoerd als beheerder. Als u net use uitvoert vanaf een opdracht prompt met beheerders rechten, wijst u het netwerk station toe als beheerder. Omdat toegewezen stations gebruikers gericht zijn, worden de stations niet weer gegeven in de gebruikers account die is aangemeld als deze zijn gekoppeld onder een ander gebruikers account.

### <a name="solution"></a>Oplossing
Koppel de share vanuit een niet-beheerders opdracht regel. U kunt ook [Dit TechNet-onderwerp](https://technet.microsoft.com/library/ee844140.aspx) volgen om de register waarde **EnableLinkedConnections** te configureren.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Opdracht net use mislukt als het opslag account een slash (voorwaarts) bevat

### <a name="cause"></a>Oorzaak

De opdracht net use interpreteert een slash (/) als een opdracht regel optie. Als de naam van uw gebruikers account begint met een slash, mislukt de stationstoewijzing.

### <a name="solution"></a>Oplossing

U kunt een van de volgende stappen gebruiken om het probleem te omzeilen:

- Voer de volgende PowerShell-opdracht uit:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Vanuit een batch-bestand kunt u de opdracht op deze manier uitvoeren:

  `Echo new-smbMapping ... | powershell -command –`

- Plaats dubbele aanhalings tekens rond de sleutel om dit probleem te omzeilen, tenzij de slash het eerste teken is. Als dat het geval is, gebruikt u de interactieve modus en voert u uw wacht woord afzonderlijk in of genereert u de sleutels opnieuw om een sleutel op te halen die niet begint met een slash.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Toepassing of service kan geen toegang krijgen tot een gekoppeld Azure Files station

### <a name="cause"></a>Oorzaak

Stations zijn gekoppeld per gebruiker. Als uw toepassing of service wordt uitgevoerd onder een ander gebruikers account dan die waarmee het station is gekoppeld, wordt het station niet weer geven door de toepassing.

### <a name="solution"></a>Oplossing

Gebruik een van de volgende oplossingen:

-   Koppel het station van hetzelfde gebruikers account dat de toepassing bevat. U kunt een hulp programma zoals PsExec gebruiken.
- Geef de naam en sleutel van het opslag account op in de para meters gebruikers naam en wacht woord van de opdracht net use.
- Gebruik de opdracht cmdkey om de referenties toe te voegen aan referentie beheer. Voer dit uit vanaf een opdracht regel in de context van het service account, hetzij via een interactieve aanmelding ofwel met behulp van `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Wijs de share rechtstreeks toe zonder een toegewezen stationsletter te gebruiken. Het is mogelijk dat sommige toepassingen niet opnieuw verbinding maken met de stationsletter, zodat het volledige UNC-pad mogelijk betrouwbaarder is. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Nadat u deze instructies hebt gevolgd, wordt het volgende fout bericht weer gegeven wanneer u net use uitvoert voor het systeem-of netwerk service account: ' systeem fout 1312 is opgetreden. Een opgegeven aanmeldings sessie bestaat niet. Mogelijk is deze al beëindigd. " Als dit het geval is, moet u ervoor zorgen dat de gebruikers naam die wordt door gegeven aan net use, domein gegevens bevat (bijvoorbeeld: [naam van het opslag account]. file. core. Windows. net ").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fout ' u kopieert een bestand naar een doel dat geen ondersteuning biedt voor versleuteling '

Wanneer een bestand via het netwerk wordt gekopieerd, wordt het bestand ontsleuteld op de bron computer, verzonden als Lees bare tekst en opnieuw versleuteld op de bestemming. Het is echter mogelijk dat u de volgende fout ziet wanneer u een versleuteld bestand probeert te kopiëren: ' u kopieert het bestand naar een bestemming die geen ondersteuning biedt voor versleuteling '.

### <a name="cause"></a>Oorzaak
Dit probleem kan optreden als u Encrypting File System (EFS) gebruikt. Met BitLocker versleutelde bestanden kunnen naar Azure Files worden gekopieerd. Azure Files ondersteunt echter geen NTFS EFS.

### <a name="workaround"></a>Tijdelijke oplossing
Als u een bestand wilt kopiëren via het netwerk, moet u het eerst decoderen. Hanteer één van de volgende methoden:

- Gebruik de opdracht **copy/d** . Hierdoor kunnen de versleutelde bestanden worden opgeslagen als ontsleutelde bestanden op de bestemming.
- Stel de volgende registersleutel in:
  - Pad = HKLM\Software\Policies\Microsoft\Windows\System
  - Waardetype = DWORD
  - Naam = CopyFileAllowDecryptedRemoteDestination
  - Waarde = 1

Houd er rekening mee dat het instellen van de register sleutel van invloed is op alle kopieer bewerkingen die worden uitgevoerd op netwerk shares.

## <a name="slow-enumeration-of-files-and-folders"></a>Trage inventarisatie van bestanden en mappen

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als er onvoldoende cache op de client computer is voor grote mappen.

### <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de register waarde **DirectoryCacheEntrySizeMax** aan te passen, zodat er grotere mappen worden opgeslagen in de cache van de client computer:

- Locatie: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Waarde Mane: DirectoryCacheEntrySizeMax 
- Waardetype: DWORD
 
 
U kunt dit bijvoorbeeld instellen op 0x100000 en zien of de prestaties beter worden.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fout AadDsTenantNotFound bij het inschakelen van de verificatie van de Azure Active Directory Domain Service (AAD DS) voor Azure Files ' kan geen actieve tenants vinden met de Tenant-id Aad-Tenant-id '

### <a name="cause"></a>Oorzaak

Fout AadDsTenantNotFound treedt op wanneer u probeert [Azure Active Directory Domain Services-verificatie (Azure AD DS) in azure files in te scha kelen](storage-files-identity-auth-active-directory-domain-service-enable.md) op een opslag account waarin Aad [-domein service (Aad DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) niet is gemaakt op de Aad-Tenant van het gekoppelde abonnement.  

### <a name="solution"></a>Oplossing

Schakel AAD DS in op de AAD-Tenant van het abonnement waarop uw opslag account is geïmplementeerd. U hebt beheerders bevoegdheden van de AAD-Tenant nodig om een beheerd domein te maken. Als u niet de beheerder van de Azure AD-Tenant bent, neemt u contact op met de beheerder en volgt u de stapsgewijze richt lijnen om Azure Active Directory Domain Services in te [scha kelen met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Fout: systeem fout 1359 is opgetreden. Er is een interne fout ontvangen over SMB-toegang tot bestands shares waarvoor AAD DS-verificatie (Azure Active Directory Domain Service) is ingeschakeld

### <a name="cause"></a>Oorzaak

Fout: systeem fout 1359 is opgetreden. Er treedt een interne fout op wanneer u verbinding probeert te maken met uw bestands share waarbij AAD DS-verificatie is ingeschakeld voor een AAD DS met domein-DNS-naam, beginnend met een numeriek teken. Als de DNS-naam van uw AAD DS-domein bijvoorbeeld ' 1domain ' is, krijgt u deze fout melding wanneer u probeert de bestands share te koppelen aan de hand van AAD-referenties. 

### <a name="solution"></a>Oplossing

Op dit moment kunt u overwegen om uw AAD DS opnieuw te implementeren met een nieuwe DNS-domein naam die van toepassing is op de volgende regels:
- Namen mogen niet beginnen met een numeriek teken.
- Namen moeten tussen de 3 en 63 tekens lang zijn.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Kan Azure Files niet koppelen aan AD-referenties 

### <a name="self-diagnostics-steps"></a>Stappen voor zelf diagnostische gegevens
Zorg er eerst voor dat u alle vier de stappen hebt gevolgd om [Azure files AD-verificatie in te scha kelen](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Probeer vervolgens de [Azure-bestands share te koppelen met de sleutel van het opslag account](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Als u de koppeling niet hebt gemaakt, downloadt u [AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) om u te helpen bij het valideren van de client omgeving, detecteert u de niet-compatibele client configuratie die toegang zou hebben tot de Azure files, bevat prescriptieve richt lijnen voor zelf herstel en worden de diagnostische traceringen verzameld.

Ten derde kunt u de cmdlet debug-AzStorageAccountAuth uitvoeren om een set basis controles uit te voeren op uw AD-configuratie met de aangemelde AD-gebruiker. Deze cmdlet wordt ondersteund met [versie AzFilesHybrid v 0.1.2 en hoger](https://github.com/Azure-Samples/azure-files-samples/releases). U moet deze cmdlet uitvoeren met een AD-gebruiker die de machtiging Eigenaar heeft voor het doelopslagaccount.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
De cmdlet voert deze controles hieronder uit, en biedt richt lijnen voor fouten:
1. CheckPort445Connectivity: Controleer of poort 445 is geopend voor de SMB-verbinding
2. CheckDomainJoined: Controleer of de client computer lid is van een domein dat is gekoppeld aan AD
3. CheckADObject: Bevestig dat er een object in de Active Directory is dat het opslag account vertegenwoordigt en de juiste SPN (Service Principal Name) heeft.
4. CheckGetKerberosTicket: er wordt geprobeerd een Kerberos-ticket op te halen om verbinding te maken met het opslag account 
5. CheckADObjectPasswordIsCorrect: Zorg ervoor dat het wacht woord dat is geconfigureerd voor de AD-identiteit die het opslag account vertegenwoordigt, overeenkomt met de sleutel kerb1 of kerb2 van het opslag account.
6. CheckSidHasAadUser: Controleer of de aangemelde AD-gebruiker is gesynchroniseerd met Azure AD. Als u wilt controleren of een specifieke AD-gebruiker is gesynchroniseerd met Azure AD, kunt u de-gebruikers naam en-domein opgeven in de invoer parameters.
7. CheckAadUserHasSid: Controleer of een Azure AD-gebruiker een SID heeft in AD. deze controle vereist dat gebruiker de object-id van de Azure AD-gebruiker met para meter-ObjectId opgeeft. 
8. CheckStorageAccountDomainJoined: Controleer de eigenschappen van het opslag account om te zien of AD-verificatie is ingeschakeld en of de AD-eigenschappen van het account zijn ingevuld.

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Kan de machtigingen voor mappen en bestands niveau (Windows-Acl's) niet configureren met Windows bestanden Verkenner

### <a name="symptom"></a>Symptoom

U kunt de volgende symptomen ondervinden bij het configureren van Windows-Acl's met bestanden Verkenner op een gekoppelde bestands share:
- Nadat u op de machtiging bewerken hebt geklikt op het tabblad Beveiliging, wordt de wizard machtiging niet geladen. 
- Wanneer u probeert een nieuwe gebruiker of groep te selecteren, wordt in de domein locatie niet het juiste AD DS domein weer gegeven. 

### <a name="solution"></a>Oplossing

U wordt aangeraden om het [icacls-hulp programma](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) te gebruiken om de machtigingen voor het Directory/bestands niveau als tijdelijke oplossing te configureren. 

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
