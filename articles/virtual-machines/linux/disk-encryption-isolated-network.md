---
title: Azure Disk Encryption op een geïsoleerd netwerk
description: In dit artikel vindt u tips voor het oplossen van problemen met Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970665"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption op een geïsoleerd netwerk

Wanneer verbinding wordt beperkt door een firewall of proxy vereiste netwerkinstellingen security group (NSG), kan het vermogen van de extensie noodzakelijke taken worden onderbroken. Deze wordt onderbroken, kan resulteren in statusberichten, zoals "Status van extensie niet beschikbaar op de virtuele machine."

## <a name="package-management"></a>Pakketbeheer

Azure Disk Encryption is afhankelijk van een aantal onderdelen, die meestal worden geïnstalleerd als onderdeel van ADE-inactivering als dit nog niet is gebeurd. Wanneer u zich achter een firewall bevindt of op een andere manier geïsoleerd van Internet, moeten deze pakketten vooraf zijn geïnstalleerd of lokaal beschikbaar zijn.

Dit zijn de pakketten die nodig zijn voor elke distributie. Zie [ondersteunde vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)voor een volledige lijst met ondersteunde distributies-en volume typen.

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, psmisc, op, cryptsetup-bin, python-parted, python-zes, procps
- **CentOS 7,2-7,7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencryption, pyparted, procps-aardgas, util-linux
- **CentOS 6,8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypte, pyparted, python-zes
- **RedHat 7,2-7,7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencryption, procps-aardgas, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup opnieuw versleutelen
- **openSUSE 42,3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Red Hat, wanneer er een proxy is vereist, moet u ervoor zorgen dat de abonnement-manager en yum zijn juist ingesteld. Zie problemen [oplossen met abonnement-manager en yum](https://access.redhat.com/solutions/189533)voor meer informatie.  

Wanneer pakketten hand matig worden geïnstalleerd, moeten ze ook hand matig worden bijgewerkt wanneer nieuwe versies worden vrijgegeven.

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Alle instellingen voor de netwerk beveiligings groep die worden toegepast, moeten het eind punt nog steeds toestaan om te voldoen aan de gedocumenteerde vereisten voor de netwerk configuratie voor schijf versleuteling.  Zie [Azure Disk Encryption: netwerk vereisten](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption met Azure AD (vorige versie)

Als [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md)gebruikt, moet de [Azure Active Directory-bibliotheek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) hand matig worden geïnstalleerd voor alle distributies (in aanvulling op de pakketten die voor de distributie van toepassing zijn, zoals [hierboven vermeld](#package-management)).

Wanneer versleuteling wordt ingeschakeld met [Azure AD-referenties](disk-encryption-linux-aad.md), moet de doel-VM verbinding kunnen maken met zowel Azure Active Directory-eind punten als Key Vault-eind punten. De huidige Azure Active Directory-verificatie-eind punten worden onderhouden in de secties 56 en 59 van de documentatie voor [Office 365-url's en IP-](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) adresbereiken. Key Vault instructies vindt u in de documentatie over het [verkrijgen van toegang tot Azure Key Vault achter een firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

De virtuele machine moet toegang hebben tot de [meta gegevens service](instance-metadata-service.md) -eind punt van Azure, die gebruikmaakt van een bekend, niet-routeerbaar IP-adres (`169.254.169.254`) dat alleen vanuit de VM kan worden geopend.  Proxy configuraties waarmee lokaal HTTP-verkeer naar dit adres wordt gewijzigd (bijvoorbeeld het toevoegen van een X-doorgestuurd-for-header) worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer stappen voor het [oplossen van problemen met Azure Disk Encryption](disk-encryption-troubleshooting.md) bekijken
- [Azure-gegevens versleuteling in rust](../../security/fundamentals/encryption-atrest.md)
