---
title: Active Directory authenticatie via SMB voor Azure Files inschakelen
description: Meer informatie over het inschakelen van verificatie op basis van identiteit via SMB voor Azure-bestands shares via Active Directory. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met behulp van AD-referenties.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: rogarana
ms.openlocfilehash: 1f904435622c8128810bb0e381308c8a308dd360
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128937"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Active Directory authenticatie via SMB voor Azure-bestands shares inschakelen

[Azure Files](storage-files-introduction.md) ondersteunt verificatie op basis van identiteiten via SMB (Server Message Block) via twee typen domein Services: Azure Active Directory Domain Services (Azure AD DS) (ga) en Active Directory (AD) (preview). Dit artikel richt zich op de nieuw geïntroduceerde (preview)-ondersteuning van het gebruik van Active Directory-domein-service voor verificatie voor Azure-bestands shares. Raadpleeg [ons artikel over het onderwerp](storage-files-identity-auth-active-directory-domain-service-enable.md)als u de Azure AD DS (ga)-verificatie voor Azure-bestands shares wilt inschakelen.

> [!NOTE]
> Azure-bestands shares bieden alleen ondersteuning voor verificatie op basis van één domein service, hetzij Azure Active Directory Domain Service (Azure AD DS) of Active Directory (AD). 
>
> AD-identiteiten die worden gebruikt voor Azure file share-verificatie moeten worden gesynchroniseerd met Azure AD. Synchronisatie van wacht woord-hash is optioneel. 
> 
> AD-verificatie biedt geen ondersteuning voor verificatie tegen computer accounts die zijn gemaakt in AD. 
> 
> AD-verificatie kan alleen worden ondersteund voor één AD-forest waar het opslag account is geregistreerd. U kunt standaard alleen toegang krijgen tot Azure-bestands shares met de AD-referenties van één AD-forest. Als u toegang nodig hebt tot uw Azure-bestands share vanuit een ander forest, moet u ervoor zorgen dat u de juiste forestvertrouwensrelatie hebt geconfigureerd. Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) voor meer informatie.  
> 
> AD-verificatie voor SMB-toegang en ACL-persistentie wordt ondersteund voor Azure-bestands shares die worden beheerd door Azure File Sync.
>
> Azure Files ondersteunt Kerberos-verificatie met AD met RC4-HMAC-versleuteling. AES Kerberos-versleuteling wordt nog niet ondersteund.

Wanneer u AD voor Azure-bestands shares via SMB inschakelt, kunnen uw aan AD-domein gekoppelde computers Azure-bestands shares koppelen met behulp van uw bestaande AD-referenties. Deze mogelijkheid kan worden ingeschakeld met een AD-omgeving die wordt gehost op on-premises computers of die wordt gehost in Azure.

