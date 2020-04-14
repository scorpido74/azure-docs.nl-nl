---
title: Veelvoorkomende problemen tijdens VHD-creatie (FAQ)
description: Veelgestelde vragen over veelvoorkomende problemen bij het maken van een virtuele harde schijf (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266264"
---
# <a name="common-issues-during-vhd-creation"></a>Veelvoorkomende problemen tijdens vhd-creatie

> [!NOTE]
> We verplaatsen het beheer van uw Azure VM-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, u de instructies in [veelvoorkomende problemen tijdens VHD-creatie (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) in Cloud Partner Portal blijven volgen om uw aanbiedingen te beheren.

Deze veelgestelde vragen (FAQ) hebben betrekking op veelvoorkomende problemen die u tegenkomen bij het maken van een virtuele harde schijf (VHD) voor uw Azure Virtual Machine-aanbieding.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Hoe maak ik een VM van de Azure-portal met een VHD in premium opslag?

Azure Marketplace biedt momenteel geen ondersteuning voor het maken van VM-aanbiedingen van afbeeldingen op beheerde opslag of vanuit Azure Premium Storage. Zie Overzicht [van Azure Managed Disks voor](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)meer informatie.

## <a name="can-i-use-generation-2-vms-for-offers"></a>Kan ik Generatie 2 VM's gebruiken voor aanbiedingen?

Nee, alleen Generatie 1 VHD's worden ondersteund. We werken momenteel echter samen met het Microsoft Azure Platform Team om ondersteuning voor Generatie 2 VM's te onderzoeken. Zie Moet ik voor meer informatie [een generatie 1 of 2 virtuele machine maken in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Hoe wijzig ik de naam van de verhuurder?

Dat kan je niet. Nadat een vm is gemaakt, kunnen gebruikers (inclusief eigenaren) de hostnaam niet bijwerken.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hoe kan ik de Extern bureaublad-service of het aanmeldingswachtwoord opnieuw instellen?

In deze artikelen wordt uitgelegd hoe u RDS-resets uitvoert voor Vm's op basis van Windows en Linux:

* [De service Extern bureaublad of het bijbehorende aanmeldingswachtwoord opnieuw instellen in een virtuele Windows-machine](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Een Linux VM-wachtwoord of SSH-sleutel opnieuw instellen, de SSH-configuratie herstellen en de consistentie van de schijf controleren met de VMAccess-extensie](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Hoe genereer ik nieuwe SSH-certificaten?

Het genereren van certificaten wordt uitgelegd in [Azure VM-afbeeldingscertificering](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe configureer ik een VPN (virtual private network) om met mijn VM's te werken?

Als u het implementatiemodel azure resource manager gebruikt, hebt u drie opties:

* [Een op route gebaseerde VPN-gateway maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Een op route gebaseerde VPN-gateway maken met Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Een op route gebaseerde VPN-gateway maken met CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat zijn ondersteuningsbeleidsregels van Microsoft voor het uitvoeren van Microsoft-serversoftware op Vm's op Azure?

U vindt details bij [Microsoft server software support voor Microsoft Azure virtuele machines.](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Zijn er unieke id's aan gekoppeld?

Ja, als deze wordt gehost op Azure. Azure kent een unieke id, genaamd de [Azure Virtual Machine Unique ID,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)toe aan elke nieuwe VM-bron die is gemaakt. Zie Azure Virtual Machine Unique ID voor meer informatie. U deze id ook krijgen via de [List API.](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe beheer ik in een VM de aangepaste scriptextensie in de opstarttaak?

Zie [Custom Script Extension for Windows voor](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)meer informatie over het gebruik van de Aangepaste Script-extensie met de Azure PowerShell-module, Azure Resource Manager-sjablonen en stappen voor het oplossen van problemen op Windows-systemen.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in Azure Marketplace?

In het algemeen niet. De ondersteunde besturingssystemen en standaardservices voor Azure VM's zijn allemaal 64-bits. Hoewel de meeste 64-bits besturingssystemen 32-bits versies van toepassingen ondersteunen voor achterwaartse compatibiliteit, wordt het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing niet ondersteund en sterk ontmoedigd. Maak uw toepassing opnieuw als een 64-bits project.

Raadpleeg voor meer informatie de volgende artikelen:

* [32-bits toepassingen uitvoeren](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fout: VHD is al geregistreerd bij image repository als de bron

Elke keer als ik probeer een afbeelding van mijn VHD's te maken, krijg ik de foutmelding "VHD is al geregistreerd bij image repository als de bron" in Azure PowerShell. Ik heb geen afbeelding gemaakt voor noch heb ik geen afbeelding met deze naam in Azure. Hoe los ik dit op?

Dit probleem wordt meestal weergegeven als u een VM hebt gemaakt van een VHD met een vergrendeling. Controleer of er geen VM is toegewezen van deze VHD en probeer de bewerking opnieuw. Als dit probleem doorgaat, opent u een ondersteuningsticket. Zie [Ondersteuning voor partnercentrum](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
