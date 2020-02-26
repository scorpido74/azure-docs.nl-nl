---
title: Overzicht-Azure Files autorisatie op basis van een identiteit
description: Azure Files ondersteunt op identiteit gebaseerde verificatie via SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) en Active Directory. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met Azure AD-referenties.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598813"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Overzicht van Azure Files verificatie op basis van identiteiten voor SMB-toegang
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [Active Directory authenticatie inschakelen via SMB voor Azure-bestands shares](storage-files-identity-auth-active-directory-enable.md)voor meer informatie over het inschakelen van AD-verificatie voor Azure-bestands shares.

Zie [Azure Active Directory Domain Services-verificatie inschakelen voor Azure files](storage-files-identity-auth-active-directory-domain-service-enable.md)voor meer informatie over het inschakelen van Azure AD DS-verificatie voor Azure-bestands shares.

## <a name="glossary"></a>Woordenlijst 
Het is handig om enkele belang rijke termen te begrijpen met betrekking tot Azure AD domain service-verificatie via SMB voor Azure-bestands shares:

-   **Kerberos-verificatie**

    Kerberos is een authenticatie protocol dat wordt gebruikt om de identiteit van een gebruiker of host te controleren. Zie [overzicht van Kerberos-verificatie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)voor meer informatie over Kerberos.