AD-identiteiten die worden gebruikt voor toegang tot Azure-bestands shares moeten worden gesynchroniseerd met Azure AD om bestands machtigingen op share niveau af te dwingen via het standaard [RBAC-model (op rollen gebaseerd toegangs beheer)](../../role-based-access-control/overview.md) . [Windows-dacl's](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) voor bestanden/mappen die zijn overgedragen van bestaande bestands servers blijven behouden en worden afgedwongen. Deze functie biedt naadloze integratie met uw bedrijfs-AD-domein infrastructuur. Wanneer u on-premises bestands servers vervangt door Azure-bestands shares, hebben bestaande gebruikers toegang tot Azure-bestands shares van hun huidige clients met eenmalige aanmelding, zonder enige wijziging van de referenties die in gebruik zijn.  
 
## <a name="prerequisites"></a>Vereisten 

Voordat u AD-verificatie voor Azure-bestands shares inschakelt, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid: 

- Selecteer of maak uw AD-omgeving en synchroniseer deze met Azure AD. 

    U kunt de functie inschakelen voor een nieuwe of bestaande AD-omgeving. Identiteiten die worden gebruikt voor toegang moeten worden gesynchroniseerd met Azure AD. De Azure AD-Tenant en de bestands share die u wilt openen, moeten zijn gekoppeld aan hetzelfde abonnement. 

    Raadpleeg [Active Directory Domain Services Overview voor informatie](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)over het instellen van een AD-domein omgeving. Als u uw AD niet hebt gesynchroniseerd met uw Azure AD, volgt u de richt lijnen in [Wat is Hybrid Identity met Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) om uw voorkeurs verificatie methode en Azure AD Connect-installatie optie te bepalen. 

- Domein: lid worden van een on-premises machine of een Azure VM aan AD (ook wel AD DS genoemd). 

    Om toegang te krijgen tot een bestands share via AD-referenties van een computer of virtuele machine, moet uw apparaat lid zijn van een domein. Zie [een computer toevoegen aan een domein](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)voor meer informatie over het koppelen van domein aan AD. 

- Selecteer of maak een Azure-opslag account in [een ondersteunde regio](#regional-availability). 

    Zorg ervoor dat het opslag account dat uw bestands shares bevat, nog niet is geconfigureerd voor Azure AD DS-verificatie. Als Azure Files Azure AD DS-verificatie is ingeschakeld voor het opslag account, moet dit worden uitgeschakeld voordat u AD gaat gebruiken. Dit betekent dat bestaande Acl's die zijn geconfigureerd in azure AD DS-omgeving opnieuw moeten worden geconfigureerd voor de juiste machtigings afdwinging.
    
    Zie [een bestands share maken in azure files](storage-how-to-create-file-share.md)voor meer informatie over het maken van een nieuwe bestands share.
    
    Voor optimale prestaties raden we u aan het opslag account in dezelfde regio te implementeren als de virtuele machine van waaruit u toegang wilt krijgen tot de share. 

- Controleer de connectiviteit door Azure-bestands shares te koppelen met behulp van de sleutel van het opslag account. 

    Als u wilt controleren of het apparaat en de bestands share correct zijn geconfigureerd, kunt u de bestands share koppelen met de sleutel van het opslag account. Zie [een Azure-bestands share gebruiken met Windows](storage-how-to-use-files-windows.md)voor meer informatie.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files AD-verificatie (preview) is beschikbaar in de [meeste open bare regio's](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Files AD-verificatie is niet beschikbaar in:
- VS - west
- VS - west 2
- VS - oost
- VS - oost 2
- Europa -west
- Europa - noord

## <a name="workflow-overview"></a>Overzicht werk stroom

Voordat u AD-verificatie via SMB voor Azure-bestands shares inschakelt, wordt u aangeraden de [vereisten](#prerequisites) te en ervoor te zorgen dat u alle stappen hebt voltooid. De vereisten controleren of uw AD-, Azure AD-en Azure Storage-omgevingen op de juiste wijze zijn geconfigureerd. 

Verleen vervolgens toegang tot Azure Files resources met AD-referenties: 

- Schakel Azure Files AD-verificatie in voor uw opslag account.  

- Wijs toegangs machtigingen voor een share toe aan de Azure AD-identiteit (een gebruiker, groep of Service-Principal) die is gesynchroniseerd met de doel-AD-identiteit. 

- Configureer Acl's via SMB voor mappen en bestanden. 

- Een Azure-bestands share koppelen vanuit een AD-domein die is toegevoegd aan de virtuele machine. 

In het volgende diagram ziet u de end-to-end werk stroom voor het inschakelen van Azure AD-verificatie via SMB voor Azure-bestands shares. 

![Bestanden AD-werk stroom diagram](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD-verificatie via SMB voor Azure-bestands shares wordt alleen ondersteund op machines of Vm's die worden uitgevoerd op nieuwere versies van het besturings systeem dan Windows 7 of Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>AD-verificatie voor uw account inschakelen 

Als u AD-verificatie via SMB voor Azure-bestands shares wilt inschakelen, moet u eerst uw opslag account bij AD registreren en vervolgens de vereiste domein eigenschappen instellen voor het opslag account. Wanneer de functie is ingeschakeld op het opslag account, is dit van toepassing op alle nieuwe en bestaande bestands shares in het account. Gebruik `join-AzStorageAccountForAuth` om de functie in te scha kelen. U vindt de gedetailleerde beschrijving van de end-to-end werk stroom in de volgende sectie. 

> [!IMPORTANT]
> Met de cmdlet `join-AzStorageAccountForAuth` worden wijzigingen aangebracht in uw AD-omgeving. Lees de volgende uitleg om beter te begrijpen wat er gebeurt om ervoor te zorgen dat u over de juiste machtigingen beschikt om de opdracht uit te voeren en dat de toegepaste wijzigingen worden uitgelijnd met het nalevings-en beveiligings beleid. 

Met de cmdlet `join-AzStorageAccountForAuth` wordt het equivalent van een offline domein deelname uitgevoerd namens het opgegeven opslag account. Er wordt een account in uw AD-domein gemaakt, ofwel een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) of een [service-aanmeldings account](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Het gemaakte AD-account vertegenwoordigt het opslag account in het AD-domein. Als het AD-account wordt gemaakt onder een AD-organisatie-eenheid (OE) die het verlopen van wacht woorden afdwingt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur. Het bijwerken van het wacht woord van het AD-account leidt ertoe dat er verificatie fouten optreden bij het openen van Azure-bestands shares. Zie het [wacht woord voor AD-accounts bijwerken](#update-ad-account-password)voor meer informatie over het bijwerken van het wacht woord.

U kunt het volgende script gebruiken om de registratie uit te voeren en de functie in te scha kelen, of u kunt de bewerkingen die door het script worden uitgevoerd, ook hand matig uitvoeren. Deze bewerkingen worden beschreven in de sectie die volgt op het script. U hoeft niet beide te doen.

### <a name="1-check-prerequisites"></a>1. vereisten controleren
- [De AzFilesHybrid-module downloaden en uitpakken](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installeer en voer de module uit in een domein dat is gekoppeld aan AD met AD-referenties die machtigingen hebben voor het maken van een account voor service aanmelding of een computer account in de doel-AD.
-  Voer het script uit met een AD-referentie die is gesynchroniseerd met uw Azure AD. De AD-referentie moet de eigenaar van het opslag account of de rechten van de rol Inzender RBAC hebben.
- Zorg ervoor dat uw opslag account zich in een [ondersteunde regio](#regional-availability)bevindt.

### <a name="2-execute-ad-enablement-script"></a>2. AD-activerings script uitvoeren
Vervang de tijdelijke aanduidingen door uw eigen waarden in de onderstaande para meters voordat u deze in Power shell uitvoert.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationalUnitName "<ou-name-here>"
```

De volgende beschrijving bevat een overzicht van alle acties die worden uitgevoerd wanneer de `join-AzStorageAccountForAuth` cmdlet wordt uitgevoerd. U kunt deze stappen hand matig uitvoeren als u de opdracht liever niet gebruikt:

> [!NOTE]
> Als u het bovenstaande script voor deelname aan AzStorageAccountForAuth al hebt uitgevoerd, gaat u naar de volgende sectie 3. Controleer of de functie is ingeschakeld. U hoeft de bewerkingen niet opnieuw uit te voeren.

#### <a name="a-checking-environment"></a>a. Omgeving controleren

Eerst wordt uw omgeving gecontroleerd. Met name wordt gecontroleerd of de [Active Directory Power shell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) is geïnstalleerd en of de shell wordt uitgevoerd met beheerders bevoegdheden. Vervolgens wordt gecontroleerd of de [module AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) is geïnstalleerd, en wordt deze geïnstalleerd als dat niet het geval is. Als deze controles worden uitgevoerd, wordt uw AD gecontroleerd om te zien of er een [computer account](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [service aanmeldings account](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) is dat al met SPN/UPN is gemaakt als CIFS/uw-Storage-account-name. file. core. Windows. net. Als het account niet bestaat, wordt er een gemaakt zoals beschreven in onderstaande sectie b.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Een identiteit maken die het opslag account in uw AD hand matig weergeeft

Als u dit account hand matig wilt maken, maakt u een nieuwe Kerberos-sleutel voor uw opslag account met behulp van `New-AzStorageAccountKey -KeyName kerb1`. Gebruik vervolgens die Kerberos-sleutel als wacht woord voor uw account. Deze sleutel wordt alleen gebruikt tijdens het instellen en kan niet worden gebruikt voor beheer-of gegevenslaag bewerkingen voor het opslag account.

Wanneer u deze sleutel hebt, moet u een service-of computer account maken onder uw organisatie-eenheid. Gebruik de volgende specificatie: SPN: ' CIFS/uw-Storage-account-name-hier. file. core. Windows. net ' wacht woord: Kerberos-sleutel voor uw opslag account.

Als uw organisatie-eenheid het wacht woord verloopt, moet u het wacht woord bijwerken vóór de maximale wachtwoord duur om verificatie fouten te voor komen bij het openen van Azure-bestands shares. Zie het [wacht woord voor de ad-account bijwerken](#update-ad-account-password) voor meer informatie.

Behoud de SID van het zojuist gemaakte account, u hebt deze nodig voor de volgende stap.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. De functie op uw opslag account inschakelen

Het script schakelt vervolgens de functie in voor uw opslag account. Als u deze installatie hand matig wilt uitvoeren, geeft u bepaalde configuratie details op voor de domein eigenschappen in de volgende opdracht en voert u deze uit. De SID van het opslag account die is vereist in de volgende opdracht is de SID van de identiteit die u in AD hebt gemaakt (sectie b hierboven).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServicesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Controleer of de functie is ingeschakeld

U kunt controleren of de functie is ingeschakeld voor uw opslag account. u kunt het volgende script gebruiken:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

De functie is nu ingeschakeld voor uw opslag account. Hoewel de functie is ingeschakeld, moet u nog steeds aanvullende acties volt ooien om de functie goed te kunnen gebruiken.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt nu AD-verificatie via SMB ingeschakeld en er is een aangepaste rol toegewezen waarmee u toegang krijgt tot een Azure-bestands share met een AD-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestands share, volgt u de instructies in de [toegangs machtigingen toewijzen](#assign-access-permissions-to-an-identity) voor het gebruik van een identiteit en [NTFS-machtigingen configureren via SMB-](#configure-ntfs-permissions-over-smb) secties.

## <a name="update-ad-account-password"></a>Wacht woord voor AD-account bijwerken

Als u de AD-identiteit/het account die uw opslag account vertegenwoordigt, hebt geregistreerd bij een organisatie-eenheid die de verval tijd van het wacht woord afdwingt, moet u het wacht woord voor de maximale wachtwoord duur draaien. Als het niet lukt om het wacht woord van het AD-account bij te werken, worden verificatie fouten voor toegang tot Azure-bestands shares veroorzaakt.  

Als u het draaien van wacht woorden wilt activeren, kunt u de opdracht `Update-AzStorageAccountADObjectPassword` uitvoeren vanuit de module AzFilesHybrid. De cmdlet voert acties uit die vergelijkbaar zijn met de rotatie van de opslag account sleutel. Hiermee wordt de tweede Kerberos-sleutel van het opslag account opgehaald en gebruikt om het wacht woord van het geregistreerde account in AD bij te werken. Vervolgens wordt de Kerberos-doel sleutel van het opslag account opnieuw gegenereerd en wordt het wacht woord van het geregistreerde account in AD bijgewerkt. U moet deze cmdlet uitvoeren in een omgeving die is toegevoegd aan een AD-domein.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van AD via SMB:

- [Overzicht van Azure Files verificatie op basis van identiteiten voor SMB-toegang](storage-files-active-directory-overview.md)
- [Veelgestelde vragen](storage-files-faq.md)
