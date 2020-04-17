---
title: Overzicht - Autorisatie op basis van azure-bestanden
description: Azure Files ondersteunt verificatie op basis van identiteit via SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) en Active Directory. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben vervolgens toegang tot Azure-bestandsshares met Azure AD-referenties.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536574"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Overzicht van azure files-verificatieondersteuning voor SMB-toegang
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [On-premises Active Directory Domain Services-verificatie via SMB inschakelen voor Azure-bestandsshares voor on-premises Active Directory Domain Services.For](storage-files-identity-auth-active-directory-enable.md)learn how to enable on-premises Active Directory Domain Services authentication for Azure file shares (preview) see Enable on-premises Active Directory Domain Services authentication over SMB for Azure file shares .

Zie [Azure Active Directory Domain Services-verificatie inschakelen op Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)voor meer informatie over het inschakelen van Azure AD DS-verificatie voor Azure-bestandsshares.

## <a name="glossary"></a>Woordenlijst 
Het is handig om enkele belangrijke termen met betrekking tot Azure AD Domain Service-verificatie via SMB voor Azure-bestandsshares te begrijpen:

-   **Kerberos-verificatie**

    Kerberos is een authenticatieprotocol dat wordt gebruikt om de identiteit van een gebruiker of host te controleren. Zie [Kerberos-verificatieoverzicht](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)voor meer informatie over Kerberos.

-  **SMB-protocol (Server Message Block)**

    SMB is een industriestandaard protocol voor het delen van netwerkbestanden. SMB wordt ook wel Common Internet File System of CIFS genoemd. Zie Overzicht van het [Microsoft SMB-protocol en het CIFS-protocol](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)voor meer informatie over het MKB.

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) is de op meerdere tenant cloudgebaseerde directory- en identiteitsbeheerservice van Microsoft. Azure AD combineert kerndirectoryservices, toepassingstoegangsbeheer en identiteitsbescherming in één oplossing. Vm's (Windows Virtual Machines) met Azure AD hebben toegang tot Azure-bestandsshares met uw Azure AD-referenties. Zie Wat is Azure Active Directory voor meer [informatie?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP en Kerberos/NTLM-verificatie. Deze services zijn volledig compatibel met Active Directory Domain Services. Zie [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)voor meer informatie.

