---
title: Active Directory-verificatie via SMB inschakelen voor Azure-bestanden
description: Meer informatie over het inschakelen van identiteitsgebaseerde verificatie via SMB voor Azure-bestandsshares via Active Directory. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben vervolgens toegang tot Azure-bestandsshares met behulp van AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: dbcc204f71a9bfe4f48f51ab6af36014e130cb7f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548954"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Active Directory-verificatie via SMB inschakelen voor Azure-bestandsshares

[Azure Files](storage-files-introduction.md) ondersteunt verificatie op basis van identiteit via Server Message Block (SMB) via twee typen Domain Services: Azure Active Directory Domain Services (Azure AD DS) (GA) en Active Directory (AD) (preview). Dit artikel richt zich op de nieuw geïntroduceerde (preview) ondersteuning van het gebruik van Active Directory Domain Service voor verificatie naar Azure-bestandsshares. Als u Azure AD DS-verificatie (GA) wilt inschakelen voor Azure-bestandsshares, raadpleegt u [ons artikel over dit onderwerp](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Azure-bestand deelt alleen ondersteuningsverificatie met één domeinservice, azure active directory-domeinservice (Azure AD DS) of Active Directory (AD). 
>
> AD-identiteiten die worden gebruikt voor azure-verificatie voor bestandsdelen moeten worden gesynchroniseerd met Azure AD. Synchronisatie van wachtwoordhash is optioneel. 
> 
> AD-verificatie biedt geen ondersteuning voor verificatie met computeraccounts die zijn gemaakt in AD. 
> 
> AD-verificatie kan alleen worden ondersteund tegen één AD-forest waarbij het opslagaccount is geregistreerd. U hebt standaard alleen toegang tot Azure-bestandsshares met de AD-referenties vanuit één AD-forest. Als u toegang nodig hebt tot uw Azure-bestandsshare vanuit een ander forest, controleert u of u de juiste forestvertrouwensrelatie hebt geconfigureerd, [raadpleegt u veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) over meer informatie.  
> 
> AD-verificatie voor SMB-toegang en ACL-persistentie wordt ondersteund voor Azure-bestandsshares die worden beheerd door Azure File Sync.
>
> Azure Files ondersteunt Kerberos-verificatie met AD met RC4-HMAC-versleuteling. AES Kerberos encryptie wordt nog niet ondersteund.

Wanneer u AD voor Azure-bestandsshares inschakelt via SMB, kunnen uw AD-domein samengevoegde machines Azure-bestandsshares monteren met uw bestaande AD-referenties. Deze mogelijkheid kan worden ingeschakeld met een AD-omgeving die wordt gehost in on-prem-machines of wordt gehost in Azure.

AD-identiteiten die worden gebruikt om toegang te krijgen tot Azure-bestandsshares, moeten worden gesynchroniseerd met Azure AD om bestandsmachtigingen op shareniveau af te dwingen via het [RBAC-model (Standard Role-based Access Control).](../../role-based-access-control/overview.md) [Windows-achtige DACLs](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) voor bestanden/mappen die worden overgedragen vanaf bestaande bestandsservers, worden bewaard en afgedwongen. Deze functie biedt naadloze integratie met uw bedrijfs-AD-domeininfrastructuur. Als u on-prem-bestandsservers vervangt door Azure-bestandsshares, hebben bestaande gebruikers toegang tot Azure-bestandsshares van hun huidige clients met één aanmeldingservaring, zonder dat de ingebruikzijnde referenties worden gewijzigd.  
 
## <a name="prerequisites"></a>Vereisten 

Voordat u AD-verificatie inschakelt voor Azure-bestandsshares, controleert u of u de volgende vereisten hebt voltooid: 

- Selecteer of maak uw AD-omgeving en synchroniseer deze met Azure AD. 

    U de functie inschakelen in een nieuwe of bestaande AD-omgeving. Identiteiten die worden gebruikt voor toegang, moeten worden gesynchroniseerd met Azure AD. De Azure AD-tenant en het bestandsaandeel dat u opent, moeten aan hetzelfde abonnement zijn gekoppeld. 

    Als u een AD-domeinomgeving wilt instellen, raadpleegt u [Overzicht van Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Als u uw AD niet hebt gesynchroniseerd met uw Azure AD, volgt u de richtlijnen in [Wat is hybride identiteit met Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) Om de gewenste verificatiemethode en de installatieoptie Azure AD Connect te bepalen. 

- Domein-join een on-premises machine of een Azure VM naar AD (ook wel AD DS genoemd). 

    Als u toegang wilt krijgen tot een bestandsshare met behulp van AD-referenties van een machine of vm, moet uw apparaat zijn verbonden met AD. Raadpleeg [Join a-computer naar een domein](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)voor informatie over het deelnemen aan een domein. 

- Selecteer of maak een Azure-opslagaccount in [een ondersteund gebied](#regional-availability). 

    Controleer of het opslagaccount met uw bestandsshares nog niet is geconfigureerd voor Azure AD DS-verificatie. Als Azure Files Azure AD DS-verificatie is ingeschakeld voor het opslagaccount, moet het worden uitgeschakeld voordat u overstapt op het gebruik van AD. Dit houdt in dat bestaande ACL's die zijn geconfigureerd in de Azure AD DS-omgeving opnieuw moeten worden geconfigureerd voor de juiste handhaving van machtigingen.
    
    Zie [Een bestandsshare maken in Azure-bestanden](storage-how-to-create-file-share.md)voor informatie over het maken van een nieuwe bestandsshare.
    
    Voor optimale prestaties raden we u aan het opslagaccount te implementeren in dezelfde regio als de VM van waaruit u van plan bent toegang te krijgen tot het aandeel. 

- Controleer de connectiviteit door Azure-bestandsshares te monteren met uw opslagaccountsleutel. 

    Als u wilt controleren of uw apparaat en bestandsshare correct zijn geconfigureerd, probeert u het bestandsshare te monteren met uw opslagaccountsleutel. Zie [Een Azure-bestandsshare gebruiken met Windows](storage-how-to-use-files-windows.md)voor meer informatie.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files AD-verificatie (preview) is beschikbaar in [alle regio's in de openbare cloud.](https://azure.microsoft.com/global-infrastructure/regions/)

## <a name="workflow-overview"></a>Overzicht van werkstroom

Voordat u AD-verificatie over SMB voor Azure-bestandsshares inschakelt, raden we u aan de vereisten door [te](#prerequisites) nemen en ervoor te zorgen dat u alle stappen hebt voltooid. De vereisten valideren dat uw AD-, Azure AD- en Azure-opslagomgevingen correct zijn geconfigureerd. 

Geef vervolgens toegang tot Azure Files-bronnen met AD-referenties: 

- Azure Files AD-verificatie inschakelen in uw opslagaccount.  

- Toegangsmachtigingen voor een aandeel toewijzen aan de Azure AD-identiteit (een gebruiker, groep of serviceprincipal) die synchroon loopt met de doel-AD-identiteit. 

- Configureer ACL's boven SMB voor mappen en bestanden. 

- Een Azure-bestandsshare monteren vanuit een AD-domein dat is samengevoegd als VM. 

In het volgende diagram wordt de end-to-end-werkstroom weergegeven voor het inschakelen van Azure AD-verificatie via SMB voor Azure-bestandsshares. 

![AD-werkstroomdiagram bestanden](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD-verificatie via SMB voor Azure-bestandsshares wordt alleen ondersteund op machines of VM's die worden uitgevoerd op nieuwe versies van het besturingssysteem dan Windows 7 of Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>AD-verificatie inschakelen voor uw account 

Als u AD-verificatie via SMB voor Azure-bestandsshares wilt inschakelen, moet u eerst uw opslagaccount registreren bij AD en vervolgens de vereiste domeineigenschappen instellen op het opslagaccount. Wanneer de functie is ingeschakeld op het opslagaccount, is deze van toepassing op alle nieuwe en bestaande bestandsshares in het account. Gebruiken `join-AzStorageAccountForAuth` om de functie in te schakelen. In de sectie hieronder vindt u de gedetailleerde beschrijving van de end-to-end workflow. 

> [!IMPORTANT]
> De `Join-AzStorageAccountForAuth` cmdlet zal wijzigingen aanbrengen in uw AD-omgeving. Lees de volgende uitleg om beter te begrijpen wat het doet om ervoor te zorgen dat u de juiste machtigingen hebt om de opdracht uit te voeren en dat de toegepaste wijzigingen overeenkomen met het nalevings- en beveiligingsbeleid. 

De `Join-AzStorageAccountForAuth` cmdlet voert het equivalent uit van een offline domein dat lid wordt namens het aangegeven opslagaccount. Het maakt een account aan in uw AD-domein, een [computeraccount](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) of een [service-aanmeldingsaccount.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Het aangemaakt AD-account vertegenwoordigt het opslagaccount in het AD-domein. Als het AD-account wordt gemaakt onder een AD-organisatie-eenheid (OU) die het verlopen van het wachtwoord afdwingt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd. Als het wachtwoord van het AD-account niet wordt bijgewerkt, leidt dit tot verificatiefouten bij het openen van Azure-bestandsshares. Zie Het wachtwoord van het [AD-account bijwerken](#update-ad-account-password)voor meer informatie over het bijwerken van het wachtwoord .

U het volgende script gebruiken om de registratie uit te voeren en de functie in te schakelen of, als alternatief, u handmatig de bewerkingen uitvoeren die het script zou uitvoeren. Deze bewerkingen worden beschreven in de sectie naar aanleiding van het script. Je hoeft niet beide te doen.

### <a name="1-check-prerequisites"></a>1. Controleer de vereisten
- [De AzFilesHybrid-module downloaden en uitpakken](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installeer en voer de module uit in een apparaat dat is gekoppeld aan AD met AD-referenties met machtigingen voor het maken van een service-aanmeldingsaccount of een computeraccount in het doel-AD.
-  Voer het script uit met een AD-referentie die is gesynchroniseerd met uw Azure-AD. De AD-referentie moet de eigenaar van het opslagaccount of de rbac-functiemachtigingen voor inzender hebben.
- Controleer of uw opslagaccount zich in een [ondersteund gebied bevindt.](#regional-availability)

### <a name="2-domain-join-your-storage-account"></a>2. Domein wordt lid van uw opslagaccount
Vergeet niet om de tijdelijke aanduidingswaarden te vervangen door die van u in de onderstaande parameters voordat u deze uitvoert in PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

In de volgende beschrijving worden `Join-AzStorageAccountForAuth` alle acties samengevat die worden uitgevoerd wanneer de cmdlet wordt uitgevoerd. U deze stappen handmatig uitvoeren als u de opdracht liever niet gebruikt:

> [!NOTE]
> Als u het `Join-AzStorageAccountForAuth` bovenstaande script al hebt uitgevoerd, gaat u naar de volgende sectie "3. Controleer of de functie is ingeschakeld". U hoeft de onderstaande bewerkingen niet opnieuw uit te voeren.

#### <a name="a-checking-environment"></a>a. Omgeving controleren

Ten eerste, het controleert uw omgeving. Er wordt specifiek gecontroleerd of de [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) is geïnstalleerd en of de shell wordt uitgevoerd met beheerdersbevoegdheden. Vervolgens wordt gecontroleerd of de [Az.Storage 1.11.1-preview-module](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) is geïnstalleerd en wordt deze geïnstalleerd als dit niet het is. Als deze controles passeren, dan zal het controleren van uw AD om te zien of er een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of [service aanmeldingsaccount](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) die al is gemaakt met SPN / UPN als "cifs/your-storage-account-name-here.file.core.windows.net". Als het account niet bestaat, wordt er een gemaakt zoals beschreven in sectie b hieronder.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Handmatig een identiteit maken die het opslagaccount in uw AD vertegenwoordigt

Als u dit account handmatig wilt maken, maakt u `New-AzStorageAccountKey -KeyName kerb1`een nieuwe kerberos-sleutel voor uw opslagaccount met behulp van . Gebruik vervolgens die kerberos-toets als wachtwoord voor uw account. Deze sleutel wordt alleen gebruikt tijdens het instellen en kan niet worden gebruikt voor controle- of gegevensvlakbewerkingen ten opzichte van het opslagaccount.

Zodra u die sleutel hebt, maakt u een service- of computeraccount onder uw organisatieorganisatie. Gebruik de volgende specificatie: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Wachtwoord: Kerberos-sleutel voor uw opslagaccount.

Als uw organisatie het wachtwoord tot een vervaldatum afdwingt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd om verificatiefouten bij het openen van Azure-bestandsshares te voorkomen. Zie [Het wachtwoord van het AD-account bijwerken](#update-ad-account-password) voor meer informatie.

Houd de SID van het nieuw gemaakte account, je hebt het nodig voor de volgende stap. De AD-identiteit die u zojuist hebt gemaakt en die het opslagaccount vertegenwoordigt, hoeft niet te worden gesynchroniseerd met Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. De functie in uw opslagaccount inschakelen

Het script zou dan de functie op uw opslagaccount inschakelen. Als u deze instelling handmatig wilt uitvoeren, geeft u enkele configuratiegegevens op voor de domeineigenschappen in de volgende opdracht en voert u deze uit. De opslagaccount SID die vereist is in de volgende opdracht is de SID van de identiteit die u in AD hebt gemaakt (sectie b hierboven).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Controleer of de functie is ingeschakeld

U controleren of de functie is ingeschakeld in uw opslagaccount, u het volgende script gebruiken:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Je hebt de functie nu in je opslagaccount ingeschakeld. Hoewel de functie is ingeschakeld, moet u nog steeds aanvullende acties uitvoeren om de functie goed te kunnen gebruiken.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt nu AD-verificatie via SMB ingeschakeld en een aangepaste rol toegewezen die toegang biedt tot een Azure-bestandsshare met een AD-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestandsshare, volgt u de instructies in de [toegangsmachtigingen toewijzen](#assign-access-permissions-to-an-identity) om een identiteit te gebruiken en [NTFS-machtigingen configureren voor SMB-secties.](#configure-ntfs-permissions-over-smb)

## <a name="update-ad-account-password"></a>Wachtwoord van het AD-account bijwerken

Als u de AD-identiteit/-account hebt geregistreerd die uw opslagaccount vertegenwoordigt onder een organisatie-eenheid die de vervaldatum van het wachtwoord afdwingt, moet u het wachtwoord roteren vóór de maximale wachtwoordleeftijd. Als u het wachtwoord van het AD-account niet bijwerkt, leidt dit tot verificatiefouten om toegang te krijgen tot Azure-bestandsshares.  

Als u wachtwoordrotatie wilt `Update-AzStorageAccountADObjectPassword` activeren, u de opdracht uitvoeren vanuit de AzFilesHybrid-module. De cmdlet voert acties uit die vergelijkbaar zijn met de rotatie van de opslagaccountsleutel. Het krijgt de tweede Kerberos-sleutel van het opslagaccount en gebruikt het om het wachtwoord van het geregistreerde account in AD bij te werken. Vervolgens regenereert het de doelKerberos-toets van het opslagaccount en wordt het wachtwoord van het geregistreerde account in AD bijgewerkt. U moet deze cmdlet uitvoeren in een samengevoegde AD-domeinomgeving.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van AD via SMB:

- [Overzicht van azure files-verificatieondersteuning voor SMB-toegang](storage-files-active-directory-overview.md)
- [Veelgestelde vragen](storage-files-faq.md)
