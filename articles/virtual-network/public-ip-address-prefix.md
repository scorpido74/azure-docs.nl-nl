---
title: Voor voegsel van openbaar IP-adres voor Azure
description: Meer informatie over wat een voor voegsel van een openbaar IP-adres voor Azure is en hoe u het kunt gebruiken om voorspel bare, openbaar IP-adressen aan uw resources toe te wijzen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144509"
---
# <a name="public-ip-address-prefix"></a>Voorvoegsel van openbaar IP-adres

Een voor voegsel van een openbaar IP-adres is een gereserveerd bereik van IP-adressen voor uw open bare eind punten in Azure. Azure wijst een aaneengesloten adres bereik toe aan uw abonnement op basis van het aantal dat u opgeeft. Zie [open bare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) als u niet bekend bent met open bare adressen.

Open bare IP-adressen worden toegewezen uit een groep adressen in elke Azure-regio. U kunt de lijst met bereiken [downloaden](https://www.microsoft.com/download/details.aspx?id=56519) die Azure gebruikt voor elke regio. 40.121.0.0/16 is bijvoorbeeld een van meer dan 100 bereiken die Azure gebruikt in de regio VS-Oost. Het bereik bevat de bruikbare adressen van 40.121.0.1-40.121.255.254.

U maakt een voor voegsel van een openbaar IP-adres in een Azure-regio en-abonnement door een naam op te geven en te bepalen hoeveel adressen het voor voegsel moet bevatten. Als u bijvoorbeeld een openbaar IP-adres voorvoegsel van/28 maakt, wijst Azure 16 adressen van een van de bereiken voor u toe. U weet niet welk bereik Azure er moet worden toegewezen totdat u het bereik maakt, maar de adressen aaneengesloten zijn. Voor voegsels voor open bare IP-adressen gelden kosten. Zie [prijzen voor open bare IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses)voor meer informatie.

## <a name="why-create-a-public-ip-address-prefix"></a>Waarom een openbaar IP-adres voorvoegsel maken?

Wanneer u resources met een openbaar IP-adres maakt, wijst Azure een beschikbaar openbaar IP-adres toe uit een van de bereiken die in de regio worden gebruikt. Zodra het adres door Azure is toegewezen, weet u wat het adres is, maar totdat het adres door Azure wordt toegewezen, weet u niet welk adres kan worden toegewezen. Dit kan problemen opleveren wanneer u bijvoorbeeld of uw zakelijke partners firewall regels instellen waarmee specifieke IP-adressen worden toegestaan. Telkens wanneer u een nieuw openbaar IP-adres toewijst aan een resource, moet het adres worden toegevoegd aan de firewall regel. Wanneer u adressen aan uw resources toewijst vanuit een openbaar IP-adres voorvoegsel, hoeven de firewall regels niet telkens te worden bijgewerkt wanneer u een van de adressen toewijst, omdat het hele bereik kan worden toegevoegd aan een regel.

## <a name="benefits"></a>Voordelen

- U kunt open bare IP-adres bronnen maken op basis van een bekend bereik.
- U of uw zakelijke partners kunnen Firewall regels maken met bereiken die de open bare IP-adressen bevatten die u momenteel hebt toegewezen, evenals de adressen die u nog niet hebt toegewezen. Dit elimineert de nood zaak om firewall regels te wijzigen wanneer u IP-adressen aan nieuwe resources toewijst.
- De standaard grootte van een bereik dat u kunt maken, is/28 of 16 IP-adressen.
- Er zijn geen beperkingen voor het aantal bereiken dat u kunt maken, maar er zijn limieten voor het maximum aantal statische open bare IP-adressen dat u kunt hebben in een Azure-abonnement. Als gevolg hiervan kan het aantal bereiken dat u maakt niet meer statische open bare IP-adressen bevatten dan in uw abonnement. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.
- De adressen die u maakt met behulp van adressen uit het voor voegsel, kunnen worden toegewezen aan elke Azure-resource waaraan u een openbaar IP-adres kunt toewijzen.
- U kunt eenvoudig zien welke IP-adressen er zijn toegewezen en nog niet zijn toegewezen binnen het bereik.

## <a name="scenarios"></a>Scenario's
U kunt de volgende resources koppelen aan een statisch openbaar IP-adres van een voor voegsel:

|Resource|Scenario|Stappen|
|---|---|---|
|Virtuele machines| Het koppelen van open bare Ip's van een voor voegsel aan uw virtuele machines in azure reduceert de beheer overhead wanneer het gaat om white list Ip's in een firewall. U kunt eenvoudig een volledig voor voegsel white list met één firewall regel. Wanneer u schaalt met virtuele machines in azure, kunt u IP-adressen koppelen aan hetzelfde voor voegsel kosten, tijd en beheer overhead besparen.| IP-adressen van een voor voegsel aan uw virtuele machine koppelen: 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) 3. [Koppel het IP-adres aan de netwerk interface van de virtuele machine.](virtual-network-network-interface-addresses.md#add-ip-addresses) U kunt [de IP-adressen ook koppelen aan een schaalset voor virtuele machines](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standaard load balancers | Het koppelen van open bare Ip's van een voor voegsel aan uw frontend-IP-configuratie of de regel voor uitgaande verbindingen van een Load Balancer zorgt voor vereenvoudiging van uw open bare IP-adres ruimte van Azure. U kunt uw scenario vereenvoudigen door het opschonen van uitgaande verbindingen van een bereik van aaneengesloten IP-adressen die zijn gedefinieerd door het open bare IP-voor voegsel. | IP-adressen van een voor voegsel aan uw Load Balancer koppelen: 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u de Load Balancer maakt, selecteert of werkt u het IP-adres dat u in stap 2 hierboven hebt gemaakt, in als het frontend-IP-adres van uw Load Balancer. |
| Azure Firewall | U kunt een openbaar IP-adres gebruiken uit een voor voegsel voor uitgaande SNAT. Dit betekent dat al het uitgaande virtuele netwerk verkeer wordt omgezet naar het [Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) open bare IP-adres. Aangezien dit IP-adres afkomstig is van een vooraf bepaald voor voegsel, is het heel eenvoudig om te weten te komen hoe uw open bare IP-Opper vlak in azure eruitziet. | 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u [de Azure-firewall implementeert](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), moet u ervoor zorgen dat u het IP-adres selecteert dat u eerder van het voor voegsel hebt toegewezen.|
| Application Gateway v2 | U kunt een openbaar IP-adres gebruiken uit een voor voegsel voor uw automatisch schalen en een zone-redundante toepassings gateway v2. Aangezien dit IP-adres afkomstig is van een vooraf bepaald voor voegsel, is het heel eenvoudig om te weten te komen hoe uw open bare IP-Opper vlak in azure eruitziet. | 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u [de Application Gateway implementeert](../application-gateway/quick-create-portal.md#create-an-application-gateway), moet u ervoor zorgen dat u het IP-adres selecteert dat u eerder van het voor voegsel hebt toegewezen.|

## <a name="constraints"></a>Beperkingen

- U kunt de IP-adressen voor het voor voegsel niet opgeven. Azure wijst de IP-adressen voor het voor voegsel toe op basis van de grootte die u opgeeft.
- U kunt standaard een voor voegsel van Maxi maal 16 IP-adressen of een/28 maken. Bekijk de [netwerk limieten verhogen aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) en [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.
- U kunt het bereik niet meer wijzigen als u het voor voegsel hebt gemaakt.
- Alleen statische open bare IP-adressen die zijn gemaakt met de standaard-SKU kunnen worden toegewezen vanuit het bereik van het voor voegsel. Zie [openbaar IP-adres](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)voor meer informatie over open bare IP-adres-sku's.
- Adressen van het bereik kunnen alleen worden toegewezen aan Azure Resource Manager resources. Adressen kunnen niet worden toegewezen aan resources in het klassieke implementatie model.
- Alle open bare IP-adressen die zijn gemaakt op basis van het voor voegsel, moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden als het voor voegsel en moeten worden toegewezen aan resources in dezelfde regio en hetzelfde abonnement.
- U kunt een voor voegsel niet verwijderen als er adressen in de service zijn toegewezen aan open bare IP-adres resources die zijn gekoppeld aan een resource. Koppel alle open bare IP-adres bronnen die IP-adressen aan het voor voegsel zijn toegewezen eerst.


## <a name="next-steps"></a>Volgende stappen

- Een voor voegsel voor een openbaar IP-adres [maken](manage-public-ip-address-prefix.md)
