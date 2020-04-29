---
title: Publicatie handleiding voor de oplossings sjabloon van Azure Applications Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een oplossings sjabloon in azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084852"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure-toepassingen: oplossings sjabloon bieden publicatie vereisten

In dit artikel worden de vereisten beschreven voor het aanbiedings type voor de oplossings sjabloon, een manier om een Azure-toepassings aanbieding te publiceren in azure Marketplace. Voor het type van de oplossings sjabloon is een [Azure Resource Manager sjabloon (arm-sjabloon)](../azure-resource-manager/templates/overview.md) vereist om uw oplossings infrastructuur automatisch te implementeren.

Gebruik het aanbiedings type van de Azure-toepassings oplossings sjabloon wanneer de volgende voor waarden zijn vereist:

- Voor uw oplossing is extra implementatie-en configuratie automatisering meer nodig dan één virtuele machine, zoals een combi natie van Vm's, netwerken en opslag resources.
- Uw klant gaat zelf de oplossing beheren.

De aanroep van een actie die een gebruiker voor dit aanbiedings type ziet is ' nu ophalen '.

## <a name="requirements-for-solution-template-offers"></a>Vereisten voor oplossingen sjabloon bieden

| **Vereisten** | **Nadere**  |
| ---------------  | -----------  |
|Facturering en meting    |  Oplossingen voor oplossings sjablonen zijn geen Transact-aanbiedingen, maar kunnen worden gebruikt om betaalde VM-aanbiedingen te implementeren die worden gefactureerd via de micro soft Commercial Marketplace. De resources die door de ARM-sjabloon implementaties van de oplossing worden geïmplementeerd, worden ingericht in het Azure-abonnement van de klant. De virtuele machines van betalen per gebruik (PAYGO) worden door micro soft gefactureerd met de klant, in rekening gebracht via het Azure-abonnement van de klant.<br/> In het geval van uw eigen licentie (BYOL), terwijl micro soft de kosten voor de infra structuur factureert die in het abonnement van de klant worden gemaakt, kunt u de kosten voor software licenties rechtstreeks aan de klant door geven.   |
|Azure-compatibele virtuele harde schijf (VHD)  |   Vm's moeten zijn gebouwd op Windows of Linux. Zie voor meer informatie: <ul> <li>[Een Azure-toepassings aanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md)(voor Windows-vhd's)</li><li>[Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (voor Linux-vhd's).</li></ul> |
| Toewijzing van klant gebruik | Het inschakelen van de toewijzing van klant gebruik is vereist voor alle oplossings sjablonen die naar Azure Marketplace worden gepubliceerd. Zie voor meer informatie over het gebruik van de klant en hoe u deze functie inschakelt, de [klant gebruiks toewijzing van Azure-partners](./azure-partner-customer-usage-attribution.md).  |
| Managed Disks gebruiken | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) is de standaard optie voor persistente schijven van IaaS-Vm's in Azure. U moet Managed Disks gebruiken in oplossings sjablonen. <br> <br> 1. Volg de [richt lijnen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) en voor [beelden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) voor het gebruik van Managed DISKS in de Azure arm-sjablonen om uw oplossings sjablonen bij te werken. <br> <br> 2. Volg de onderstaande instructies om de onderliggende VHD van de Managed Disks te importeren in een opslag account voor het publiceren van de VHD als een installatie kopie op Marketplace: <br> <ul> <li> [Zo](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over Azure Marketplace als u dit nog niet hebt gedaan.
- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- [Maak een Azure-toepassings aanbieding](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.
