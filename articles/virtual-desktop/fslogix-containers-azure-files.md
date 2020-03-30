---
title: Bestanden van Windows Virtual Desktop FSLogix-profielcontainers - Azure
description: In dit artikel worden FSLogix-profielcontainers beschreven in Windows Virtual Desktop- en Azure-bestanden.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127875"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profielcontainers en Azure Files

De Windows Virtual Desktop-service raadt FSLogix-profielcontainers aan als gebruikersprofieloplossing. FSLogix is ontworpen om te roamen met profielen in externe computeromgevingen, zoals Windows Virtual Desktop. Het slaat een volledig gebruikersprofiel op in één container. Bij het aanmelden is deze container dynamisch verbonden met de computeromgeving met behulp van native ondersteunde Virtual Hard Disk (VHD) en Hyper-V Virtual Hard Disk (VHDX). Het gebruikersprofiel is onmiddellijk beschikbaar en verschijnt in het systeem precies als een native gebruikersprofiel. In dit artikel wordt beschreven hoe FSLogix-profielcontainers worden gebruikt met de functie Azure Files in Windows Virtual Desktop.

>[!NOTE]
>Zie Opslagopties voor [FSLogix-profielcontainers](store-fslogix-profile.md)als u op zoek bent naar vergelijkingsmateriaal over de verschillende opslagopties voor FSLogix-profiel .

## <a name="user-profiles"></a>Gebruikersprofielen

Een gebruikersprofiel bevat gegevenselementen over een individu, waaronder configuratie-informatie zoals bureaubladinstellingen, permanente netwerkverbindingen en toepassingsinstellingen. Standaard maakt Windows een lokaal gebruikersprofiel dat nauw is geïntegreerd met het besturingssysteem.

Een extern gebruikersprofiel biedt een partitie tussen gebruikersgegevens en het besturingssysteem. Hiermee kan het besturingssysteem worden vervangen of gewijzigd zonder dat dit gevolgen heeft voor de gebruikersgegevens. In RdSH (Remote Desktop Session Host) en Virtual Desktop Infrastructures (VDI) kan het besturingssysteem om de volgende redenen worden vervangen:

- Een upgrade van het besturingssysteem
- Een vervanging van een bestaande virtuele machine (VM)
- Een gebruiker die deel uitmaakt van een gepoolde (niet-permanente) RDSH- of VDI-omgeving

Microsoft-producten werken met verschillende technologieën voor externe gebruikersprofielen, waaronder deze technologieën:
- Zwervende gebruikersprofielen (RUP)
- Schijven voor gebruikersprofiel (UPD)
- Bedrijfsstatusroaming (ESR)

UPD en RUP zijn de meest gebruikte technologieën voor gebruikersprofielen in RDSH- en Virtual Hard Disk(VHD)-omgevingen (Remote Desktop Session Host).

### <a name="challenges-with-previous-user-profile-technologies"></a>Uitdagingen met eerdere gebruikersprofieltechnologieën

Bestaande en verouderde Microsoft-oplossingen voor gebruikersprofielen kwamen met verschillende uitdagingen. Geen eerdere oplossing behandeld alle gebruikersprofiel behoeften die worden geleverd met een RDSH of VDI-omgeving. UPD kan bijvoorbeeld geen grote OST-bestanden verwerken en RUP blijft niet bestaan uit moderne instellingen.

#### <a name="functionality"></a>Functionaliteit

In de volgende tabel ziet u voordelen en beperkingen van eerdere gebruikersprofieltechnologieën.

| Technologie | Moderne instellingen | Win32-instellingen | BE-instellingen | Gebruikersgegevens | Ondersteund op server SKU | Back-endopslag op Azure | Back-end opslag on-premises | Versieondersteuning | Volgende aanmeldingstijd |Opmerkingen|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Schijven voor gebruikersprofielen (UPD)** | Ja | Ja | Ja | Ja | Ja | Nee | Ja | Win 7+ | Ja | |
| **Roaming-gebruikersprofiel (RUP), onderhoudsmodus** | Nee | Ja | Ja | Ja | Ja| Nee | Ja | Win 7+ | Nee | |
| **Roaming van enterprise state (ESR)** | Ja | Nee | Ja | Nee | Zie notities | Ja | Nee | Win 10 | Nee | Functies op server SKU, maar geen ondersteunende gebruikersinterface |
| **Personalisatie van gebruikerservaring (UE-V)** | Ja | Ja | Ja | Nee | Ja | Nee | Ja | Win 7+ | Nee |  |
| **OneDrive-cloudbestanden** | Nee | Nee | Nee | Ja | Zie notities | Zie notities  | Zie Notities | Win 10 RS3 | Nee | Niet getest op server SKU. Back-endopslag op Azure is afhankelijk van de synchronisatieclient. Back-end opslag on-prem heeft een synchronisatieclient nodig. |

#### <a name="performance"></a>Prestaties

