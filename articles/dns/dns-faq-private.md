---
title: Veelgestelde vragen over privé-DNS in Azure
description: Lees in dit artikel veelgestelde vragen over Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939427"
---
# <a name="azure-private-dns-faq"></a>Veelgestelde vragen over privé-DNS in Azure

Hieronder vindt u veelgestelde vragen over Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Ondersteunt Azure DNS privédomeinen?

Privédomeinen worden ondersteund met de functie Azure Private DNS-zones. Private DNS-zones zijn alleen oplosbaar vanuit bepaalde virtuele netwerken. Zie voor meer informatie het [overzicht.](private-dns-overview.md)

Zie [Naamomzetting voor VM's en rolinstanties voor](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)informatie over andere interne DNS-opties in Azure.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Werken Azure Private DNS-zones in Azure-regio's?

Ja. Private Zones wordt ondersteund voor DNS-resolutie tussen virtuele netwerken in Azure-regio's. Private Zones werkt zelfs zonder expliciet peering de virtuele netwerken. Alle virtuele netwerken moeten worden gekoppeld aan de private DNS-zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Is connectiviteit met het internet van virtuele netwerken vereist voor privézones?

Nee. Privézones werken samen met virtuele netwerken. U gebruikt ze om domeinen voor virtuele machines of andere bronnen binnen en tussen virtuele netwerken te beheren. Internetverbinding is niet vereist voor naamomzetting.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan dezelfde privézone worden gebruikt voor verschillende virtuele netwerken voor resolutie?

Ja. U een privé-DNS-zone koppelen aan duizenden virtuele netwerken. Zie [Azure DNS-limieten voor](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) meer informatie

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Kan een virtueel netwerk dat tot een ander abonnement behoort, worden gekoppeld aan een privézone?

Ja. U moet schrijfbewerkingstoestemming hebben op de virtuele netwerken en de privé-DNS-zone. De schrijftoestemming kan worden verleend aan verschillende RBAC-rollen. De RBAC-rol Classic Network Contributor heeft bijvoorbeeld schrijfmachtigingen voor virtuele netwerken en private DNS-zones De rol Van de inzender heeft schrijfmachtigingen voor de privé-DNS-zones. Zie [Op rollen gebaseerd toegangscontrole](../role-based-access-control/overview.md)voor meer informatie over RBAC-rollen.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Worden de automatisch geregistreerde DNS-records voor virtuele machines in een privézone automatisch verwijderd wanneer u de virtuele machine verwijdert?

Ja. Als u een virtuele machine verwijdert binnen een gekoppeld virtueel netwerk waarbij automatische registratie is ingeschakeld, worden de geregistreerde records automatisch verwijderd.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan een automatisch geregistreerde virtuele machinerecord in een privézone van een gekoppeld virtueel netwerk handmatig worden verwijderd?

Ja. U de automatisch geregistreerde DNS-records overschrijven met een handmatig gemaakte DNS-record in de zone. De volgende vraag en antwoord hebben betrekking op dit onderwerp.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Wat gebeurt er als ik handmatig een nieuwe DNS-record probeer te maken in een privézone met dezelfde hostnaam als een automatisch geregistreerde bestaande virtuele machine in een gekoppeld virtueel netwerk?

U probeert handmatig een nieuwe DNS-record te maken in een privézone met dezelfde hostnaam als een bestaande, automatisch geregistreerde virtuele machine in een gekoppeld virtueel netwerk. Wanneer u dit doet, overschrijft de nieuwe DNS-record de automatisch geregistreerde virtuele machinerecord. Als u deze handmatig gemaakte DNS-record opnieuw uit de zone probeert te verwijderen, slaagt de verwijdering. De automatische registratie gebeurt opnieuw zolang de virtuele machine nog bestaat en heeft een prive-IP-eigendom aan. De DNS-record wordt automatisch opnieuw gemaakt in de zone.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Wat gebeurt er als we een gekoppeld virtueel netwerk loskoppelen van een privézone? Worden de automatisch geregistreerde virtuele machinerecords uit het virtuele netwerk ook uit de zone verwijderd?

Ja. Als u een gekoppeld virtueel netwerk loskoppelt van een privézone, werkt u de DNS-zone bij om de bijbehorende virtuele netwerkkoppeling te verwijderen. In dit proces worden virtuele machinerecords die automatisch zijn geregistreerd, uit de zone verwijderd.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Wat gebeurt er als we een gekoppeld virtueel netwerk verwijderen dat is gekoppeld aan een privézone? Moeten we de privézone handmatig bijwerken om het virtuele netwerk als gekoppeld virtueel netwerk uit de zone te ontkoppelen?

Nee. Wanneer u een gekoppeld virtueel netwerk verwijdert zonder het eerst los te koppelen van een privézone, slaagt uw verwijderingsbewerking en worden de koppelingen naar de DNS-zone automatisch gewist.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Werkt DNS-resolutie met behulp van de standaard FQDN (internal.cloudapp.net) nog steeds, zelfs wanneer een privézone (bijvoorbeeld private.contoso.com) is gekoppeld aan een virtueel netwerk?

Ja. Privézones vervangen de standaard azure-internal.cloudapp.net-zone niet. Of u nu afhankelijk bent van de door Azure geleverde internal.cloudapp.net of op uw eigen privézone, gebruik de FQDN van de zone waartegen u wilt oplossen.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Wordt het DNS-achtervoegsel op virtuele machines binnen een gekoppeld virtueel netwerk gewijzigd in dat van de privézone?

Nee. Het DNS-achtervoegsel op de virtuele machines in uw gekoppelde virtuele netwerk blijft het standaard achtervoegsel van Azure ("*.internal.cloudapp.net"). U dit DNS-achtervoegsel op uw virtuele machines handmatig wijzigen in dat van de privézone.
Voor richtlijnen voor het wijzigen van dit achtervoegsel verwijzen naar [Dynamische DNS gebruiken om hostnamen te registreren in uw eigen DNS-server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Wat zijn de gebruikslimieten voor Azure DNS Private zones?

Raadpleeg [Azure DNS-limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) voor meer informatie over de gebruikslimieten voor Azure DNS-privézones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Waarom worden mijn bestaande privé-DNS-zones niet weergegeven in nieuwe portalervaring?

Als uw bestaande privé-DNS-zone is gemaakt met behulp van preview-API, moet u deze zones migreren naar een nieuw resourcemodel. Privé-DNS-zones die zijn gemaakt met preview-API, worden niet weergegeven in de nieuwe portalervaring. Zie hieronder voor instructies over het migreren naar een nieuw resourcemodel.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hoe migreer ik mijn bestaande privé-DNS-zones naar het nieuwe model?

We raden u ten zeerste aan om zo snel mogelijk naar het nieuwe resourcemodel te migreren. Legacy resource model zal worden ondersteund, maar verdere functies zullen niet worden ontwikkeld op de top van dit model. In de toekomst zijn we van plan om het af te keuren ten gunste van nieuwe resource model. Zie[migratiehandleiding voor Azure DNS-privézones voor](private-dns-migration-guide.md)richtlijnen voor het migreren van uw bestaande privé-DNS-zones naar een nieuw resourcemodel.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Private DNS](private-dns-overview.md)
