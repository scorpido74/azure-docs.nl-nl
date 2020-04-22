---
title: Active Directory-verificatie via SMB inschakelen voor Azure-bestanden
description: Meer informatie over het inschakelen van identiteitsgebaseerde verificatie via SMB voor Azure-bestandsshares via Active Directory. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben vervolgens toegang tot Azure-bestandsshares met behulp van AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: 44debc299054568769bfbe6cfc089cc528594274
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677080"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>On-premises Active Directory Domain Services-verificatie via SMB inschakelen voor Azure-bestandsshares

[Azure Files](storage-files-introduction.md) ondersteunt verificatie op basis van identiteit via Server Message Block (SMB) via twee typen Domain Services: Azure Active Directory Domain Services (Azure AD DS) en on-premises Active Directory Domain Services (AD DS) (preview). Dit artikel richt zich op de nieuw geïntroduceerde (preview) ondersteuning van het gebruik van Active Directory Domain Service voor verificatie naar Azure-bestandsshares. Als u Azure AD DS-verificatie (GA) wilt inschakelen voor Azure-bestandsshares, raadpleegt u [ons artikel over dit onderwerp](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Azure-bestand deelt alleen ondersteuningsverificatie met één domeinservice, azure active directory-domeinservice (Azure AD DS) of on-premises Active Directory Domain Services (AD DS). 
>
> AD DS-identiteiten die worden gebruikt voor azure-verificatie van bestandsdelen, moeten worden gesynchroniseerd met Azure AD. Synchronisatie van wachtwoordhash is optioneel. 
> 
> On-premises AD DS-verificatie biedt geen ondersteuning voor verificatie met computeraccounts die zijn gemaakt in AD DS. 
> 
> On-premises AD DS-verificatie kan alleen worden ondersteund tegen één AD-forest waar het opslagaccount is geregistreerd. U hebt standaard alleen toegang tot Azure-bestandsshares met de AD DS-referenties vanuit één forest. Als u toegang nodig hebt tot uw Azure-bestandsshare vanuit een ander forest, controleert u of u de juiste forestvertrouwensrelatie hebt geconfigureerd, [raadpleegt u veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) over meer informatie.  
> 
> AD DS-verificatie voor SMB-toegang en ACL-persistentie wordt ondersteund voor Azure-bestandsshares die worden beheerd door Azure File Sync.
>
> Azure Files ondersteunt Kerberos-verificatie met AD met RC4-HMAC-versleuteling. AES Kerberos encryptie wordt nog niet ondersteund.

Wanneer u AD DS voor Azure-bestandsshares inschakelt via SMB, kunnen uw door AD-DS aangesloten machines Azure-bestandsshares monteren met uw bestaande AD-referenties. Deze mogelijkheid kan worden ingeschakeld met een AD DS-omgeving die wordt gehost in on-prem-machines of wordt gehost in Azure.

Identiteiten die worden gebruikt om toegang te krijgen tot Azure-bestandsshares, moeten worden gesynchroniseerd met Azure AD om bestandsmachtigingen op shareniveau af te dwingen via het [RBAC-model (Role-based Access Control).](../../role-based-access-control/overview.md) [Windows-achtige DACLs](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) voor bestanden/mappen die worden overgedragen vanaf bestaande bestandsservers, worden bewaard en afgedwongen. Deze functie biedt naadloze integratie met uw zakelijke AD DS-omgeving. Als u on-prem-bestandsservers vervangt door Azure-bestandsshares, hebben bestaande gebruikers toegang tot Azure-bestandsshares van hun huidige clients met één aanmeldingservaring, zonder dat de ingebruikzijnde referenties worden gewijzigd.  

> [!NOTE]
> Om u te helpen bij het instellen van AZURE Files AD-verificatie voor een aantal veelvoorkomende use cases, hebben we [twee video's](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) gepubliceerd met stapsgewijze richtlijnen:
> - On-premises bestandsservers vervangen door Azure Files (inclusief installatie op privékoppeling voor bestanden en AD-verificatie)
> - Azure-bestanden gebruiken als profielcontainer voor Windows Virtual Desktop (inclusief installatie op AD-verificatie en FsLogix-configuratie)

## <a name="prerequisites"></a>Vereisten 

Voordat u AD DS-verificatie inschakelt voor Azure-bestandsshares, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid: 

- Selecteer of maak uw AD DS-omgeving en [synchroniseer deze met Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    U de functie inschakelen in een nieuwe of bestaande on-premises AD DS-omgeving. Identiteiten die worden gebruikt voor toegang, moeten worden gesynchroniseerd met Azure AD. De Azure AD-tenant en het bestandsaandeel dat u opent, moeten aan hetzelfde abonnement zijn gekoppeld. 

    Als u een AD-domeinomgeving wilt instellen, raadpleegt u [Overzicht van Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Als u uw AD niet hebt gesynchroniseerd met uw Azure AD, volgt u de richtlijnen in [azure AD Connect en de installatieroadmap azure AD Connect-status](../../active-directory/hybrid/how-to-connect-install-roadmap.md) om Azure AD Connect te configureren en in te stellen. 

- Sluit u aan bij een on-premises machine of een Azure VM voor on-premises AD DS. 

    Als u toegang wilt krijgen tot een bestandsshare met behulp van AD-referenties van een machine of vm, moet uw apparaat zijn verbonden met AD DS. Raadpleeg Join een computer naar een domein voor informatie over het deelnemen aan [een domein.](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) 

- Selecteer of maak een Azure-opslagaccount in [een ondersteund gebied](#regional-availability). 

    Controleer of het opslagaccount met uw bestandsshares nog niet is geconfigureerd voor Azure AD DS-verificatie. Als Azure Files Azure AD DS-verificatie is ingeschakeld voor het opslagaccount, moet deze worden uitgeschakeld voordat u overstapt op het gebruik van on-premises AD DS. Dit houdt in dat bestaande ACL's die zijn geconfigureerd in de Azure AD DS-omgeving opnieuw moeten worden geconfigureerd voor de juiste handhaving van machtigingen.
    
    Zie [Een bestandsshare maken in Azure-bestanden](storage-how-to-create-file-share.md)voor informatie over het maken van een nieuwe bestandsshare.
    
    Voor optimale prestaties raden we u aan het opslagaccount te implementeren in dezelfde regio als de VM van waaruit u van plan bent toegang te krijgen tot het aandeel. 

- Controleer de connectiviteit door Azure-bestandsshares te monteren met uw opslagaccountsleutel. 

    Als u wilt controleren of uw apparaat en bestandsshare correct zijn geconfigureerd, probeert u het bestandsshare te monteren met uw opslagaccountsleutel. Zie [Een Azure-bestandsshare gebruiken met Windows](storage-how-to-use-files-windows.md)voor meer informatie.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files-verificatie met AD DS (preview) is beschikbaar in [alle openbare regio's en Azure Gov-regio's.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="workflow-overview"></a>Overzicht van werkstroom

Voordat u AD DS-verificatie via SMB inschakelt voor Azure-bestandsshares, raden we u aan de [vereistesectie](#prerequisites) te lezen en in te vullen. De vereisten valideren dat uw AD-, Azure AD- en Azure-opslagomgevingen correct zijn geconfigureerd. 

Als u van plan bent netwerkconfiguraties in te schakelen in uw bestandsshare, raden we u aan de [overweging van](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) netwerken te evalueren en de bijbehorende configuratie eerst in te vullen voordat u AD DS-verificatie inschakelt.

Volg vervolgens de onderstaande stappen om Azure Files in te stellen voor AD-verificatie: 

1. Azure Files AD DS-verificatie inschakelen op uw opslagaccount. 

2. Toegangsmachtigingen voor een aandeel toewijzen aan de Azure AD-identiteit (een gebruiker, groep of serviceprincipal) die synchroon loopt met de doel-AD-identiteit. 

3. Configureer ACL's boven SMB voor mappen en bestanden. 
 
4. Stel een Azure-bestandsshare in op een VM die is aangesloten bij uw AD DS. 

5. Werk het wachtwoord van de identiteit van uw opslagaccount bij in AD DS.

In het volgende diagram wordt de end-to-end-werkstroom weergegeven voor het inschakelen van Azure AD-verificatie via SMB voor Azure-bestandsshares. 

![AD-werkstroomdiagram bestanden](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD DS-verificatie via SMB voor Azure-bestandsshares wordt alleen ondersteund op machines of VM's die worden uitgevoerd op nieuwe OS-versies dan Windows 7 of Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. AD-verificatie inschakelen voor uw account 

Als u AD DS-verificatie via SMB voor Azure-bestandsshares wilt inschakelen, moet u eerst uw opslagaccount registreren bij AD DS en vervolgens de vereiste domeineigenschappen instellen voor het opslagaccount. Wanneer de functie is ingeschakeld op het opslagaccount, is deze van toepassing op alle nieuwe en bestaande bestandsshares in het account. Gebruiken `join-AzStorageAccountForAuth` om de functie in te schakelen. In deze sectie vindt u de gedetailleerde beschrijving van de end-to-end workflow in het script. 

> [!IMPORTANT]
> De `Join-AzStorageAccountForAuth` cmdlet zal wijzigingen aanbrengen in uw AD-omgeving. Lees de volgende uitleg om beter te begrijpen wat het doet om ervoor te zorgen dat u de juiste machtigingen hebt om de opdracht uit te voeren en dat de toegepaste wijzigingen overeenkomen met het nalevings- en beveiligingsbeleid. 

De `Join-AzStorageAccountForAuth` cmdlet voert het equivalent uit van een offline domein dat lid wordt namens het aangegeven opslagaccount. Het script gebruikt de cmdlet om een account in uw AD-domein te maken, een [computeraccount](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [service-aanmeldingsaccount.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Als u ervoor kiest om dit handmatig te doen, moet u het account selecteren dat het meest geschikt is voor uw omgeving.

Het AD DS-account dat door de cmdlet is gemaakt, vertegenwoordigt het opslagaccount in het AD-domein. Als het AD DS-account wordt gemaakt onder een organisatie-eenheid (OU) die het verlopen van het wachtwoord afdwingt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd. Als u het accountwachtwoord niet bijwerkt, leidt dit tot verificatiefouten bij de toegang tot Azure-bestandsshares. Zie Het wachtwoord van het [AD DS-account bijwerken](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)voor meer informatie over het bijwerken van het wachtwoord .

U het volgende script gebruiken om de registratie uit te voeren en de functie in te schakelen of, als alternatief, u handmatig de bewerkingen uitvoeren die het script zou uitvoeren. Deze bewerkingen worden beschreven in de sectie naar aanleiding van het script. Je hoeft niet beide te doen.

### <a name="11-script-prerequisites"></a>1.1 Scriptvereisten
- [De AzFilesHybrid-module downloaden en uitpakken](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installeer en voer de module uit in een apparaat dat is gekoppeld aan on-premises AD DS met AD DS-referenties met machtigingen voor het maken van een service-aanmeldingsaccount of een computeraccount in het doel-AD.
-  Voer het script uit met een on-premises AD DS-referentie die is gesynchroniseerd met uw Azure AD. De on-premises AD DS-referentie moet de eigenaar van het opslagaccount of de rbac-functiemachtigingen voor inzender hebben.
- Controleer of uw opslagaccount zich in een [ondersteund gebied bevindt.](#regional-availability)

### <a name="12-domain-join-your-storage-account"></a>1.2 Domein word lid van uw opslagaccount
Vergeet niet om de tijdelijke aanduidingswaarden te vervangen door die van u in de onderstaande parameters voordat u deze uitvoert in PowerShell.
> [!IMPORTANT]
> De cmdlet van de domeinjoin maakt een AD-account om het opslagaccount (bestandsshare) in AD weer te geven. U ervoor kiezen om u te registreren als een computeraccount of service-aanmeldingsaccount, zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) voor meer informatie. Voor computeraccounts is er een standaardleeftijd voor het verlopen van wachtwoorden ingesteld in AD op 30 dagen. Op dezelfde manier kan het serviceaanmeldingsaccount een standaardwachtwoordvervaldatum hebben ingesteld op het AD-domein of de organisatie-eenheid (OU).
> Voor beide accounttypen raden we u ten zeerste aan om te controleren wat de vervaldatum van het wachtwoord is die is geconfigureerd in uw AD-omgeving en het [wachtwoord van uw opslagaccountidentiteit in AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) van het AD-account hieronder bij te werken vóór de maximale wachtwoordleeftijd. Als het wachtwoord van het AD-account niet wordt bijgewerkt, leidt dit tot verificatiefouten bij het openen van Azure-bestandsshares. U overwegen om [een nieuwe AD-organisatie-eenheid (OU) in AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) te maken en het wachtwoordverloopbeleid op [computeraccounts](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) of serviceaanmeldingsaccounts dienovereenkomstig uit te schakelen. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

In de volgende beschrijving worden `Join-AzStorageAccountForAuth` alle acties samengevat die worden uitgevoerd wanneer de cmdlet wordt uitgevoerd. U deze stappen handmatig uitvoeren als u de opdracht liever niet gebruikt:

> [!NOTE]
> Als u het `Join-AzStorageAccountForAuth` bovenstaande script al hebt uitgevoerd, gaat u naar de volgende sectie "1.3 Bevestigen dat de functie is ingeschakeld". U hoeft de onderstaande bewerkingen niet opnieuw uit te voeren.

#### <a name="a-checking-environment"></a>a. Omgeving controleren

Eerst controleert het script uw omgeving. Er wordt specifiek gecontroleerd of [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) is geïnstalleerd en of de shell wordt uitgevoerd met beheerdersbevoegdheden. Vervolgens wordt gecontroleerd of de [Az.Storage 1.11.1-preview-module](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) is geïnstalleerd en wordt deze geïnstalleerd als dit niet het is. Als deze controles passeren, dan zal het controleren van uw AD DS om te zien of er een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of [service aanmeldingsaccount](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) die al is gemaakt met SPN / UPN als "cifs/your-storage-account-name-here.file.core.windows.net". Als het account niet bestaat, wordt er een gemaakt zoals beschreven in sectie b hieronder.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Handmatig een identiteit maken die het opslagaccount in uw AD vertegenwoordigt

Als u dit account handmatig wilt maken, maakt u `New-AzStorageAccountKey -KeyName kerb1`een nieuwe Kerberos-sleutel voor uw opslagaccount met behulp van . Gebruik vervolgens die Kerberos-toets als wachtwoord voor uw account. Deze sleutel wordt alleen gebruikt tijdens het instellen en kan niet worden gebruikt voor controle- of gegevensvlakbewerkingen ten opzichte van het opslagaccount.

Zodra u die sleutel hebt, maakt u een service- of computeraccount onder uw organisatieorganisatie. Gebruik de volgende specificatie: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Wachtwoord: Kerberos-sleutel voor uw opslagaccount.

Als uw organisatie het wachtwoord tot een vervaldatum afdwingt, moet u het wachtwoord bijwerken vóór de maximale wachtwoordleeftijd om verificatiefouten bij het openen van Azure-bestandsshares te voorkomen. Zie [Wachtwoord van uw opslagaccount bijwerken in AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) voor meer informatie.

Houd de SID van het nieuw gemaakte account, je hebt het nodig voor de volgende stap. De identiteit die u hebt gemaakt en die het opslagaccount vertegenwoordigt, hoeft niet te worden gesynchroniseerd met Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. De functie in uw opslagaccount inschakelen

Het script zou dan de functie op uw opslagaccount inschakelen. Als u deze instelling handmatig wilt uitvoeren, geeft u enkele configuratiegegevens op voor de domeineigenschappen in de volgende opdracht en voert u deze uit. De opslagaccount SID die vereist is in de volgende opdracht is de SID van de identiteit die u in uw AD DS in [de vorige sectie](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)hebt gemaakt.

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Bevestigen dat de functie is ingeschakeld

U controleren of de functie is ingeschakeld in uw opslagaccount, u het volgende script gebruiken:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Je hebt de functie nu in je opslagaccount ingeschakeld. Nu de functie is ingeschakeld, zijn er meer stappen die u moet uitvoeren om de functie te gebruiken.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt ad DS-verificatie nu met smb-verificatie ingeschakeld en een aangepaste rol toegewezen die toegang biedt tot een Azure-bestandsshare met een AD DS-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestandsshare, volgt u de instructies in de [toegangsmachtigingen toewijzen](#2-assign-access-permissions-to-an-identity) om een identiteit te gebruiken en [NTFS-machtigingen configureren voor SMB-secties.](#3-configure-ntfs-permissions-over-smb)

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Het wachtwoord van uw opslagaccountidentiteit bijwerken in AD DS

Als u de AD DS-identiteit/-account hebt geregistreerd die uw opslagaccount vertegenwoordigt onder een organisatieorganisatie die de vervaldatum van het wachtwoord afdwingt, moet u het wachtwoord roteren voordat het wachtwoordwordt leeftijd. Als u het wachtwoord van het AD DS-account niet bijwerkt, leidt dit tot verificatiefouten om toegang te krijgen tot Azure-bestandsshares.  

Als u wachtwoordrotatie wilt `Update-AzStorageAccountADObjectPassword` activeren, u de opdracht uitvoeren vanuit de AzFilesHybrid-module. De cmdlet voert acties uit die vergelijkbaar zijn met de rotatie van de opslagaccountsleutel. Het krijgt de tweede Kerberos-sleutel van het opslagaccount en gebruikt het om het wachtwoord van het geregistreerde account in AD DS bij te werken. Vervolgens regenereert het de doelKerberos-toets van het opslagaccount en wordt het wachtwoord van het geregistreerde account in AD DS bijgewerkt. U moet deze cmdlet uitvoeren in een on-premises AD DS-domein samengevoegde omgeving.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van AD via SMB:

- [Overzicht van azure files-verificatieondersteuning voor SMB-toegang](storage-files-active-directory-overview.md)
- [Veelgestelde vragen](storage-files-faq.md)