-  **SMB-protocol (Server Message Block)**

    SMB is een standaard protocol voor het delen van netwerk bestanden. SMB is ook bekend als common Internet File System of CIFS. Zie het [overzicht van micro soft SMB-protocol en CIFS-protocol](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)voor meer informatie over SMB.

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Azure AD is een combi natie van basis Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging in één oplossing. Met Azure AD kunt u virtuele Windows-machines (Vm's) die lid zijn van een domein om toegang te krijgen tot Azure-bestands shares met uw Azure AD-referenties. Zie [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie.

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie. Deze services zijn volledig compatibel met Windows Server Active Directory. Zie [Azure Active Directory (AD) Domain Services (Engelstalig)](../../active-directory-domain-services/overview.md)voor meer informatie.

- **Active Directory Domain Services (AD DS, ook wel aangeduid als AD)**

    Active Directory (AD) (preview) biedt de methoden voor het opslaan van Directory gegevens en deze beschikbaar maken voor netwerk gebruikers en beheerders. Beveiliging is geïntegreerd met Active Directory via aanmeldings verificatie en toegangs beheer voor objecten in de Directory. Met één netwerk aanmelding kunnen beheerders Directory gegevens en organisatie beheren in hun netwerk, en geautoriseerde netwerk gebruikers kunnen overal toegang krijgen tot bronnen in het netwerk. AD wordt doorgaans door ondernemingen in on-premises goedgekeurd en gebruiken AD-referenties als de identiteit voor toegangs beheer. Zie [Active Directory Domain Services Overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie.

-   **Access Control op basis van Azure Role (RBAC)**

    Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC kunt u de toegang tot resources beheren door gebruikers de minste machtigingen te verlenen die nodig zijn om hun taken uit te voeren. Zie [Wat is op rollen gebaseerd toegangs beheer (RBAC) in azure?](../../role-based-access-control/overview.md)voor meer informatie over RBAC.

## <a name="common-use-cases"></a>Algemene scenario’s

Verificatie op basis van identiteit en ondersteuning voor Windows-Acl's op Azure Files is het meest geschikt voor de volgende use-cases:

### <a name="replace-on-premises-file-servers"></a>On-premises bestands servers vervangen

Het afmaken en vervangen van verspreide on-premises bestands servers is een veelvoorkomend probleem dat elke onderneming in hun IT-moderniserings traject tegen komt. Azure-bestands shares met AD (preview)-verificatie is het meest geschikt wanneer u de gegevens naar Azure Files kunt migreren. Met een volledige migratie kunt u profiteren van de hoge Beschik baarheid en schaal baarheid, terwijl ook de wijzigingen aan de client zijde worden geminimaliseerd, met name ingewikkelde infra structuur van de AD-domein. Het biedt een naadloze migratie-ervaring voor eind gebruikers, zodat ze hun gegevens met dezelfde referenties kunnen blijven gebruiken met hun bestaande computers die lid zijn van een domein.

### <a name="lift-and-shift-applications-to-azure"></a>Toepassingen naar Azure tillen en verplaatsen

Wanneer u toepassingen in de Cloud opneemt en verschuift, moet u hetzelfde verificatie model voor uw gegevens blijven gebruiken. Als we de op id's gebaseerde ervaring voor toegangs beheer uitbreiden naar Azure-bestands shares, is het niet meer nodig om uw toepassing te wijzigen in moderne authenticatie methoden en de acceptatie van de cloud te versnellen. Azure-bestands shares bieden de mogelijkheid om te integreren met Azure AD DS (GA) of AD (preview) voor verificatie. Als uw abonnement een 100% in de Cloud is en de inspanningen voor het beheer van Cloud infrastructuren tot een minimum beperken, is Azure AD DS beter geschikt als een volledig beheerde domein service. Als u volledige compatibiliteit met de mogelijkheden van AD DS (GA) nodig hebt, kunt u overwegen uw AD-omgeving uit te breiden naar de Cloud door zelf hosting van domein controllers op Vm's. In beide gevallen bieden we de flexibiliteit om de domein services te kiezen die geschikt zijn voor uw bedrijf.

### <a name="backup-and-disaster-recovery-dr"></a>Back-ups en herstel na nood gevallen (DR)

Als u uw primaire bestands opslag on-premises houdt, kunnen Azure-bestands shares fungeren als ideale opslag voor back-ups of DR, om de bedrijfs continuïteit te verbeteren. U kunt Azure-bestands shares gebruiken om een back-up te maken van uw gegevens van bestaande bestands servers, terwijl u Windows-DACL'S behoudt. Voor nood herstel scenario's kunt u een verificatie optie configureren ter ondersteuning van de juiste handhaving van het toegangs beheer bij een failover.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende tabel bevat een overzicht van de ondersteunde Azure file shares-verificatie scenario's voor Azure AD DS (GA) en AD (preview). U kunt het beste de domein service selecteren die u voor uw client omgeving hebt gekozen voor de integratie met Azure Files. Als u AD (preview) al on-premises of op Azure hebt ingesteld, waarbij uw apparaten zijn gekoppeld aan AD, moet u ervoor kiezen om AD (preview) te gebruiken voor de verificatie van Azure-bestands shares. Als u Azure AD DS (GA) al hebt toegepast, moet u dit ook gebruiken voor de verificatie van Azure-bestands shares.


|Azure AD DS-verificatie (GA)  |AD-verificatie (preview-versie)  |
|---------|---------|
|Azure-AD DS domein toegevoegd aan Windows-computers hebben toegang tot Azure-bestands shares met Azure AD-referenties via SMB.     |AD-domein gekoppelde Windows-computers hebben toegang tot Azure-bestands shares met AD-referenties die via SMB worden gesynchroniseerd met Azure AD.         |

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

- De verificatie van Azure AD DS (GA) en AD (preview) biedt geen ondersteuning voor verificatie bij computer accounts. U kunt in plaats daarvan een service aanmeldings account gebruiken.
- Azure AD DS (GA)-verificatie biedt geen ondersteuning voor verificatie voor apparaten die zijn toegevoegd aan Azure AD Cloud.

## <a name="advantages-of-identity-based-authentication"></a>Voor delen van verificatie op basis van identiteit
Verificatie op basis van identiteit voor Azure Files biedt verschillende voor delen ten opzichte van het gebruik van gedeelde sleutel verificatie:

-   **Breid de traditionele toegang tot de bestands share op basis van identiteiten uit naar de Cloud met AD en Azure AD DS**  
    Als u van plan bent om uw toepassing in de cloud te zetten en te verplaatsen, vervangt u traditionele bestands servers door Azure-bestands shares, dan kunt u uw toepassing verifiëren met AD of Azure AD-referenties voor toegang tot bestands gegevens. Azure Files ondersteunt het gebruik van AD of Azure AD-referenties voor toegang tot Azure-bestands shares via SMB vanuit AD of Azure AD DS Vm's die aan een domein zijn gekoppeld.

-   **Gedetailleerd toegangs beheer voor Azure-bestands shares afdwingen**  
    U kunt machtigingen verlenen aan een specifieke identiteit op share-, map-of bestands niveau. Stel bijvoorbeeld dat u verschillende teams hebt die één Azure-bestands share gebruiken voor samen werking tussen projecten. U kunt alle teams toegang verlenen tot niet-gevoelige directory's, terwijl de toegang tot mappen met alleen gevoelige financiële gegevens wordt beperkt tot uw financiële team. 

-   **Back-ups maken van Windows-Acl's (ook wel NTFS genoemd) samen met uw gegevens**  
    U kunt Azure-bestands shares gebruiken om een back-up te maken van uw bestaande on-premises bestands shares. Azure Files behoudt uw Acl's samen met uw gegevens wanneer u een back-up maakt van een bestands share naar Azure-bestands shares via SMB.

## <a name="how-it-works"></a>How it works (Engelstalig artikel)
Azure-bestands shares biedt ondersteuning voor Kerberos-verificatie voor de integratie met Azure AD DS (GA) of AD (preview). Voordat u verificatie op Azure-bestands shares kunt inschakelen, moet u eerst uw domein omgeving instellen. Voor Azure AD DS (GA)-verificatie moet u Azure AD Domain Services en domein inschakelen die lid zijn van de Vm's die u wilt gebruiken om toegang tot bestands gegevens te krijgen. De virtuele machine die aan een domein is gekoppeld, moet zich in hetzelfde virtuele netwerk (VNET) bevinden als uw Azure AD Domain Services. Op dezelfde manier moet u voor de verificatie van AD (preview) de Active Directory domein controller en het domein toevoegen aan uw machines of Vm's.

Wanneer een identiteit die is gekoppeld aan een toepassing die op een virtuele machine wordt uitgevoerd, probeert toegang te krijgen tot gegevens in azure-bestands shares, wordt de aanvraag verzonden naar Azure AD Domain Services om de identiteit te verifiëren. Als de verificatie is geslaagd, retourneert Azure AD Domain Services een Kerberos-token. De toepassing verzendt een aanvraag die het Kerberos-token bevat en Azure-bestands shares gebruiken die token om de aanvraag te autoriseren. Azure-bestands shares ontvangen alleen het token en blijven geen Azure AD-referenties. AD-verificatie werkt op een vergelijk bare manier, waarbij AD het Kerberos-token biedt.

![Scherm opname van diagram van Azure AD-verificatie via SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Verificatie op basis van identiteit inschakelen

U kunt verificatie op basis van een identiteit inschakelen met behulp van Azure AD DS (GA) of AD (preview) voor Azure-bestands shares in uw nieuwe en bestaande opslag accounts. Er kan slechts één domein service worden gebruikt voor verificatie van bestands toegang op het opslag account. Dit is van toepassing op alle bestands shares in het account. Gedetailleerde stapsgewijze instructies voor het instellen van bestands shares voor verificatie met Azure AD DS (GA) in ons artikel [schakel Azure Active Directory Domain Services-verificatie in azure files](storage-files-identity-auth-active-directory-domain-service-enable.md) en richt lijnen voor AD (preview) in ons andere artikel in om [Active Directory verificatie via SMB voor Azure-bestands shares](storage-files-identity-auth-active-directory-enable.md)in te scha kelen.

### <a name="configure-share-level-permissions-for-azure-files"></a>Machtigingen op share niveau voor Azure Files configureren

Als de verificatie van Azure AD DS (GA) of AD (preview) is ingeschakeld, kunt u ingebouwde RBAC-rollen gebruiken of aangepaste rollen configureren voor Azure AD-identiteiten en toegangs rechten toewijzen aan bestands shares in uw opslag accounts. met de toegewezen machtiging kan de verleende identiteit alleen toegang krijgen tot de share, niets anders, niet zelfs de hoofdmap. U moet nog steeds machtigingen op Directory-of bestands niveau configureren voor Azure-bestands shares.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Machtigingen op Directory-of bestands niveau configureren voor Azure Files

Azure-bestands shares afdwingt de standaard Windows-bestands machtigingen op het niveau van de map en het bestand, met inbegrip van de hoofdmap. De configuratie van machtigingen op Directory-of bestands niveau wordt zowel voor SMB als voor de REST ondersteund. Koppel de doel bestands share van uw VM en Configureer machtigingen met behulp van Windows Verkenner, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)of de [set-ACL-](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) opdracht.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>De sleutel van het opslag account voor super gebruiker-machtigingen gebruiken

Een gebruiker die de sleutel van het opslag account heeft, kan toegang krijgen tot Azure-bestands shares met Super User-machtigingen. Super gebruiker-machtigingen slaan alle beperkingen voor toegangs beheer over.

> [!IMPORTANT]
> Onze aanbevolen beveiligings best practice is om te voor komen dat u de sleutels van uw opslag account deelt en wanneer dat mogelijk is op basis van identiteits verificatie.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Map-en bestands-Acl's behouden bij het importeren van gegevens in azure-bestands shares

Azure Files ondersteunt het behouden van Acl's op Directory-of bestands niveau bij het kopiëren van gegevens naar Azure-bestands shares. U kunt Acl's op een map of bestand naar Azure-bestands shares kopiëren met behulp van Azure File Sync of gemeen schappelijke sluit voor bestands verplaatsing. U kunt [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) bijvoorbeeld gebruiken met de vlag `/copy:s` om gegevens en acl's te kopiëren naar een Azure-bestands share. Acl's blijven standaard behouden, u bent niet verplicht om verificatie op basis van een identiteit in te scha kelen voor uw opslag account om Acl's te behouden.

## <a name="pricing"></a>Prijzen
Er worden geen extra kosten in rekening gebracht voor het inschakelen van verificatie op basis van identiteit via SMB voor uw opslag account. Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) en [Azure AD Domain Services prijs](https://azure.microsoft.com/pricing/details/active-directory-ds/) pagina's als u op zoek bent naar Aad DS-gegevens voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over Azure Files en verificatie op basis van identiteit via SMB:

- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Active Directory authenticatie via SMB voor Azure-bestands shares inschakelen](storage-files-identity-auth-active-directory-enable.md)
- [Azure Active Directory Domain Services authenticatie inschakelen op Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Veelgestelde vragen](storage-files-faq.md)
