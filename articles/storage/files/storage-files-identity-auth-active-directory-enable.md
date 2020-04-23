---
title: Active Directory authenticatie via SMB voor Azure Files inschakelen
description: Meer informatie over het inschakelen van verificatie op basis van identiteit via SMB voor Azure-bestands shares via Active Directory. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met behulp van AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: b2dd501344e1ea799db58ea749395aaed05d05f8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106544"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>On-premises Active Directory Domain Services authenticatie inschakelen via SMB voor Azure-bestands shares

[Azure files](storage-files-introduction.md) ondersteunt verificatie op basis van identiteiten via SMB (Server Message Block) via twee typen Domain Services: Azure Active Directory Domain Services (Azure AD DS) en on-premises Active Directory Domain Services (AD DS) (preview). Dit artikel richt zich op de nieuw geïntroduceerde (preview)-ondersteuning van het gebruik van Active Directory-domein-service voor verificatie voor Azure-bestands shares. Raadpleeg [ons artikel over het onderwerp](storage-files-identity-auth-active-directory-domain-service-enable.md)als u de Azure AD DS (ga)-verificatie voor Azure-bestands shares wilt inschakelen.

> [!NOTE]
> Azure-bestands shares bieden alleen ondersteuning voor verificatie op basis van één domein service, hetzij Azure Active Directory Domain Service (Azure AD DS) of on-premises Active Directory Domain Services (AD DS). 
>
> AD DS-identiteiten die worden gebruikt voor Azure file share-verificatie moeten worden gesynchroniseerd met Azure AD. Synchronisatie van wacht woord-hash is optioneel. 
> 
> On-premises AD DS verificatie biedt geen ondersteuning voor verificatie tegen computer accounts die zijn gemaakt in AD DS. 
> 
> On-premises AD DS authenticatie kan alleen worden ondersteund voor één AD-forest waar het opslag account is geregistreerd. U kunt standaard alleen toegang krijgen tot Azure-bestands shares met de AD DS referenties van één forest. Als u toegang nodig hebt tot uw Azure-bestands share vanuit een ander forest, moet u ervoor zorgen dat u de juiste forestvertrouwensrelatie hebt geconfigureerd. Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) voor meer informatie.  
> 
> AD DS authenticatie voor SMB-toegang en ACL-persistentie wordt ondersteund voor Azure-bestands shares die worden beheerd door Azure File Sync.
>
> Azure Files ondersteunt Kerberos-verificatie met AD met RC4-HMAC-versleuteling. AES Kerberos-versleuteling wordt nog niet ondersteund.

Wanneer u AD DS voor Azure-bestands shares via SMB inschakelt, kunnen uw AD DS-gekoppelde computers Azure-bestands shares koppelen met uw bestaande AD-referenties. Deze mogelijkheid kan worden ingeschakeld met een AD DS-omgeving die wordt gehost op on-premises computers of die wordt gehost in Azure.

