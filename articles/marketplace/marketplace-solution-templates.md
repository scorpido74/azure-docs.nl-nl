---
title: Publicatiehandleiding voor Azure-toepassingenoplossingbieden publicatiegids | Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een oplossingssjabloon in de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084852"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure-toepassingen: oplossingssjabloon biedt publicatievereisten

In dit artikel worden de vereisten voor het type oplossingssjabloonaanbieding uitgelegd, wat een manier is om een Azure-toepassingsaanbieding in de Azure Marketplace te publiceren. Voor het type oplossingssjabloonmoet een [ARM-sjabloon (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) worden gebruikt om uw oplossingsinfrastructuur automatisch te implementeren.

Gebruik het aanbiedingstype azure-toepassingsoplossingssjabloon wanneer de volgende voorwaarden vereist zijn:

- Uw oplossing vereist extra implementatie- en configuratieautomatisering buiten één VM, zoals een combinatie van VM's, netwerk- en opslagbronnen.
- Uw klant gaat de oplossing zelf beheren.

De call-to-action die een gebruiker ziet voor dit aanbiedingstype is 'Nu worden ontvangen'.

## <a name="requirements-for-solution-template-offers"></a>Vereisten voor oplossingssjabloonaanbiedingen

| **Vereisten** | **Details**  |
| ---------------  | -----------  |
|Facturering en meting    |  Aanbiedingen voor oplossingssjablonen zijn geen transacties, maar kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via de commerciële marktplaats van Microsoft. De resources die de ARM-sjabloon van de oplossing implementeert, worden ingericht in het Azure-abonnement van de klant. Pay-as-you-go (PAYGO) virtuele machines worden afgehandeld met de klant via Microsoft, gefactureerd via het Azure-abonnement van de klant.<br/> In het geval van bring-your-own-license (BYOL), terwijl Microsoft de infrastructuurkosten voor het klantabonnement in rekening brengt, voert u uw softwarelicentiekosten rechtstreeks uit aan de klant.   |
|Azure-compatibele virtuele harde schijf (VHD)  |   VM's moeten worden gebouwd op Windows of Linux. Zie voor meer informatie: <ul> <li>[Een Azure-toepassingsaanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md)(voor Windows VHD's)</li><li>[Linux-distributies goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (voor Linux VHDs).</li></ul> |
| Naamsvermelding klantgebruik | Het inschakelen van toeschrijving van klantgebruik is vereist voor alle oplossingssjablonen die zijn gepubliceerd in de Azure Marketplace. Zie toeschrijving van [azure-partnergebruikers](./azure-partner-customer-usage-attribution.md)voor meer informatie over de attributie van klantgebruik en hoe u deze inschakelen.  |
| Managed Disks gebruiken | [Beheerde schijven](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) is de standaardoptie voor volgehouden schijven van IaaS VM's in Azure. U moet Beheerde schijven gebruiken in oplossingssjablonen. <br> <br> 1. Volg de [richtlijnen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) en [voorbeelden](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) voor het gebruik van beheerde schijven in de Azure ARM-sjablonen om uw oplossingssjablonen bij te werken. <br> <br> 2. Volg de onderstaande instructies om de onderliggende VHD van de beheerde schijven te importeren naar een opslagaccount om de VHD als afbeelding in de Marketplace te publiceren: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Volgende stappen

- Als u dit nog niet hebt gedaan, [leest u meer](https://azuremarketplace.microsoft.com/sell) over de Azure Marketplace.
- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- [Maak een Azure-toepassingsaanbieding](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.