- **On-premises Active Directory Domain Services (AD DS)**

    On-premises AD DS-integratie (Active Directory Domain Services) met Azure Files (preview) biedt de methoden voor het opslaan van directorygegevens en maakt deze beschikbaar voor netwerkgebruikers en beheerders. Beveiliging is geïntegreerd met AD DS door middel van aanmeldingsverificatie en toegangsbeheer tot objecten in de directory. Met één netwerkaanmelding kunnen beheerders directorygegevens en -organisatie in hun netwerk beheren en kunnen geautoriseerde netwerkgebruikers overal in het netwerk toegang krijgen tot bronnen. AD DS wordt vaak aangenomen door ondernemingen in on-premises omgevingen en AD DS-referenties worden gebruikt als de identiteit voor toegangscontrole. Zie [Overzicht van Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie.

-   **Azure Role Based Access Control (RBAC)**

    Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC u de toegang tot bronnen beheren door gebruikers de minste machtigingen te geven die nodig zijn om hun taken uit te voeren. Zie Wat is op [rolgebaseerde toegangscontrole (RBAC) in Azure?](../../role-based-access-control/overview.md)voor meer informatie over RBAC.

## <a name="common-use-cases"></a>Algemene scenario’s

Verificatie en ondersteuning op basis van identiteit voor Windows-ACL's in Azure-bestanden is het beste te gebruiken voor de volgende use cases:

### <a name="replace-on-premises-file-servers"></a>On-premises bestandsservers vervangen

Het uitkeuren en vervangen van verspreide on-premises bestandsservers is een veel voorkomend probleem dat elke onderneming tegenkomt in hun IT-moderniseringstraject. Azure-bestand deelt met on-premises AD DS -verificatie (preview)-verificatie past hier het beste, wanneer u de gegevens migreren naar Azure Files. Een volledige migratie stelt u in staat om te profiteren van de voordelen voor hoge beschikbaarheid en schaalbaarheid en tegelijkertijd de wijzigingen aan de clientzijde te minimaliseren. Het biedt een naadloze migratie-ervaring voor eindgebruikers, zodat ze toegang kunnen blijven krijgen tot hun gegevens met dezelfde referenties met behulp van hun bestaande domein samengevoegde machines.

### <a name="lift-and-shift-applications-to-azure"></a>Toepassingen naar Azure tillen en verschuiven

Wanneer u toepassingen naar de cloud tilt en verschuift, wilt u hetzelfde verificatiemodel voor uw gegevens behouden. Als we de identiteitsgebaseerde toegangscontrole-ervaring uitbreiden naar Azure-bestandsshares, hoeft u uw toepassing niet meer te wijzigen in moderne auth-methoden en de acceptatie van de cloud te versnellen. Azure-bestandsshares bieden de optie om te integreren met Azure AD DS of on-premises AD DS (preview) voor verificatie. Als uw plan 100% cloud native wil zijn en de inspanningen voor het beheer van cloudinfrastructuren wilt minimaliseren, past Azure AD DS beter als een volledig beheerde domeinservice. Als u volledige compatibiliteit met AD DS-mogelijkheden nodig hebt, u overwegen uw AD DS-omgeving uit te breiden naar de cloud door domeincontrollers voor zelfhosting op VM's. Hoe dan ook bieden we de flexibiliteit om de domeinservices te kiezen die passen bij uw bedrijfsbehoeften.

### <a name="backup-and-disaster-recovery-dr"></a>Back-up en noodherstel (DR)

Als u uw primaire bestandsopslag on-premises houdt, kunnen Azure-bestandsshares dienen als een ideale opslag voor back-up of DR, om de bedrijfscontinuïteit te verbeteren. U Azure-bestandsshares gebruiken om een back-up van uw gegevens van bestaande bestandsservers te maken, terwijl de SER's van Windows behouden blijven. Voor DR-scenario's u een verificatieoptie configureren om de juiste toegangscontrolehandhaving bij failover te ondersteunen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

In de volgende tabel worden de ondersteunde verificatiescenario's voor Azure-bestanden voor Azure AD DS en on-premises AD DS (voorbeeld) samengevat. We raden u aan de domeinservice te selecteren die u voor uw clientomgeving hebt gebruikt voor integratie met Azure Files. Als ad DS (preview) al on-premises of in Azure is ingesteld waar uw apparaten zijn verbonden met uw AD, u ervoor kiezen om AD DS (preview) te gebruiken voor azure-verificatie voor bestandsshares. Als u Azure AD DS (GA) al hebt goedgekeurd, moet u dat ook gebruiken voor azure-verificatie voor bestandsshares.


|Azure AD DS-verificatie  | on-premises AD DS-verificatie (voorbeeld)  |
|---------|---------|
|Windows-machines met Azure AD-lid hebben toegang tot Azure-bestandsshares met Azure AD-referenties via SMB.     |On-premises AD DS-aangesloten Windows-machines hebben toegang tot Azure-bestandsshares met on-premises Active Directory-referenties die worden gesynchroniseerd met Azure AD via SMB.         |

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

- Azure AD DS en on-premises AD DS-verificatie bieden geen ondersteuning voor verificatie met computeraccounts. U in plaats daarvan overwegen om een service-aanmeldingsaccount te gebruiken.
- Azure AD DS-verificatie biedt geen ondersteuning voor verificatie met A2-apparaten die zijn aangesloten bij Azure.

## <a name="advantages-of-identity-based-authentication"></a>Voordelen van identiteitsgebaseerde authenticatie
Verificatie op basis van identiteit voor Azure Files biedt verschillende voordelen ten opzichte van het gebruik van shared key-verificatie:

-   **De traditionele toegang tot bestandsshare op basis van identiteit uitbreiden naar de cloud met on-premises AD DS en Azure AD DS**  
    Als u van plan bent uw toepassing op te heffen en te verplaatsen naar de cloud, traditionele bestandsservers te vervangen door Azure-bestandsshares, wilt u mogelijk dat uw toepassing zich verifieert met on-premises AD DS- of Azure AD DS-referenties om toegang te krijgen tot bestandsgegevens. Azure Files ondersteunt het gebruik van zowel on-premises AD DS- als Azure AD DS-referenties om toegang te krijgen tot Azure-bestandsshares via SMB vanaf on-premises AD DS- of Azure AD DS-vm's die zijn verbonden aan het DOMEIN.

-   **Gedetailleerd toegangsbeheer afdwingen op Azure-bestandsshares**  
    U machtigingen verlenen aan een specifieke identiteit op het niveau van delen, directory of bestand. Stel dat u meerdere teams hebt die één Azure-bestandsshare gebruiken voor projectsamenwerking. U alle teams toegang verlenen tot niet-gevoelige mappen, terwijl u de toegang tot mappen met gevoelige financiële gegevens beperkt tot uw financeteam. 

-   **Maak een back-up van Windows ACL's (ook bekend als NTFS) samen met uw gegevens**  
    U Azure-bestandsshares gebruiken om een back-up te maken van uw bestaande on-premises bestandsshares. Azure Files behoudt uw ACL's samen met uw gegevens wanneer u een back-up maakt van een bestandsshare naar Azure-bestandsshares via SMB.

## <a name="how-it-works"></a>Hoe werkt het?

Azure-bestandsshares ondersteunt Kerberos-verificatie voor integratie met Azure AD DS of on-premises AD DS (preview). Voordat u verificatie op Azure-bestandsshares inschakelen, moet u eerst uw domeinomgeving instellen. Voor Azure AD DS-verificatie moet u Azure AD Domain Services en domeinlid van de VM's inschakelen die u van plan bent om toegang te krijgen tot bestandsgegevens. Uw vm die is verbonden aan uw domein moet zich in hetzelfde virtuele netwerk (VNET) bevinden als uw AD DS in Azure. Voor on-premises AD DS-verificatie (preview)-verificatie moet u ook uw domeincontroller en domeininstellen om lid te worden van uw machines of VM's.

Wanneer een identiteit die is gekoppeld aan een toepassing die wordt uitgevoerd op een vm probeert toegang te krijgen tot gegevens in Azure-bestandsshares, wordt het verzoek verzonden naar Azure AD DS om de identiteit te verifiëren. Als de verificatie is geslaagd, retourneert Azure AD DS een Kerberos-token. De toepassing verzendt een verzoek dat het Kerberos-token bevat en Azure-bestandsshares gebruiken dat token om de aanvraag te autoriseren. Azure-bestandsshares ontvangen het token alleen en blijven niet bestaan uit Azure AD DS-referenties. On-premises AD DS-verificatie werkt op een vergelijkbare manier, waarbij uw AD DS het Kerberos-token biedt.

![Schermafbeelding van diagram met Azure AD-verificatie via SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Verificatie op basis van identiteit inschakelen

U verificatie op basis van identiteit inschakelen met Azure AD DS of on-premises AD DS (preview) voor Azure-bestandsshares op uw nieuwe en bestaande opslagaccounts. Er kan slechts één domeinservice worden gebruikt voor verificatie van bestandstoegang op het opslagaccount, wat van toepassing is op alle bestandsshares in het account. Gedetailleerde richtlijnen voor het instellen van uw bestandsshares voor verificatie met Azure AD DS in ons artikel [Azure Active Directory Domain Services-verificatie inschakelen voor Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) en richtlijnen voor on-premises AD DS (preview) in ons andere artikel, [On-premises Active Directory Domain Services-verificatie inschakelen via SMB voor Azure-bestandsshares.](storage-files-identity-auth-active-directory-enable.md)

### <a name="configure-share-level-permissions-for-azure-files"></a>Machtigingen op shareniveau configureren voor Azure-bestanden

Zodra Azure AD DS- of on-premises AD DS-verificatie (preview)-verificatie is ingeschakeld, u ingebouwde RBAC-rollen gebruiken of aangepaste rollen configureren voor Azure AD-identiteiten en toegangsrechten toewijzen aan bestandsshares in uw opslagaccounts. Met de toegewezen machtiging kan de verleende identiteit alleen toegang krijgen tot het aandeel, niets anders, zelfs niet de hoofdmap. U moet nog steeds machtigingen op map- of bestandsniveau voor Azure-bestandsshares afzonderlijk configureren.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Machtigingen voor directory- of bestandsniveau configureren voor Azure-bestanden

Azure-bestandsshares dwingen standaard Windows-bestandsmachtigingen af op zowel directory- als bestandsniveau, inclusief de hoofdmap. Configuratie van machtigingen op map- of bestandsniveau wordt ondersteund via zowel SMB als REST. Monteer de doelbestandsshare vanaf uw VM en configureer machtigingen met Windows File Explorer, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)of de opdracht [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>De opslagaccountsleutel gebruiken voor superusermachtigingen

Een gebruiker met de opslagaccountsleutel heeft toegang tot Azure-bestandsshares met superusermachtigingen. Superuser-machtigingen omzeilen alle toegangscontrolebeperkingen.

> [!IMPORTANT]
> Onze aanbevolen beveiligingsaanbevolen procedures zijn om te voorkomen dat uw opslagaccountsleutels worden gedeeld en waar mogelijk gebruik te maken van identiteitsgebaseerde verificatie.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Directory- en bestandsacl.n.s. behouden bij het importeren van gegevens naar Azure-bestandsshares

Azure Files ondersteunt het bewaren van acl. directory- of bestandsniveau bij het kopiëren van gegevens naar Azure-bestandsshares. U ACL's in een map of bestand kopiëren naar Azure-bestandsshares met Azure File Sync of algemene toolsets voor bestandsverplaatsingen. U bijvoorbeeld [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) met `/copy:s` de vlag gebruiken om gegevens en ACL's naar een Azure-bestandsshare te kopiëren. ACL's worden standaard bewaard, u hoeft verificatie op basis van identiteit op uw opslagaccount niet in te schakelen om ACL's te behouden.

## <a name="pricing"></a>Prijzen
Er zijn geen extra servicekosten om verificatie op basis van identiteit via SMB in te schakelen op uw opslagaccount. Zie [Azure Files-prijzen](https://azure.microsoft.com/pricing/details/storage/files/) en [azure AD Domain Services-prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/)voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over Azure Files en verificatie op basis van identiteit via SMB:

- [Planning voor de implementatie van Azure Files](storage-files-planning.md)
- [On-premises Active Directory Domain Services-verificatie via SMB inschakelen voor Azure-bestandsshares](storage-files-identity-auth-active-directory-enable.md)
- [Azure Active Directory Domain Services-verificatie inschakelen in Azure-bestanden](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Veelgestelde vragen](storage-files-faq.md)
