---
title: Veelvoorkomende problemen tijdens VHD-creatie (FAQ) voor de Azure Marketplace
description: Veelgestelde vragen over VHD-creatie en bijbehorende problemen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 192fea4101fb972ec66bb5f21a2a83f9903f0855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278106"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Veelvoorkomende problemen tijdens VHD-creatie (FAQ)

De volgende veelgestelde vragen (FAQ) hebben betrekking op veelvoorkomende problemen die zich voordoen tijdens virtual hard disk (VHD) en virtual machine (VM) creatie voor VM-aanbiedingen. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hoe maakt u een VM van de Azure-portal met de VHD die wordt geüpload naar premium opslag?

Azure Marketplace biedt momenteel geen ondersteuning voor het maken van VM-aanbiedingen van afbeeldingen die zich bevinden op beheerde opslag of vanuit Azure Premium Storage.  Zie Overzicht van [Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)voor meer informatie over deze opslagopties.


## <a name="can-you-use-generation-2-vms-for-offers"></a>u generatie 2 VM's gebruiken voor aanbiedingen?

Nee, alleen generatie 1 VHD's worden ondersteund.  We werken momenteel echter samen met het Microsoft Azure Platform Team om ondersteuning voor generatie 2 VM's te onderzoeken.  Zie Moet ik een [generatie 1 of 2 virtuele machine maken in Hyper-V voor](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) meer informatie over de verschillen?


## <a name="how-do-you-change-the-name-of-the-host"></a>Hoe verander je de naam van de gastheer?

Dat kan je niet.  Zodra VM is gemaakt, kunnen gebruikers (inclusief eigenaren) de naam van de host niet bijwerken.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hoe u de Extern bureaublad-service of het aanmeldingswachtwoord opnieuw instellen?

In de volgende artikelen wordt uitgelegd hoe u RDS-resets uitvoert voor Vm's op basis van Windows en Linux:   

- [De service Extern bureaublad of het bijbehorende aanmeldingswachtwoord opnieuw instellen in een virtuele Windows-machine](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Een Linux VM-wachtwoord of SSH-sleutel opnieuw instellen, de SSH-configuratie herstellen en de consistentie van de schijf controleren met de VMAccess-extensie](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hoe genereert u nieuwe SSH-certificaten?

Het genereren van certificaten wordt uitgelegd in het artikel [Get shared access signature URI for your VM image](./cpp-get-sas-uri.md) in the subsequent section Create technical assets for a VM [offer](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe configureer je een vpn (virtual private network) om met mijn VM's te werken?

Als u het azure resource manager-implementatiemodel gebruikt, hebt u drie algemene opties voor het instellen van een VPN:
- [Een op route gebaseerde VPN-gateway maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Een op route gebaseerde VPN-gateway maken met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Een op route gebaseerde VPN-gateway maken met CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat zijn ondersteuningsbeleidsregels van Microsoft voor het uitvoeren van Microsoft-serversoftware op Vm's op Azure?

Deze ondersteuningsbeleidsregels worden beschreven in het artikel [Microsoft-serversoftwareondersteuning voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Zijn er unieke id's aan gekoppeld?

Ja, als deze wordt gehost op Azure.  Azure kent een unieke id toe, genaamd de Azure Virtual Machine Unique ID, aan elke nieuwe VM-bron die wordt gemaakt.  Lees voor meer informatie het blogbericht [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  U deze id ook programmatisch verkrijgen via de [List API.](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe beheert u in een VM de aangepaste scriptextensie in de opstarttaak?

In het volgende artikel wordt uitgelegd hoe u de aangepaste scriptextensie gebruiken met de Azure PowerShell-module, Azure Resource Manager-sjablonen en stappen voor het oplossen van problemen met Windows-systemen: [Aangepaste scriptextensie voor Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in de Azure Marketplace?

In het algemeen niet.  De ondersteunde besturingssystemen en standaardservices voor Azure VM's zijn allemaal 64-bits.  Vanuit technisch oogpunt ondersteunen de meeste 64-bits besturingssystemen echter het uitvoeren van 32-bits versies van toepassingen voor achterwaartse compatibiliteit.  Het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing wordt echter niet ondersteund en wordt daarom *sterk ontmoedigd.*  In plaats daarvan compileerje uw toepassing opnieuw als een 64-bits project.

Raadpleeg voor meer informatie de volgende artikelen:
- [32-bits toepassingen uitvoeren](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Elke keer als ik probeer om een afbeelding te `.VHD is already registered with image repository as the resource` maken van mijn VHDs, krijg ik de fout in PowerShell. Ik heb geen afbeelding gemaakt voor noch heb ik geen afbeelding met deze naam in Azure. Hoe los ik dit probleem op?

Dit probleem treedt meestal op als de gebruiker een VM heeft ingericht van een VHD met een vergrendeling.  Controleer of er geen VM is toegewezen van deze VHD en probeer de bewerking opnieuw.  Als dit probleem zich blijft voordoen, opent u een ondersteuningsticket, zoals uitgelegd in [Ondersteuning voor Cloud Partner Portal.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal) 

