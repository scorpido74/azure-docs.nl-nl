---
title: Azure-schijfversleuteling op een geïsoleerd netwerk
description: In dit artikel vindt u tips voor probleemoplossing voor Microsoft Azure Disk Encryption voor Linux VM's.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970665"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure-schijfversleuteling op een geïsoleerd netwerk

Wanneer de connectiviteit wordt beperkt door een firewall- of proxy-vereisten of NSG-instellingen (Network Security Group), kan de mogelijkheid van de extensie om benodigde taken uit te voeren worden verstoord. Deze onderbreking kan resulteren in statusberichten zoals 'Extensiestatus niet beschikbaar op de vm'.

## <a name="package-management"></a>Pakketbeheer

Azure Disk Encryption is afhankelijk van een aantal componenten, die doorgaans worden geïnstalleerd als onderdeel van ADE-enablement als deze nog niet aanwezig zijn. Wanneer u zich achter een firewall bevindt of anderszins van het internet wordt geïsoleerd, moeten deze pakketten vooraf zijn geïnstalleerd of lokaal beschikbaar zijn.

Hier zijn de pakketten die nodig zijn voor elke distributie. Zie [ondersteunde VM's en besturingssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems)voor een volledige lijst met ondersteunde distro's en volumetypen.

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Op Red Hat, wanneer een proxy vereist is, moet u ervoor zorgen dat de abonnementsmanager en yum goed zijn ingesteld. Zie [Problemen met abonnementbeheer en yum oplossen](https://access.redhat.com/solutions/189533)voor meer informatie.  

Wanneer pakketten handmatig worden geïnstalleerd, moeten ze ook handmatig worden bijgewerkt als nieuwe versies worden uitgebracht.

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Instellingen voor netwerkbeveiligingsgroepen die worden toegepast, moeten het eindpunt nog steeds toestaan om te voldoen aan de vereiste vereisten voor netwerkconfiguratie voor schijfversleuteling.  Zie [Azure Disk Encryption: netwerkvereisten](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure-schijfversleuteling met Azure AD (vorige versie)

Als u [Azure Disk Encryption gebruikt met Azure AD (vorige versie),](disk-encryption-overview-aad.md)moet de Azure Active [Directory-bibliotheek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) handmatig worden geïnstalleerd voor alle distributies (naast de pakketten die geschikt zijn voor de distro, zoals [hierboven vermeld).](#package-management)

Wanneer versleuteling wordt ingeschakeld met [Azure AD-referenties,](disk-encryption-linux-aad.md)moet de doel-vm connectiviteit toestaan voor zowel Azure Active Directory-eindpunten als Key Vault-eindpunten. Huidige Azure Active Directory-verificatieeindpunten worden onderhouden in secties 56 en 59 van de documentatie over [Office 365-URL's en IP-adresbereiken.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Key Vault-instructies worden gegeven in de documentatie over het [openen van Azure Key Vault achter een firewall.](../../key-vault/key-vault-access-behind-firewall.md)

### <a name="azure-instance-metadata-service"></a>Azure-instantiemetagegevensservice 

De virtuele machine moet toegang hebben tot het eindpunt van de [Azure Instance Metadata-service,](instance-metadata-service.md) dat gebruikmaakt van een bekend niet-routable IP-adres (`169.254.169.254`) dat alleen vanuit de VM toegankelijk is.  Proxyconfiguraties die lokaal HTTP-verkeer naar dit adres wijzigen (bijvoorbeeld het toevoegen van een X-Forwarded-For-header) worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer stappen bekijken voor [het oplossen van problemen met Azure-schijfversleuteling](disk-encryption-troubleshooting.md)
- [Azure-gegevensversleuteling in rust](../../security/fundamentals/encryption-atrest.md)
