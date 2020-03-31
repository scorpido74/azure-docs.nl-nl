---
title: Verbinding maken met Azure Data Lake Storage Gen1 van VNETs | Microsoft Documenten
description: Verbinding maken met Azure Data Lake Storage Gen1 vanuit Azure VNETs
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878865"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Toegang tot Azure Data Lake Storage Gen1 vanuit VM's binnen een Azure VNET
Azure Data Lake Storage Gen1 is een PaaS-service die wordt uitgevoerd op openbare IP-adressen op internet. Elke server die verbinding kan maken met het openbare internet, kan doorgaans ook verbinding maken met Azure Data Lake Storage Gen1-eindpunten. Standaard hebben alle VM's die zich in Azure VNETs bevinden toegang tot internet en hebben ze dus toegang tot Azure Data Lake Storage Gen1. Het is echter mogelijk om VM's in een VNET te configureren om geen toegang tot internet te hebben. Voor dergelijke VM's is de toegang tot Azure Data Lake Storage Gen1 ook beperkt. Het blokkeren van openbare internettoegang voor VM's in Azure VNET's kan worden gedaan met behulp van een van de volgende benaderingen:

* Door netwerkbeveiligingsgroepen (NSG) te configureren
* Door Gebruikersgedefinieerde routes (UDR) te configureren
* Door routes uit te wisselen via BGP (industriestandaard dynamic routing protocol), wanneer ExpressRoute wordt gebruikt, blokkeert dat de toegang tot het internet

In dit artikel leert u hoe u toegang tot Azure Data Lake Storage Gen1 inschakelen vanuit Azure VM's, die zijn beperkt tot toegang tot bronnen met behulp van een van de drie eerder genoemde methoden.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Connectiviteit met Azure Data Lake Storage Gen1 inschakelen vanuit VM's met beperkte connectiviteit
Als u toegang wilt krijgen tot Azure Data Lake Storage Gen1 van dergelijke VM's, moet u deze configureren om toegang te krijgen tot het IP-adres voor het gebied waar het Azure Data Lake Storage Gen1-account beschikbaar is. U de IP-adressen voor uw Data Lake Storage Gen1-accountregio's identificeren door de DNS-namen van uw accounts op te lossen (`<account>.azuredatalakestore.net`). Als u DNS-namen van uw accounts wilt oplossen, u hulpprogramma's zoals **nslookup**gebruiken. Open een opdrachtprompt op uw computer en voer de volgende opdracht uit:

    nslookup mydatastore.azuredatalakestore.net

De uitvoer lijkt op het volgende. De waarde ten opzichte **van de** eigenschap Adres is het IP-adres dat is gekoppeld aan uw Data Lake Storage Gen1-account.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Connectiviteit van VM's beperkt door gebruik te maken van NSG
Wanneer een NSG-regel wordt gebruikt om de toegang tot internet te blokkeren, u een andere NSG maken waarmee toegang tot het IP-adres Data Lake Storage Gen1 kan worden geopend. Zie [Overzicht netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over NSG-regels. Zie [Een netwerkbeveiligingsgroep maken](../virtual-network/tutorial-filter-network-traffic.md)voor instructies over het maken van NSGs.

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Connectiviteit van VM's beperkt door udr of ExpressRoute te gebruiken
Wanneer routes, ofwel UDR's of BGP-uitwisselingsroutes, worden gebruikt om de toegang tot het internet te blokkeren, moet een speciale route worden geconfigureerd zodat VM's in dergelijke subnetten toegang hebben tot Data Lake Storage Gen1-eindpunten. Zie [Overzicht van door de gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie. Zie [UDR's maken in Resourcemanager](../virtual-network/tutorial-create-route-table-powershell.md)voor instructies voor het maken van UDR's.

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Connectiviteit van VM's beperkt inschakelen met ExpressRoute
Wanneer een ExpressRoute-circuit is geconfigureerd, hebben de on-premises servers toegang tot Data Lake Storage Gen1 via openbare peering. Meer informatie over het configureren van ExpressRoute voor openbare peering is beschikbaar op [ExpressRoute VEELGestelde vragen.](../expressroute/expressroute-faqs.md)

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