UPD vereist [Storage Spaces Direct (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) om aan de prestatievereisten te voldoen. UPD maakt gebruik van het SMB-protocol (Server Message Block). Het kopieert het profiel naar de VM waarin de gebruiker wordt geregistreerd. UPD met S2D is de oplossing die we aanbevelen voor Windows Virtual Desktop.  

#### <a name="cost"></a>Kosten

Hoewel S2D-clusters de nodige prestaties leveren, zijn de kosten duur voor zakelijke klanten, maar vooral duur voor kleine en middelgrote (MKB)-klanten. Voor deze oplossing betalen bedrijven voor opslagschijven, samen met de kosten van de VM's die de schijven gebruiken voor een aandeel.

#### <a name="administrative-overhead"></a>Meer beheertaken

S2D-clusters vereisen een besturingssysteem dat is gepatcht, bijgewerkt en in een veilige staat wordt onderhouden. Deze processen en de complexiteit van het opzetten van S2D disaster recovery maken S2D alleen haalbaar voor bedrijven met een toegewijde IT-staf.

## <a name="fslogix-profile-containers"></a>FSLogix profielcontainers

Op 19 november 2018 [heeft Microsoft FSLogix overgenomen.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) FSLogix pakt veel uitdagingen op het gebied van profielcontainers aan. Sleutel onder hen zijn:

- **Prestaties:** De [FSLogix-profielcontainers](/fslogix/configure-profile-container-tutorial/) zijn hoge prestaties en lossen prestatieproblemen op die in het verleden de exchange-modus in de cache hebben geblokkeerd.
- **OneDrive:** Zonder FSLogix-profielcontainers wordt OneDrive for Business niet ondersteund in niet-persistente RDSH- of VDI-omgevingen. [Met de best practices van OneDrive voor Bedrijven en FSLogix](/fslogix/overview/) worden de manier waarop ze met elkaar omgaan beschreven. Zie [De synchronisatieclient gebruiken op virtuele bureaubladen](/deployoffice/rds-onedrive-business-vdi/)voor meer informatie .
- **Aanvullende mappen:** FSLogix biedt de mogelijkheid om gebruikersprofielen uit te breiden met extra mappen.

Sinds de overname is Microsoft begonnen met het vervangen van bestaande gebruikersprofieloplossingen, zoals UPD, door FSLogix profielcontainers.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure-bestanden-integratie met Azure Active Directory Domain Service

De prestaties en functies van FSLogix-profielcontainers maken gebruik van de cloud. Op 7 augustus 2019 heeft Microsoft Azure Files de algemene beschikbaarheid van [Azure Files-verificatie aangekondigd met Azure Active Directory Domain Service (AD DS).](../storage/files/storage-files-active-directory-overview.md) Door zowel kosten als administratieve overhead aan te pakken, is Azure Files met Azure AD DS-verificatie een premium oplossing voor gebruikersprofielen in de Windows Virtual Desktop-service.

## <a name="best-practices-for-windows-virtual-desktop"></a>Aanbevolen procedures voor Windows Virtual Desktop

Windows Virtual Desktop biedt volledige controle over de grootte, het type en het aantal VM's die door klanten worden gebruikt. Zie Wat is Windows Virtual Desktop voor meer [informatie?](overview.md).

Ga als volgt om ervoor te zorgen dat uw Windows Virtual Desktop-omgeving de beste procedures volgt:

- Azure Files-opslagaccount moet zich in dezelfde regio bevinden als de VM's voor sessiehost.
- Azure Files-machtigingen moeten overeenkomen met machtigingen die zijn beschreven in [Vereisten - Profielcontainers](/fslogix/fslogix-storage-config-ht).
- Elke hostgroep moet worden gebouwd van hetzelfde type en dezelfde grootte VM op basis van dezelfde hoofdafbeelding.
- Elke vm van de hostgroep moet zich in dezelfde resourcegroep bevindt om het beheer, de schaling en het bijwerken te ondersteunen.
- Voor optimale prestaties moeten de opslagoplossing en de FSLogix-profielcontainer zich op dezelfde datacenterlocatie bevinden.
- Het opslagaccount met de hoofdafbeelding moet zich in dezelfde regio bevinden en een abonnement waarbij de VM's worden ingericht.

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende hulplijnen om een Windows Virtual Desktop-omgeving in te stellen.

- Zie [Een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md)als u wilt beginnen met het uitbouwen van uw desktopvirtualisatieoplossing.
- Zie [Een hostgroep](create-host-pools-azure-marketplace.md)maken met Azure Marketplace als u een hostgroep wilt maken in uw Windows Virtual Desktop-tenant.
- Zie Azure Files share instellen als u volledig beheerde bestandsshares in de cloud wilt [instellen.](/azure/storage/files/storage-files-active-directory-enable/)
- Zie [Een profielcontainer maken voor een hostgroep maken met een bestandsshare](create-host-pools-user-profile.md)als u de profielcontainers van FSLogix wilt configureren.
- Zie [App-groepen voor Windows Virtueel bureaublad](manage-app-groups.md)beheren als u gebruikers wilt toewijzen aan een hostgroep.
- Zie Verbinding maken met [Windows Virtual Desktop](connect-web.md)voor toegang tot uw Windows Virtual Desktop-bronnen vanuit een webbrowser.
