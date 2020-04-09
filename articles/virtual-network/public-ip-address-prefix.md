---
title: Azure Public IP-adresvoorvoegsel | Microsoft Documenten
description: Meer informatie over wat een openbaar IP-adresvoorvoegsel van Azure is en hoe u voorspelbare openbare IP-adressen aan uw bronnen toewijzen.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: anavin
ms.openlocfilehash: 9604d5567e74ef470c3e798b6a3df9e48d6fde5d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985300"
---
# <a name="public-ip-address-prefix"></a>Voorvoegsel van openbaar IP-adres

Een openbaar IP-adresvoorvoegsel is een gereserveerd bereik van IP-adressen voor uw openbare eindpunten in Azure. Azure kent een aaneengesloten reeks adressen toe aan uw abonnement op basis van het aantal dat u opgeeft. Zie [Openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) als u niet bekend bent met openbare adressen.

Openbare IP-adressen worden toegewezen vanuit een groep adressen in elke Azure-regio. U de lijst met bereiken [downloaden](https://www.microsoft.com/download/details.aspx?id=56519) die Azure voor elke regio gebruikt. 40.121.0.0/16 is bijvoorbeeld een van de meer dan 100 bereiken die Azure gebruikt in de regio Oost-VS. Het assortiment omvat de bruikbare adressen van 40.121.0.1 - 40.121.255.254.

U maakt een openbaar IP-adresvoorvoegsel in een Azure-regio en -abonnement door een naam op te geven en hoeveel adressen het voorvoegsel moet bevatten. Als u bijvoorbeeld een openbaar IP-adresvoorvoegsel van /28 maakt, wijst Azure 16 adressen uit een van de bereiken voor u toe. U weet niet welk bereik Azure toewijst totdat u het bereik hebt gemaakt, maar de adressen zijn aaneengesloten. Openbare IP-adres voorvoegsels hebben een vergoeding. Zie voor meer informatie [de prijzen van openbare IP-adres .](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="why-create-a-public-ip-address-prefix"></a>Waarom een openbaar IP-adresvoorvoegsel maken?

Wanneer u openbare IP-adresbronnen maakt, kent Azure een beschikbaar openbaar IP-adres toe uit een van de bereiken die in de regio worden gebruikt. Zodra Azure het adres toewijst, weet u wat het adres is, maar totdat Azure het adres toewijst, weet u niet welk adres mogelijk wordt toegewezen. Dit kan problematisch zijn wanneer u, bijvoorbeeld, of uw zakelijke partners, firewallregels instellen die specifieke IP-adressen toestaan. Elke keer dat u een nieuw openbaar IP-adres aan een resource toewijst, moet het adres worden toegevoegd aan de firewallregel. Wanneer u adressen aan uw resources toewijst vanuit een openbaar IP-adresvoorvoegsel, hoeven firewallregels niet elke keer dat u een van de adressen toewijst, te worden bijgewerkt, omdat het hele bereik aan een regel kan worden toegevoegd.

## <a name="benefits"></a>Voordelen

- U openbare IP-adresbronnen maken uit een bekend bereik.
- U of uw zakelijke partners kunnen firewallregels maken met bereiken die openbare IP-adressen bevatten die u momenteel hebt toegewezen, evenals adressen die u nog niet hebt toegewezen. Dit elimineert de noodzaak om firewallregels te wijzigen wanneer u IP-adressen toewijst aan nieuwe bronnen.
- De standaardgrootte van een bereik dat u maken, is /28 of 16 IP-adressen.
- Er zijn geen grenzen aan het aantal bereiken dat u maken, maar er zijn beperkingen op het maximum aantal statische openbare IP-adressen dat u hebben in een Azure-abonnement. Als gevolg hiervan kan het aantal bereiken dat u maakt niet meer statische openbare IP-adressen omvatten dan u in uw abonnement hebben. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie .
- De adressen die u maakt met adressen uit het voorvoegsel, kunnen worden toegewezen aan elke Azure-bron waaraan u een openbaar IP-adres toewijzen.
- U eenvoudig zien welke IP-adressen die zijn toegewezen en nog niet binnen het bereik zijn toegewezen.

## <a name="scenarios"></a>Scenario's
U de volgende bronnen koppelen aan een statisch openbaar IP-adres van een voorvoegsel:

|Resource|Scenario|Stappen|
|---|---|---|
|Virtuele machines| Het koppelen van openbare IP's van een voorvoegsel aan uw virtuele machines in Azure vermindert de overhead van het beheer als het gaat om het whitelisten van IP's in een firewall. U een volledig voorvoegsel eenvoudig op de witte lijst zetten met één firewallregel. Terwijl u schaalt met virtuele machines in Azure, u IP's van hetzelfde voorvoegsel koppelen dat kosten, tijd en beheeroverhead bespaart.| Ip-knop van een voorvoegsel koppelen aan uw virtuele machine: 1. [Maak een voorvoegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP van het voorvoegsel.](manage-public-ip-address-prefix.md) 3. [Koppel het IP aan de netwerkinterface van uw virtuele machine.](virtual-network-network-interface-addresses.md#add-ip-addresses) U [de IP's ook koppelen aan een virtuele machineschaalset.](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)
| Standaard load balancers | Het koppelen van openbare IP-ups aan uw ip-configuratie of uitgaande regel van een Load Balancer zorgt voor vereenvoudiging van uw openbare IP-adresruimte in Azure. U uw scenario vereenvoudigen door uitgaande verbindingen te verzorgen die moeten worden afgeleid uit een reeks aaneengesloten IP-adressen die zijn gedefinieerd door een openbaar IP-voorvoegsel. | Ip-effect koppelen aan je load balancer: 1. [Maak een voorvoegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP van het voorvoegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u de load balancer maakt, selecteert of werkt u het IP-adres dat in stap 2 hierboven is gemaakt, als het ip-adres van uw load balancer. |
| Azure Firewall | U een openbaar IP-adres gebruiken van een voorvoegsel voor uitgaande SNAT. Dit betekent dat al het uitgaande virtuele netwerkverkeer wordt vertaald naar het openbare IP-adres [van Azure Firewall.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Aangezien dit IP afkomstig is van een vooraf bepaald voorvoegsel, is het heel gemakkelijk om van tevoren te weten hoe uw openbare IP-voetafdruk in Azure eruit zal zien. | 1. [Maak een voorvoegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP van het voorvoegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u [de Azure-firewall implementeert,](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)moet u het IP-adres selecteren dat u eerder vanuit het voorvoegsel hebt toegewezen.|
| Application Gateway v2 | U een openbaar IP-adres gebruiken van een voorvoegsel voor uw automatische schaling en zoneredundante toepassingsgateway v2. Aangezien dit IP afkomstig is van een vooraf bepaald voorvoegsel, is het heel gemakkelijk om van tevoren te weten hoe uw openbare IP-voetafdruk in Azure eruit zal zien. | 1. [Maak een voorvoegsel.](manage-public-ip-address-prefix.md) 2. [Maak een IP van het voorvoegsel.](manage-public-ip-address-prefix.md) 3. Wanneer u [de toepassingsgateway implementeert,](../application-gateway/quick-create-portal.md#create-an-application-gateway)moet u het IP selecteren dat u eerder vanuit het voorvoegsel hebt toegewezen.|

## <a name="constraints"></a>Beperkingen

- U de IP-adressen voor het voorvoegsel niet opgeven. Azure wijst de IP-adressen voor het voorvoegsel toe op basis van de grootte die u opgeeft.
- U standaard een voorvoegsel van maximaal 16 IP-adressen of een /28 maken. [Netwerklimieten controleren verhogen aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) en [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.
- U het bereik niet wijzigen nadat u het voorvoegsel hebt gemaakt.
- Alleen statische openbare IP-adressen die met de Standaard SKU zijn gemaakt, kunnen worden toegewezen aan het bereik van het voorvoegsel. Zie openbaar IP-adres voor meer informatie over sKU's voor openbare [IP-adres.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- Adressen uit het bereik kunnen alleen worden toegewezen aan Azure Resource Manager-resources. Adressen kunnen niet worden toegewezen aan resources in het klassieke implementatiemodel.
- Alle openbare IP-adressen die met het voorvoegsel zijn gemaakt, moeten in hetzelfde Azure-gebied en -abonnement als het voorvoegsel bestaan en moeten worden toegewezen aan resources in dezelfde regio en hetzelfde abonnement.
- U een voorvoegsel niet verwijderen als er adressen in het voorvoegsel zijn toegewezen aan openbare IP-adresbronnen die zijn gekoppeld aan een bron. Distantiëren van alle openbare IP-adresbronnen die ip-adressen eerst van het voorvoegsel toegewezen krijgen.


## <a name="next-steps"></a>Volgende stappen

- [Een](manage-public-ip-address-prefix.md) openbaar IP-adresvoorvoegsel maken
