---
title: Veelvoorkomende problemen tijdens het maken van de VHD (FAQ)
description: Veelgestelde vragen over veelvoorkomende problemen bij het maken van een virtuele harde schijf (VHD).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: d717d93b616e8acd296c89322b7277cda5ade3a3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323992"
---
# <a name="common-issues-during-vhd-creation"></a>Veelvoorkomende problemen tijdens het maken van VHD

Deze veelgestelde vragen zijn van belang voor veelvoorkomende problemen die kunnen optreden bij het maken van een virtuele harde schijf (VHD) voor uw virtuele machine-aanbieding van Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Hoe kan ik een virtuele machine maken op basis van de Azure Portal met behulp van een VHD in Premium Storage?

Azure Marketplace biedt momenteel geen ondersteuning voor het maken van VM-aanbiedingen van installatie kopieën op beheerde opslag of Azure Premium Storage. Zie [overzicht van Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md)voor meer informatie.

## <a name="can-i-use-generation-2-vms-for-offers"></a>Kan ik virtuele machines van de tweede generatie voor aanbiedingen gebruiken?

Nee, alleen Vhd's van de eerste generatie worden ondersteund. We werken momenteel echter met het Microsoft Azure platform team om ondersteuning te onderzoeken voor Vm's van de tweede generatie. Zie [moet ik een virtuele machine van de eerste of tweede generatie maken in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) voor meer informatie.

## <a name="how-do-i-change-the-name-of-the-host"></a>De naam van de host Hoe kan ik wijzigen?

U kunt dit niet doen. Nadat een virtuele machine is gemaakt, kunnen gebruikers (inclusief eigen aren) de hostnaam niet bijwerken.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>De Extern bureaublad-service of het aanmeldings wachtwoord Hoe kan ik opnieuw instellen?

In deze artikelen wordt uitgelegd hoe u RDS resets uitvoert voor Vm's op basis van Windows en Linux:

* [De service Extern bureaublad of het bijbehorende aanmeldingswachtwoord opnieuw instellen in een virtuele Windows-machine](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Een Linux VM-wacht woord of SSH-sleutel opnieuw instellen, de SSH-configuratie herstellen en schijf consistentie controleren met behulp van de VMAccess-extensie](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Hoe kan ik nieuwe SSH-certificaten genereren?

Het genereren van certificaten wordt uitgelegd in [Azure VM-installatie kopie-certificering](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe kan ik een virtueel particulier netwerk (VPN) configureren om met mijn Vm's te werken?

Als u het Azure Resource Manager-implementatie model gebruikt, hebt u drie opties:

* [Een op een route gebaseerde VPN-gateway maken met behulp van de Azure Portal](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Een op een route gebaseerde VPN-gateway maken met behulp van Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Een op een route gebaseerde VPN-gateway maken met behulp van CLI](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat is micro soft-ondersteunings beleid voor het uitvoeren van micro soft-server software op Vm's op basis van Azure?

Meer informatie vindt u op de [micro soft-server software ondersteuning voor Microsoft Azure virtuele machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Zijn er unieke id's gekoppeld aan virtuele machines?

Ja, indien gehost op Azure. Azure wijst een unieke id, de [unieke id van de virtuele machine van Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), toe aan elke nieuwe VM-resource die wordt gemaakt. Zie de unieke ID van de virtuele machine van Azure voor meer informatie. U kunt deze id ook ophalen via de [List-API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe kan ik in een virtuele machine de aangepaste script extensie in de opstart taak beheren?

Zie [aangepaste script extensie voor Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)voor meer informatie over het gebruik van de aangepaste script extensie met behulp van de module Azure PowerShell, Azure Resource Manager sjablonen en stappen voor probleem oplossing op Windows-systemen.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in azure Marketplace?

In het algemeen niet. De ondersteunde besturings systemen en standaard services voor virtuele Azure-machines zijn allemaal 64 bits. Hoewel de meeste 64-bits besturings systemen 32-bits versies van toepassingen ondersteunen voor achterwaartse compatibiliteit, is het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing niet-ondersteund en sterk afgeraden. Maak uw toepassing opnieuw als een 64-bits project.

Raadpleeg deze artikelen voor meer informatie:

* [Het uitvoeren van 32-bits toepassingen](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fout: VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource

Telkens wanneer ik een installatie kopie van mijn Vhd's probeer te maken, krijg ik de fout ' VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource ' in Azure PowerShell. Ik heb geen installatie kopie gemaakt vóór en ik heb een installatie kopie met deze naam in azure gevonden. Hoe los ik dit op?

Dit probleem wordt doorgaans weer gegeven als u een virtuele machine hebt gemaakt op basis van een virtuele harde schijf met een vergren deling. Controleer of er geen VM is toegewezen van deze VHD en voer de bewerking opnieuw uit. Als dit probleem zich blijft voordoen, opent u een ondersteunings ticket. Zie [ondersteuning voor Partner Center](support.md).
