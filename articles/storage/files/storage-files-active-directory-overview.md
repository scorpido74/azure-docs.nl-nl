---
title: Overzicht van Azure Active Directory verificatie via SMB voor Azure Files-Azure Storage
description: Azure Files ondersteunt verificatie op basis van identiteiten via SMB (Server Message Block) via Azure Active Directory (Azure AD) Domain Services. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met Azure AD-referenties.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 6cdee8f1ad59962822e9e0394547c395c13e4bd8
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611774"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Overzicht van de verificatie ondersteuning voor Azure Files Azure Active Directory Domain Service (Azure AD DS) voor SMB-toegang
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [Azure Active Directory Domain Service-verificatie inschakelen via SMB voor Azure files](storage-files-active-directory-enable.md)voor meer informatie over het inschakelen van Azure AD DS-verificatie voor Azure files.

## <a name="glossary"></a>Woordenlijst 
Het is handig om enkele belang rijke termen te begrijpen met betrekking tot Azure AD Domain Service-authenticatie via SMB voor Azure Files:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Azure AD is een combi natie van basis Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging in één oplossing. Zie [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie.

-   **Azure AD Domain Services**  
    Azure AD Domain Services biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie. Deze services zijn volledig compatibel met Windows Server Active Directory. Zie [Azure Active Directory (AD) Domain Services (Engelstalig)](../../active-directory-domain-services/overview.md)voor meer informatie.

-   **Access Control op basis van Azure Role (RBAC)**  
    Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC kunt u de toegang tot resources beheren door gebruikers de minste machtigingen te verlenen die nodig zijn om hun taken uit te voeren. Zie [Wat is op rollen gebaseerd toegangs beheer (RBAC) in azure?](../../role-based-access-control/overview.md) voor meer informatie over RBAC.

-   **Kerberos-verificatie**

    Kerberos is een authenticatie protocol dat wordt gebruikt om de identiteit van een gebruiker of host te controleren. Zie [overzicht van Kerberos-verificatie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)voor meer informatie over Kerberos.

-  **SMB-protocol (Server Message Block)**  
    SMB is een standaard protocol voor het delen van netwerk bestanden. SMB is ook bekend als common Internet File System of CIFS. Zie het [overzicht van micro soft SMB-protocol en CIFS-protocol](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)voor meer informatie over SMB.

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Voor delen van Azure AD domain service-verificatie
Azure AD domain service-verificatie voor Azure Files biedt verschillende voor delen ten opzichte van het gebruik van gedeelde sleutel verificatie:

-   **De traditionele toegang tot de Cloud op basis van identiteiten uitbreiden met de Azure AD-en Azure AD-domein service**  
    Als u van plan bent om uw toepassing in de cloud te zetten en te verplaatsen, vervangt u de traditionele bestands servers door Azure Files. vervolgens wilt u uw toepassing verifiëren met Azure AD-referenties voor toegang tot bestands gegevens. Azure Files biedt ondersteuning voor het gebruik van Azure AD-referenties voor toegang tot Azure Files via SMB van Azure AD DS Windows-Vm's die lid zijn van een domein. U kunt er ook voor kiezen om al uw on-premises Active Directory objecten te synchroniseren met Azure AD om gebruikers namen, wacht woorden en andere groeps toewijzingen te behouden.

-   **Gedetailleerd toegangs beheer voor Azure-bestands shares afdwingen**  
    U kunt machtigingen verlenen aan een specifieke identiteit op share-, map-of bestands niveau. Stel bijvoorbeeld dat u verschillende teams hebt die één Azure-bestands share gebruiken voor samen werking tussen projecten. U kunt alle teams toegang verlenen tot niet-gevoelige directory's, terwijl de toegang tot mappen met alleen gevoelige financiële gegevens wordt beperkt tot uw financiële team. 

-   **Back-ups maken van Acl's samen met uw gegevens**  
    U kunt Azure Files gebruiken om een back-up te maken van uw bestaande on-premises bestands shares. Azure Files behoudt uw Acl's samen met uw gegevens wanneer u een back-up maakt van een bestands share naar Azure Files over SMB.

## <a name="how-it-works"></a>Hoe werkt het?
Azure Files gebruikt Azure AD Domain Services om Kerberos-verificatie te ondersteunen met Azure AD-referenties van Vm's die lid zijn van een domein. Voordat u Azure AD met Azure Files kunt gebruiken, moet u eerst Azure AD Domain Services inschakelen en lid worden van de virtuele machines van waaruit u toegang wilt krijgen tot bestands gegevens. De VM van het domein moet zich in hetzelfde virtuele netwerk (VNET) bevinden als Azure AD Domain Services. 

Wanneer een identiteit die is gekoppeld aan een toepassing die op een virtuele machine wordt uitgevoerd, probeert toegang te krijgen tot gegevens in Azure Files, wordt de aanvraag verzonden naar Azure AD Domain Services om de identiteit te verifiëren. Als de verificatie is geslaagd, retourneert Azure AD Domain Services een Kerberos-token. De toepassing verzendt een aanvraag die het Kerberos-token bevat, en Azure Files gebruikt dat token om de aanvraag te autoriseren. Azure Files ontvangt alleen het token en de Azure AD-referenties blijven niet behouden.

![Scherm opname van diagram van Azure AD-verificatie via SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Verificatie van Azure AD-domein service inschakelen voor SMB-toegang
U kunt Azure AD domain service-verificatie inschakelen voor Azure Files op uw nieuwe en bestaande opslag accounts die zijn gemaakt na 24 september 2018. 

Voordat u deze functie inschakelt, moet u controleren of Azure AD Domain Services is geïmplementeerd voor de primaire Azure AD-Tenant waarmee uw opslag account is gekoppeld. Als u Azure AD Domain Services nog niet hebt ingesteld, volgt u de stapsgewijze instructies in [enable Azure Active Directory Domain Services met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

Azure AD Domain Services implementatie duurt over het algemeen 10 tot 15 minuten. Nadat Azure AD Domain Services is geïmplementeerd, kunt u Azure AD-verificatie via SMB inschakelen voor Azure Files. Zie [Azure Active Directory Domain Service-authenticatie via SMB voor Azure files inschakelen](storage-files-active-directory-enable.md)voor meer informatie. 

### <a name="configure-share-level-permissions-for-azure-files"></a>Machtigingen op share niveau voor Azure Files configureren
Zodra Azure AD domain service-verificatie is ingeschakeld, kunt u aangepaste RBAC-rollen voor Azure AD-identiteiten configureren en toegangs rechten toewijzen aan bestands shares in het opslag account.

Wanneer een toepassing die wordt uitgevoerd op een virtuele machine die lid is van een domein probeert een Azure-bestands share te koppelen of een map of bestand te openen, worden de Azure AD-referenties van de toepassing gecontroleerd om te zorgen voor de juiste machtigingen op share niveau en NTFS-machtigingen. Zie voor meer informatie over het configureren van machtigingen op share niveau [Azure Active Directory domein service-authenticatie via SMB inschakelen](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Machtigingen op Directory-of bestands niveau configureren voor Azure Files 
Azure Files dwingt standaard NTFS-bestands machtigingen af op Directory-en bestands niveau, met inbegrip van de hoofdmap. De configuratie van machtigingen op Directory-of bestands niveau wordt alleen voor SMB ondersteund. Koppel de doel bestands share van uw VM en Configureer machtigingen met behulp van Windows Verkenner, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) of de opdracht [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>De sleutel van het opslag account voor super gebruiker-machtigingen gebruiken 
Een gebruiker die de sleutel van het opslag account heeft, heeft toegang tot Azure Files met machtigingen voor super gebruiker. Super gebruiker-machtigingen overschrijden alle toegangs beheer beperkingen die zijn geconfigureerd op het share niveau met RBAC en afgedwongen door Azure AD. Machtigingen voor de super gebruiker zijn vereist voor het koppelen van een Azure-bestands share. 

> [!IMPORTANT]
> Als onderdeel van aanbevolen procedures voor beveiliging vermijdt u het delen van de sleutels van uw opslag account en maakt u waar mogelijk gebruik van Azure AD-machtigingen.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Map-en bestands-Acl's bewaren voor het importeren van gegevens naar Azure-bestands shares
Azure Files ondersteunt nu het behoud van de map-of bestands-Acl's wanneer u gegevens kopieert naar Azure-bestands shares. U kunt de Acl's voor een map of bestand kopiëren naar Azure Files. U kunt bijvoorbeeld [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) met vlag `/copy:s` gebruiken om zowel gegevens als acl's te kopiëren naar een Azure-bestands share. ACL-behoud is standaard ingeschakeld en u hoeft de Azure AD domain service-verificatie functie niet expliciet in te scha kelen voor uw opslag account. 

## <a name="pricing"></a>Prijzen
Er worden geen extra kosten in rekening gebracht om Azure AD-verificatie via SMB in te scha kelen voor uw opslag account. Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) en [Azure AD Domain Services prijs](https://azure.microsoft.com/pricing/details/active-directory-ds/) pagina's voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over Azure Files en Azure AD-verificatie via SMB:

- [Inleiding tot Azure Files](storage-files-introduction.md)
- [Azure Active Directory authenticatie via SMB voor Azure Files inschakelen](storage-files-active-directory-enable.md)
- [Veelgestelde vragen](storage-files-faq.md)
