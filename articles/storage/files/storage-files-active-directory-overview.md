---
title: Overzicht - Autorisatie op basis van azure-bestanden
description: Azure Files ondersteunt verificatie op basis van identiteit via SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) en Active Directory. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben vervolgens toegang tot Azure-bestandsshares met Azure AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061076"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Overzicht van azure files-verificatieondersteuning voor SMB-toegang
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [Active Directory-verificatie inschakelen via SMB voor Azure-bestandsshares voor](storage-files-identity-auth-active-directory-enable.md)meer informatie over het inschakelen van AD-verificatie voor Azure-bestandsshares.

Zie [Azure Active Directory Domain Services-verificatie inschakelen op Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)voor meer informatie over het inschakelen van Azure AD DS-verificatie voor Azure-bestandsshares.

## <a name="glossary"></a>Woordenlijst 
Het is handig om enkele belangrijke termen met betrekking tot Azure AD Domain Service-verificatie via SMB voor Azure-bestandsshares te begrijpen:

-   **Kerberos-verificatie**

    Kerberos is een authenticatieprotocol dat wordt gebruikt om de identiteit van een gebruiker of host te controleren. Zie [Kerberos-verificatieoverzicht](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)voor meer informatie over Kerberos.

-  **SMB-protocol (Server Message Block)**

    SMB is een industriestandaard protocol voor het delen van netwerkbestanden. SMB wordt ook wel Common Internet File System of CIFS genoemd. Zie Overzicht van het [Microsoft SMB-protocol en het CIFS-protocol](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)voor meer informatie over het MKB.

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) is de op meerdere tenant cloudgebaseerde directory- en identiteitsbeheerservice van Microsoft. Azure AD combineert kerndirectoryservices, toepassingstoegangsbeheer en identiteitsbescherming in één oplossing. Met Azure AD kunnen uw met een domein verbonden Windows Virtual Machines (VM's) toegang krijgen tot Azure-bestandsshares met uw Azure AD-referenties. Zie Wat is Azure Active Directory voor meer [informatie?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP en Kerberos/NTLM-verificatie. Deze services zijn volledig compatibel met Windows Server Active Directory. Zie [Azure Active Directory (AD) Domain Services voor](../../active-directory-domain-services/overview.md)meer informatie .

- **Active Directory Domain Services (AD DS, ook wel AD genoemd)**

    Active directory (AD) (preview) biedt de methoden voor het opslaan van directorygegevens en maakt deze beschikbaar voor netwerkgebruikers en beheerders. Beveiliging is geïntegreerd met Active Directory door middel van aanmeldingsverificatie en toegangsbeheer tot objecten in de map. Met één netwerkaanmelding kunnen beheerders directorygegevens en -organisatie in hun netwerk beheren en kunnen geautoriseerde netwerkgebruikers overal in het netwerk toegang krijgen tot bronnen. AD wordt vaak aangenomen door ondernemingen in on-premises en gebruiken AD-referenties als de identiteit voor toegangscontrole. Zie [Overzicht van Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie.

-   **Azure Role Based Access Control (RBAC)**

    Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC u de toegang tot bronnen beheren door gebruikers de minste machtigingen te geven die nodig zijn om hun taken uit te voeren. Zie Wat is op [rolgebaseerde toegangscontrole (RBAC) in Azure?](../../role-based-access-control/overview.md)voor meer informatie over RBAC.

## <a name="common-use-cases"></a>Algemene scenario’s

Verificatie en ondersteuning op basis van identiteit voor Windows-ACL's in Azure-bestanden is het beste te gebruiken voor de volgende use cases:

### <a name="replace-on-premises-file-servers"></a>On-premises bestandsservers vervangen

Het uitkeuren en vervangen van verspreide on-premises bestandsservers is een veel voorkomend probleem dat elke onderneming tegenkomt in hun IT-moderniseringstraject. Azure-bestandsshares met AD -verificatie (preview)-verificatie passen hier het beste, wanneer u de gegevens migreren naar Azure-bestanden. Met een volledige migratie u profiteren van de voordelen voor hoge beschikbaarheid en schaalbaarheid, terwijl u ook de wijzigingen aan de clientzijde minimaliseert, met name gecompliceerde AD-domeininfrastructuur. Het biedt een naadloze migratie-ervaring voor eindgebruikers, zodat ze toegang kunnen blijven krijgen tot hun gegevens met dezelfde referenties met behulp van hun bestaande domein samengevoegde machines.

### <a name="lift-and-shift-applications-to-azure"></a>Toepassingen naar Azure tillen en verschuiven

Wanneer u toepassingen "liften en verschuiven" naar de cloud, wilt u hetzelfde verificatiemodel voor uw gegevens behouden. Als we de identiteitsgebaseerde toegangscontrole-ervaring uitbreiden naar Azure-bestandsshares, hoeft u uw toepassing niet meer te wijzigen in moderne auth-methoden en de acceptatie van de cloud te versnellen. Azure-bestandsshares bieden de optie om te integreren met Azure AD DS (GA) of AD (preview) voor verificatie. Als uw plan 100% cloud native wil zijn en de inspanningen voor het beheer van cloudinfrastructuren wilt minimaliseren, past Azure AD DS beter als een volledig beheerde domeinservice. Als u volledige compatibiliteit met AD DS-mogelijkheden (GA) nodig hebt, u overwegen uw AD-omgeving uit te breiden naar de cloud door domeincontrollers voor zelfhosting op VM's te hosten. Hoe dan ook bieden we de flexibiliteit om de domeinservices te kiezen die passen bij uw bedrijfsbehoeften.

### <a name="backup-and-disaster-recovery-dr"></a>Back-up en noodherstel (DR)

Als u uw primaire bestandsopslag on-premises houdt, kunnen Azure-bestandsshares dienen als een ideale opslag voor back-up of DR, om de bedrijfscontinuïteit te verbeteren. U Azure-bestandsshares gebruiken om een back-up van uw gegevens van bestaande bestandsservers te maken, terwijl de SER's van Windows behouden blijven. Voor DR-scenario's u een verificatieoptie configureren om de juiste toegangscontrolehandhaving bij failover te ondersteunen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

In de volgende tabel worden de ondersteunde verificatiescenario's voor Azure AD DS (GA) en AD (voorbeeld) samengevat. We raden u aan de domeinservice te selecteren die u voor uw clientomgeving hebt gebruikt voor integratie met Azure Files. Als ad (preview) al on-premises of op Azure is ingesteld waar uw apparaten zijn verbonden met AD, moet u ervoor kiezen om AD (preview) te gebruiken voor azure-verificatie voor bestandsshares. Als u Azure AD DS (GA) al hebt goedgekeurd, moet u dat ook gebruiken voor azure-verificatie voor bestandsshares.


|Azure AD DS -verificatie (GA)  |VERIFICATIE AD (voorbeeld)  |
|---------|---------|
|Azure AD DS-domein samengevoegde Windows-machines hebben toegang tot Azure-bestandsshares met Azure AD-referenties via SMB.     |Ad-domein samengevoegde Windows-machines hebben toegang tot Azure-bestandsshares met AD-referenties die worden gesynchroniseerd met Azure AD via SMB.         |

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

- Azure AD DS (GA) en AD -verificatie (voorbeeld) ondersteunen geen verificatie tegen computeraccounts. U in plaats daarvan overwegen om een service-aanmeldingsaccount te gebruiken.
- Azure AD DS -verificatie (GA) biedt geen ondersteuning voor verificatie met azure AD-cloud-apparaten.

## <a name="advantages-of-identity-based-authentication"></a>Voordelen van identiteitsgebaseerde authenticatie
Verificatie op basis van identiteit voor Azure Files biedt verschillende voordelen ten opzichte van het gebruik van shared key-verificatie:

-   **De traditionele toegang tot bestandsshare op basis van identiteit uitbreiden naar de cloud met AD en Azure AD DS**  
    Als u van plan bent uw toepassing naar de cloud te tillen en te verplaatsen, traditionele bestandsservers te vervangen door Azure-bestandsshares, u uw toepassing verifiëren met AD- of Azure AD-referenties om toegang te krijgen tot bestandsgegevens. Azure Files ondersteunt het gebruik van zowel AD- als Azure AD-referenties om toegang te krijgen tot Azure-bestandsshares via SMB vanuit AD- of Azure AD DS-vm's met domein.

-   **Gedetailleerd toegangsbeheer afdwingen op Azure-bestandsshares**  
    U machtigingen verlenen aan een specifieke identiteit op het niveau van delen, directory of bestand. Stel dat u meerdere teams hebt die één Azure-bestandsshare gebruiken voor projectsamenwerking. U alle teams toegang verlenen tot niet-gevoelige mappen, terwijl u de toegang tot mappen met gevoelige financiële gegevens beperkt tot uw financeteam. 

-   **Maak een back-up van Windows ACL's (ook bekend als NTFS) samen met uw gegevens**  
    U Azure-bestandsshares gebruiken om een back-up te maken van uw bestaande on-premises bestandsshares. Azure Files behoudt uw ACL's samen met uw gegevens wanneer u een back-up maakt van een bestandsshare naar Azure-bestandsshares via SMB.

## <a name="how-it-works"></a>Hoe werkt het?
Azure-bestandsshares ondersteunt kerberos-verificatie voor integratie met Azure AD DS (GA) of AD (voorbeeld). Voordat u verificatie op Azure-bestandsshares inschakelen, moet u eerst uw domeinomgeving instellen. Voor Azure AD DS-verificatie (GA) moet u Azure AD Domain Services en domeinlid maken van de VM's waaruit u van plan bent om toegang te krijgen tot bestandsgegevens. Uw vm die is verbonden aan het domein moet zich in hetzelfde virtuele netwerk (VNET) bevinden als uw Azure AD Domain Services. Voor AD-verificatie (preview)-verificatie moet u ook uw Active Directory-domeincontroller en domeinlid van uw machines of VM's instellen.

Wanneer een identiteit die is gekoppeld aan een toepassing die wordt uitgevoerd op een VM probeert toegang te krijgen tot gegevens in Azure-bestandsshares, wordt het verzoek verzonden naar Azure AD Domain Services om de identiteit te verifiëren. Als de verificatie is geslaagd, retourneert Azure AD Domain Services een Kerberos-token. De toepassing verzendt een verzoek dat het Kerberos-token bevat en Azure-bestandsshares gebruiken dat token om de aanvraag te autoriseren. Azure-bestandsshares ontvangen het token alleen en blijven niet bestaan uit Azure AD-referenties. AD-verificatie werkt op een vergelijkbare manier, waarbij AD het Kerberos-token aanbiedt.

![Schermafbeelding van diagram met Azure AD-verificatie via SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Verificatie op basis van identiteit inschakelen

U verificatie op basis van identiteit inschakelen met Azure AD DS (GA) of AD (preview) voor Azure-bestandsshares op uw nieuwe en bestaande opslagaccounts. Er kan slechts één domeinservice worden gebruikt voor verificatie van bestandstoegang op het opslagaccount, wat van toepassing is op alle bestandsshares in het account. Gedetailleerde stapsgewijze richtlijnen voor het instellen van uw bestandsshares voor verificatie met Azure AD DS (GA) in ons artikel [Azure Active Directory Domain Services-verificatie inschakelen voor Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) en richtlijnen voor AD (preview) in ons andere artikel, Active [Directory-verificatie inschakelen via SMB voor Azure-bestandsshares](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Machtigingen op shareniveau configureren voor Azure-bestanden

Zodra Azure AD DS -verificatie (GA) of AD -verificatie is ingeschakeld, u ingebouwde RBAC-rollen gebruiken of aangepaste rollen configureren voor Azure AD-identiteiten en toegangsrechten toewijzen aan bestandsshares in uw opslagaccounts. met de toegewezen toestemming kan de verleende identiteit alleen toegang krijgen tot het aandeel, niets anders, zelfs niet de hoofdmap. U moet nog steeds machtigingen op map- of bestandsniveau voor Azure-bestandsshares afzonderlijk configureren.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Machtigingen voor directory- of bestandsniveau configureren voor Azure-bestanden

Azure-bestandsshares dwingt standaard Windows-bestandsmachtigingen af op zowel directory- als bestandsniveau, inclusief de hoofdmap. Configuratie van machtigingen op map- of bestandsniveau wordt ondersteund via zowel SMB als REST. Monteer de doelbestandsshare vanaf uw VM en configureer machtigingen met Windows File Explorer, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)of de opdracht [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>De opslagaccountsleutel gebruiken voor superusermachtigingen

Een gebruiker die de opslagaccountsleutel bezit, heeft toegang tot Azure-bestandsshares met superusermachtigingen. Superuser-machtigingen omzeilen alle toegangscontrolebeperkingen.

> [!IMPORTANT]
> Onze aanbevolen best practice voor beveiliging is om te voorkomen dat uw opslagaccountsleutels worden gedeeld en waar mogelijk gebruik te maken van identiteitsverificatie.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Directory- en bestandsacl.n.s. behouden bij het importeren van gegevens naar Azure-bestandsshares

Azure Files ondersteunt het bewaren van acl. directory- of bestandsniveau bij het kopiëren van gegevens naar Azure-bestandsshares. U ACL's in een map of bestand kopiëren naar Azure-bestandsshares met Azure File Sync of algemene toolsets voor bestandsverplaatsingen. U bijvoorbeeld [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) met `/copy:s` de vlag gebruiken om gegevens en ACL's naar een Azure-bestandsshare te kopiëren. ACL's worden standaard bewaard, u hoeft verificatie op basis van identiteit op uw opslagaccount niet in te schakelen om ACL's te behouden.

## <a name="pricing"></a>Prijzen
Er zijn geen extra servicekosten om verificatie op basis van identiteit via SMB in te schakelen op uw opslagaccount. Zie [prijspagina's](https://azure.microsoft.com/pricing/details/storage/files/) voor Azure Files en [Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) als u op zoek bent naar AAD DS-informatie voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over Azure Files en verificatie op basis van identiteit via SMB:

- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Active Directory-verificatie via SMB inschakelen voor Azure-bestandsshares](storage-files-identity-auth-active-directory-enable.md)
- [Azure Active Directory Domain Services-verificatie inschakelen in Azure-bestanden](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Veelgestelde vragen](storage-files-faq.md)
