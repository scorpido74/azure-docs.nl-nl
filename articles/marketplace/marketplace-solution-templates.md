---
title: Publicatiegids voor azure-toepassingenoplossingssjabloon | Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een oplossingssjabloon in de Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687513"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-toepassingen: publicatiegids voor oplossingensjablonen

Oplossingssjablonen zijn een van de belangrijkste manieren om een oplossing in de Marketplace te publiceren. Gebruik deze handleiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Gebruik de Azure-app: het type oplossingssjabloonaanbieding wanneer uw oplossing extra implementatie- en configuratieautomatisering vereist dan één virtuele machine. U de inrichting van een of meer VM's automatiseren met Azure-apps: oplossingssjablonen. U ook netwerk- en opslagbronnen inrichten. Azure-apps: oplossingssjablonen bieden automatiseringsvoordelen voor afzonderlijke VM's en volledige IaaS-gebaseerde oplossingen.

Deze oplossingssjablonen zijn geen transactieaanbiedingen, maar kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via de commerciële marktplaats van Microsoft. De call-to-action die een gebruiker ziet is 'Nu halen'.


## <a name="requirements-for-solution-templates"></a>Vereisten voor oplossingssjablonen

| **Vereisten** | **Details**  |
| ---------------  | -----------  |
|Facturering en meting    |  De resources worden ingericht in het Azure-abonnement van de klant. Pay-as-you-go (PAYGO) virtuele machines worden afgehandeld met de klant via Microsoft, gefactureerd via het Azure-abonnement (PAYGO) van de klant.  <br/> In het geval van bring-your-own-license (BYOL), terwijl Microsoft de infrastructuurkosten voor het klantabonnement in rekening brengt, voert u uw softwarelicentiekosten rechtstreeks uit aan de klant.   |
|Azure-compatibele virtuele harde schijf (VHD)  |   VM's moeten worden gebouwd op Windows of Linux.  Zie [Een Azure-toepassingsaanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md)voor meer informatie. |
| Naamsvermelding klantgebruik | Het inschakelen van toeschrijving van klantgebruik is vereist voor alle oplossingssjablonen die zijn gepubliceerd in de Azure Marketplace. Zie toeschrijving van [azure-partnergebruikers](./azure-partner-customer-usage-attribution.md)voor meer informatie over de attributie van klantgebruik en hoe u deze inschakelen.  |
| Managed Disks gebruiken | [Beheerde schijven](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) is de standaardoptie voor volgehouden schijven van IaaS VM's in Azure. U moet Beheerde schijven gebruiken in oplossingssjablonen. <br> <br> 1. Volg de [richtlijnen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) en [voorbeelden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) voor het gebruik van beheerde schijven in de Azure ARM-sjablonen om uw oplossingssjablonen bij te werken. <br> <br> 2. Volg de onderstaande instructies om de onderliggende VHD van de beheerde schijven te importeren in een opslagaccount om de VHD als afbeelding in de Marketplace te publiceren: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u zich wilt registreren in partnercentrum, begint u met het maken van een nieuwe aanbieding of werkt u aan een bestaand aanbieding:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- Zie [een Azure-toepassingsaanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.