Identiteiten die worden gebruikt voor toegang tot Azure-bestands shares, moeten worden gesynchroniseerd met Azure AD om bestands machtigingen op share niveau af te dwingen via het [RBAC-model (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) . [Windows-dacl's](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) voor bestanden/mappen die zijn overgedragen van bestaande bestands servers blijven behouden en worden afgedwongen. Deze functie biedt naadloze integratie met uw bedrijfs AD DS omgeving. Wanneer u on-premises bestands servers vervangt door Azure-bestands shares, hebben bestaande gebruikers toegang tot Azure-bestands shares van hun huidige clients met eenmalige aanmelding, zonder enige wijziging van de referenties die in gebruik zijn.  

> [!NOTE]
> Om u te helpen bij het instellen van Azure Files AD-verificatie voor enkele veelvoorkomende gebruiks gevallen, hebben we [twee Video's](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) gepubliceerd met stapsgewijze instructies:
> - Vervangen van on-premises bestands servers met Azure Files (inclusief de installatie van een persoonlijke koppeling voor bestanden en AD-verificatie)
> - Azure Files gebruiken als de profiel container voor het virtuele Windows-bureau blad (inclusief Setup in AD-verificatie en FsLogix-configuratie)

## <a name="prerequisites"></a>Vereisten 

Voordat u AD DS verificatie voor Azure-bestands shares inschakelt, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid: 

- Selecteer of maak uw AD DS omgeving en [Synchroniseer deze met Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    U kunt de functie inschakelen op een nieuwe of bestaande on-premises AD DS omgeving. Identiteiten die worden gebruikt voor toegang moeten worden gesynchroniseerd met Azure AD. De Azure AD-Tenant en de bestands share die u wilt openen, moeten zijn gekoppeld aan hetzelfde abonnement. 

    Raadpleeg [Active Directory Domain Services Overview voor informatie](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)over het instellen van een AD-domein omgeving. Als u uw AD niet hebt gesynchroniseerd met uw Azure AD, volgt u de richt lijnen in [Azure AD Connect en Azure AD Connect Health installatie schema](../../active-directory/hybrid/how-to-connect-install-roadmap.md) om Azure AD Connect te configureren en in te stellen. 

- Domein: lid worden van een on-premises machine of een Azure-VM naar een on-premises AD DS. 

    Als u toegang wilt krijgen tot een bestands share via AD-referenties van een computer of virtuele machine, moet uw apparaat lid zijn van een domein met AD DS. Zie lid worden van [een computer aan een domein](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)voor meer informatie over het koppelen van een domein. 

- Selecteer of maak een Azure-opslag account in [een ondersteunde regio](#regional-availability). 

    Zorg ervoor dat het opslag account dat uw bestands shares bevat, nog niet is geconfigureerd voor Azure AD DS-verificatie. Als Azure Files Azure AD DS-verificatie is ingeschakeld voor het opslag account, moet het worden uitgeschakeld voordat u de lokale AD DS kunt wijzigen. Dit betekent dat bestaande Acl's die zijn geconfigureerd in azure AD DS-omgeving opnieuw moeten worden geconfigureerd voor de juiste machtigings afdwinging.
    
    Zie [een bestands share maken in azure files](storage-how-to-create-file-share.md)voor meer informatie over het maken van een nieuwe bestands share.
    
    Voor optimale prestaties raden we u aan het opslag account in dezelfde regio te implementeren als de virtuele machine van waaruit u toegang wilt krijgen tot de share. 

- Controleer de connectiviteit door Azure-bestands shares te koppelen met behulp van de sleutel van het opslag account. 

    Als u wilt controleren of het apparaat en de bestands share correct zijn geconfigureerd, kunt u [de bestands share koppelen](storage-how-to-use-files-windows.md) met de sleutel van het opslag account. Als u problemen ondervindt bij het maken van verbinding met Azure Files, raadpleegt u [het hulp programma voor probleem oplossing dat is gepubliceerd voor Azure files-koppelings fouten in Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). We bieden ook [richt lijnen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) voor het omzeilen van scenario's wanneer poort 445 wordt geblokkeerd. 

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files verificatie met AD DS (preview) is beschikbaar in [alle open bare regio's en Azure gov-regio's](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Overzicht werk stroom

Voordat u AD DS authenticatie via SMB voor Azure-bestands shares inschakelt, wordt u aangeraden de sectie [vereisten](#prerequisites) te lezen en te volt ooien. De vereisten controleren of uw AD-, Azure AD-en Azure Storage-omgevingen op de juiste wijze zijn geconfigureerd. 

Als u van plan bent om netwerk configuraties in te scha kelen op uw bestands share, raden we u aan om de [netwerk overweging](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) te evalueren en de gerelateerde configuratie eerst te volt ooien voordat u AD DS verificatie inschakelt.

Voer vervolgens de volgende stappen uit om Azure Files voor AD-verificatie in te stellen: 

1. Schakel Azure Files AD DS-verificatie in voor uw opslag account. 

2. Wijs toegangs machtigingen voor een share toe aan de Azure AD-identiteit (een gebruiker, groep of Service-Principal) die is gesynchroniseerd met de doel-AD-identiteit. 

3. Configureer Acl's via SMB voor mappen en bestanden. 
 
4. Koppel een Azure-bestands share aan een VM die is toegevoegd aan uw AD DS. 

5. Werk het wacht woord van de identiteit van uw opslag account bij in AD DS.

In het volgende diagram ziet u de end-to-end werk stroom voor het inschakelen van Azure AD-verificatie via SMB voor Azure-bestands shares. 

![Bestanden AD-werk stroom diagram](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD DS authenticatie via SMB voor Azure-bestands shares wordt alleen ondersteund op computers of Vm's die worden uitgevoerd op versies van het besturings systeem die nieuwer zijn dan Windows 7 of Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. AD-verificatie voor uw account inschakelen 

Als u AD DS verificatie via SMB voor Azure-bestands shares wilt inschakelen, moet u eerst uw opslag account bij AD DS registreren en vervolgens de vereiste domein eigenschappen instellen voor het opslag account. Wanneer de functie is ingeschakeld op het opslag account, is dit van toepassing op alle nieuwe en bestaande bestands shares in het account. Gebruiken `join-AzStorageAccountForAuth` om de functie in te scha kelen. U vindt de gedetailleerde beschrijving van de end-to-end werk stroom in het script in deze sectie. 

> [!IMPORTANT]
> Met `Join-AzStorageAccountForAuth` de cmdlet worden wijzigingen aangebracht in uw AD-omgeving. Lees de volgende uitleg om beter te begrijpen wat er gebeurt om ervoor te zorgen dat u over de juiste machtigingen beschikt om de opdracht uit te voeren en dat de toegepaste wijzigingen worden uitgelijnd met het nalevings-en beveiligings beleid. 

Met `Join-AzStorageAccountForAuth` de cmdlet wordt het equivalent van een offline domein toegevoegd namens het opgegeven opslag account. Het script gebruikt de cmdlet voor het maken van een account in uw AD-domein, hetzij een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [service aanmeldings account](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Als u ervoor kiest om dit hand matig te doen, moet u het account selecteren dat het meest geschikt is voor uw omgeving.

Het AD DS-account dat is gemaakt door de cmdlet vertegenwoordigt het opslag account in het AD-domein. Als het AD DS-account wordt gemaakt onder een organisatie-eenheid (OE) die het verlopen van wacht woorden afdwingt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur. Als het account wachtwoord niet kan worden bijgewerkt, zullen er verificatie fouten optreden bij het openen van Azure-bestands shares. Zie [AD DS account wachtwoord bijwerken](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)voor meer informatie over het bijwerken van het wacht woord.

U kunt het volgende script gebruiken om de registratie uit te voeren en de functie in te scha kelen, of u kunt de bewerkingen die door het script worden uitgevoerd, ook hand matig uitvoeren. Deze bewerkingen worden beschreven in de sectie die volgt op het script. U hoeft niet beide te doen.

### <a name="11-script-prerequisites"></a>1,1-script vereisten
- [De AzFilesHybrid-module downloaden en uitpakken](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installeer en voer de module uit in een domein dat is gekoppeld aan een on-premises AD DS met AD DS referenties die machtigingen hebben voor het maken van een account voor service aanmelding of een computer account in de doel-AD.
-  Voer het script uit met behulp van een on-premises AD DS referentie die is gesynchroniseerd met uw Azure AD. De on-premises AD DS referentie moet de eigenaar van het opslag account of de rechten van de rol Inzender RBAC hebben.
- Zorg ervoor dat uw opslag account zich in een [ondersteunde regio](#regional-availability)bevindt.

### <a name="12-domain-join-your-storage-account"></a>1,2-domein toevoegen aan uw opslag account
Vervang de tijdelijke aanduidingen door uw eigen waarden in de onderstaande para meters voordat u deze in Power shell uitvoert.
> [!IMPORTANT]
> Met de cmdlet domein toevoegen wordt een AD-account gemaakt dat het opslag account (bestands share) in AD vertegenwoordigt. Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) voor meer informatie over het registreren van een computer account of een service-aanmeldings account. Voor computer accounts is er een standaard leeftijd voor wachtwoord verloop ingesteld in AD om 30 dagen. Op dezelfde manier kan voor het service-aanmeldings account een standaard leeftijd voor het wacht woord voor het verlopen van het AD-domein of de organisatie-eenheid (OE) zijn ingesteld.
> Voor beide typen accounts raden wij u ten zeerste aan om te controleren wat de leeftijd van het wacht woord is dat in uw AD-omgeving is geconfigureerd en om [het wacht woord van de identiteit van uw opslag account in AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) van het onderstaande ad-account bij te werken vóór de maximale wachtwoord duur. Het bijwerken van het wacht woord van het AD-account leidt ertoe dat er verificatie fouten optreden bij het openen van Azure-bestands shares. U kunt overwegen om [een nieuwe AD-organisatie-eenheid (OE) in AD te maken en het](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) beleid voor wachtwoord verloop voor [computer accounts](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) of service-aanmeldings accounts dienovereenkomstig uit te scha kelen. 

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

De volgende beschrijving bevat een overzicht van alle acties die `Join-AzStorageAccountForAuth` worden uitgevoerd wanneer de cmdlet wordt uitgevoerd. U kunt deze stappen hand matig uitvoeren als u de opdracht liever niet gebruikt:

> [!NOTE]
> Als u het `Join-AzStorageAccountForAuth` bovenstaande script al hebt uitgevoerd, gaat u naar de volgende sectie "1,3 bevestigen dat de functie is ingeschakeld". U hoeft de bewerkingen niet opnieuw uit te voeren.

#### <a name="a-checking-environment"></a>a. Omgeving controleren

Eerst controleert het script uw omgeving. Met name wordt gecontroleerd of [Active Directory Power shell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) is geïnstalleerd en of de shell wordt uitgevoerd met beheerders bevoegdheden. Vervolgens wordt gecontroleerd of de [module AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) is geïnstalleerd, en wordt deze geïnstalleerd als dat niet het geval is. Als deze controles worden uitgevoerd, wordt uw AD DS gecontroleerd om te zien of er een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [account voor aanmelding](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) bij de service is gemaakt die al met SPN/UPN als ' CIFS/uw-Storage-account-name-hier ' is aangemaakt. file. core. Windows. net '. Als het account niet bestaat, wordt er een gemaakt zoals beschreven in onderstaande sectie b.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Een identiteit maken die het opslag account in uw AD hand matig weergeeft

Als u dit account hand matig wilt maken, maakt u een nieuwe Kerberos-sleutel `New-AzStorageAccountKey -KeyName kerb1`voor uw opslag account met behulp van. Gebruik vervolgens die Kerberos-sleutel als wacht woord voor uw account. Deze sleutel wordt alleen gebruikt tijdens het instellen en kan niet worden gebruikt voor beheer-of gegevenslaag bewerkingen voor het opslag account.

Wanneer u deze sleutel hebt, moet u een service-of computer account maken onder uw organisatie-eenheid. Gebruik de volgende specificatie: SPN: ' CIFS/uw-Storage-account-name-hier. file. core. Windows. net ' wacht woord: Kerberos-sleutel voor uw opslag account.

Als uw organisatie-eenheid het wacht woord verloopt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur om verificatie fouten te voor komen bij het openen van Azure-bestands shares. Zie [het wacht woord voor de identiteit van uw opslag account bijwerken in AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) voor meer informatie.

Behoud de SID van het zojuist gemaakte account, u hebt deze nodig voor de volgende stap. De identiteit die u hebt gemaakt die het opslag account vertegenwoordigt, hoeft niet te worden gesynchroniseerd met Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. De functie op uw opslag account inschakelen

Het script schakelt vervolgens de functie in voor uw opslag account. Als u deze installatie hand matig wilt uitvoeren, geeft u bepaalde configuratie details op voor de domein eigenschappen in de volgende opdracht en voert u deze uit. De SID van het opslag account die is vereist in de volgende opdracht is de SID van de identiteit die u hebt gemaakt in uw AD DS in [de vorige sectie](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 Bevestig dat de functie is ingeschakeld

U kunt controleren of de functie is ingeschakeld voor uw opslag account. u kunt het volgende script gebruiken:

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

De functie is nu ingeschakeld voor uw opslag account. Nu de functie is ingeschakeld, zijn er meer stappen die u moet uitvoeren om de functie te gebruiken.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt nu AD DS verificatie via SMB ingeschakeld en er is een aangepaste rol toegewezen die toegang biedt tot een Azure-bestands share met een AD DS-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestands share, volgt u de instructies in de [toegangs machtigingen toewijzen](#2-assign-access-permissions-to-an-identity) voor het gebruik van een identiteit en [NTFS-machtigingen configureren via SMB-](#3-configure-ntfs-permissions-over-smb) secties.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. werk het wacht woord van de identiteit van uw opslag account bij in AD DS

Als u de AD DS identiteit/account die uw opslag account vertegenwoordigt, hebt geregistreerd bij een organisatie-eenheid die de verval tijd van het wacht woord afdwingt, moet u het wacht woord voor de maximale wachtwoord duur draaien. Als u het wacht woord van het AD DS-account niet bijwerkt, zullen er verificatie fouten optreden voor toegang tot Azure-bestands shares.  

Als u het draaien van wacht woorden wilt activeren `Update-AzStorageAccountADObjectPassword` , kunt u de opdracht uitvoeren vanuit de module AzFilesHybrid. De cmdlet voert acties uit die vergelijkbaar zijn met de rotatie van de opslag account sleutel. Hiermee wordt de tweede Kerberos-sleutel van het opslag account opgehaald en gebruikt om het wacht woord van het geregistreerde account in AD DS bij te werken. Vervolgens wordt de Kerberos-doel sleutel van het opslag account opnieuw gegenereerd en wordt het wacht woord van het geregistreerde account in AD DS bijgewerkt. U moet deze cmdlet uitvoeren in een on-premises AD DS aan een domein gekoppelde omgeving.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van AD via SMB:

- [Overzicht van Azure Files verificatie op basis van identiteiten voor SMB-toegang](storage-files-active-directory-overview.md)
- [Veelgestelde vragen](storage-files-faq.md)
