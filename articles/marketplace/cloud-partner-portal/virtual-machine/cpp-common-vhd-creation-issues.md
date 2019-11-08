---
title: Veelvoorkomende problemen tijdens het maken van de VHD (FAQ) voor Azure Marketplace
description: Veelgestelde vragen over het maken van virtuele harde schijven en gerelateerde problemen.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 1d4224d8f1f735ecef3d4551e3032b916e81dc3c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826615"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Veelvoorkomende problemen tijdens het maken van de VHD (FAQ)

De volgende veelgestelde vragen hebben betrekking op veelvoorkomende problemen tijdens het maken van virtuele harde schijven (VHD) en virtuele machines (VM'S) voor VM-aanbiedingen. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hoe maak ik een VM van de Azure Portal met behulp van de VHD die is geüpload naar Premium Storage?

Azure Marketplace biedt momenteel geen ondersteuning voor het maken van VM-aanbiedingen van installatie kopieën die zich bevinden op beheerde opslag of Azure Premium Storage.  Zie [overzicht van Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)voor meer informatie over deze opslag opties.


## <a name="can-you-use-generation-2-vms-for-offers"></a>Kunt u virtuele machines van generatie 2 gebruiken voor aanbiedingen?

Nee, alleen Vhd's van de eerste generatie worden ondersteund.  We werken momenteel echter met het Microsoft Azure-platform team om ondersteuning te onderzoeken voor Vm's van de tweede generatie.  Zie [moet ik een virtuele machine van de eerste of tweede generatie maken in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) voor meer informatie over de verschillen.


## <a name="how-do-you-change-the-name-of-the-host"></a>Hoe wijzigt u de naam van de host?

U kunt dit niet doen.  Zodra de VM is gemaakt, kunnen gebruikers (inclusief eigen aren) de naam van de host niet bijwerken.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hoe kunt u de Extern bureaublad-service of het wacht woord voor aanmelden opnieuw instellen?

In de volgende artikelen wordt uitgelegd hoe u RDS resets uitvoert voor Vm's op basis van Windows en Linux:   

- [De service Extern bureaublad of het bijbehorende aanmeldingswachtwoord opnieuw instellen in een virtuele Windows-machine](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Een Linux VM-wacht woord of SSH-sleutel opnieuw instellen, de SSH-configuratie herstellen en schijf consistentie controleren met behulp van de VMAccess-extensie](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hoe genereer ik nieuwe SSH-certificaten?

Het genereren van certificaten wordt uitgelegd in het artikel de URI van de [gedeelde toegangs handtekening ophalen voor uw VM-installatie kopie](./cpp-get-sas-uri.md) in de volgende sectie [technische activa maken voor een VM-aanbieding](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe configureer ik een virtueel particulier netwerk (VPN) om met mijn Vm's te werken?

Als u het Azure Resource Manager-implementatie model gebruikt, hebt u drie algemene opties voor het instellen van een VPN:
- [Een op een route gebaseerde VPN-gateway maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Een op een route gebaseerde VPN-gateway maken met behulp van Power shell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Een op een route gebaseerde VPN-gateway maken met behulp van CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat is micro soft-ondersteunings beleid voor het uitvoeren van micro soft-server software op Vm's op basis van Azure?

Dit ondersteunings beleid wordt beschreven in het artikel [micro soft-server software ondersteuning voor Microsoft Azure virtuele machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Zijn er unieke id's gekoppeld aan virtuele machines?

Ja, indien gehost op Azure.  Azure wijst een unieke id toe met de naam de unieke ID van de virtuele Azure-machine aan elke nieuwe VM-resource die wordt gemaakt.  Lees de [unieke id van de Azure virtual machine](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)voor meer informatie.  U kunt deze id ook programmatisch verkrijgen via de [List-API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe beheert u in een virtuele machine de aangepaste script extensie in de opstart taak?

In het volgende artikel wordt beschreven hoe u de aangepaste script extensie gebruikt met behulp van de module Azure PowerShell, Azure Resource Manager sjablonen en Details oplossen van problemen met Windows-systemen: [aangepaste script extensie voor Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in azure Marketplace?

In het algemeen, nee.  De ondersteunde besturings systemen en standaard services voor virtuele Azure-machines zijn allemaal 64 bits.  Vanuit een technisch oogpunt ondersteunen de meeste 64-bits besturings systemen echter de uitvoering van 32-bits versies van toepassingen voor achterwaartse compatibiliteit.  Het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing wordt echter niet ondersteund en wordt daarom *sterk afgeraden*.  Compileer uw toepassing in plaats daarvan als een 64-bits project.

Raadpleeg voor meer informatie de volgende artikelen:
- [Het uitvoeren van 32-bits toepassingen](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Ondersteuning voor 32-bits besturings systemen in azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Telkens wanneer ik een installatie kopie van mijn Vhd's probeer te maken, krijg ik de fout `.VHD is already registered with image repository as the resource` in Power shell. Ik heb geen installatie kopie gemaakt vóór en ik heb een installatie kopie met deze naam in azure gevonden. Hoe kan ik dit probleem oplossen?

Dit probleem treedt meestal op als de gebruiker een virtuele machine heeft ingericht op basis van een VHD waarop deze is vergrendeld.  Controleer of er geen VM is toegewezen van deze VHD en voer de bewerking opnieuw uit.  Als dit probleem zich blijft voordoen, opent u een ondersteunings ticket, zoals wordt beschreven in [ondersteuning voor Cloud Partner-Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

