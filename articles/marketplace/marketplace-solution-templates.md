---
title: Publicatie handleiding voor de oplossings sjabloon van Azure Applications Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een oplossings sjabloon in azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 725be2ee239a879be8200d33acaf566b1d42d446
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300341"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-toepassingen: Publicatie handleiding aanbieding voor oplossings sjabloon

Oplossings sjablonen zijn een van de belangrijkste manieren om een oplossing op Marketplace te publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Gebruik het aanbiedings type voor de Azure-app: oplossings sjabloon wanneer voor uw oplossing extra implementatie-en configuratie automatisering is vereist dan één virtuele machine. U kunt het inrichten van een of meer Vm's automatiseren met Azure-apps: oplossings sjablonen. U kunt ook netwerken en opslag resources inrichten. Azure-apps: aanbod type voor oplossingen biedt automatiserings voordelen voor enkele Vm's en volledige oplossingen op basis van IaaS.

Deze oplossings sjablonen zijn geen transactie aanbiedingen, maar kunnen worden gebruikt om betaalde VM-aanbiedingen te implementeren die worden gefactureerd via de commerciële Marketplace van micro soft. De aanroep van een actie die een gebruiker ziet is ' nu ophalen '.


## <a name="requirements-for-solution-templates"></a>Vereisten voor oplossings sjablonen

| **Vereisten** | **Details**  |
| ---------------  | -----------  |
|Facturering en meting    |  De resources worden ingericht in het Azure-abonnement van de klant. PAYGO-virtuele machines (betalen per gebruik) worden met de klant in rekening gebracht via micro soft, gefactureerd via het Azure-abonnement van de klant (PAYGO).  <br/> In het geval van uw eigen licentie (BYOL), terwijl micro soft de kosten voor de infra structuur factureert die in het abonnement van de klant worden gemaakt, kunt u de kosten voor software licenties rechtstreeks aan de klant door geven.   |
|Azure-compatibele virtuele harde schijf (VHD)  |   Vm's moeten zijn gebouwd op Windows of Linux.  [Zie een virtuele harde schijf maken die compatibel is met Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)voor meer informatie. |
| Toewijzing van klant gebruik | Het inschakelen van de toewijzing van klant gebruik is vereist voor alle oplossings sjablonen die naar Azure Marketplace worden gepubliceerd. Zie voor meer informatie over het gebruik van de klant en hoe u deze functie inschakelt, de [klant gebruiks toewijzing van Azure-partners](./azure-partner-customer-usage-attribution.md).  |
| Beheerde schijven gebruiken | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) is de standaard optie voor persistente schijven van IaaS-Vm's in Azure. U moet Managed Disks gebruiken in oplossings sjablonen. <br> <br> 1. Volg de [richt lijnen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) en voor [beelden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) voor het gebruik van Managed DISKS in de Azure arm-sjablonen om uw oplossings sjablonen bij te werken. <br> <br> 2. Volg de onderstaande instructies om de onderliggende VHD van de Managed Disks te importeren in een opslag account om de VHD als een installatie kopie te publiceren in Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Volgende stappen
Als u dit nog niet hebt gedaan, [registreert u zich](https://azuremarketplace.microsoft.com/sell) in de Marketplace.

Meld u aan bij [Cloud Partner-Portal](https://cloudpartner.azure.com) om uw aanbieding te maken of te volt ooien als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand item werkt.
