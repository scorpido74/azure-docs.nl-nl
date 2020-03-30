---
title: Aanvullende netwerkvereisten voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Aanvullende netwerkvereisten voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614570"
---
# <a name="additional-network-requirements-for-large-instances"></a>Aanvullende netwerkvereisten voor grote exemplaren

Mogelijk hebt u aanvullende netwerkvereisten als onderdeel van een implementatie van grote exemplaren van SAP HANA op Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Meer IP-adressen of subnetten toevoegen

Gebruik de Azure-portal, PowerShell of de Azure CLI wanneer u meer IP-adressen of subnetten toevoegt.

Voeg het nieuwe IP-adresbereik toe als een nieuw bereik aan de virtuele netwerkadresruimte, in plaats van een nieuw geaggregeerd bereik te genereren. Dien deze wijziging in bij Microsoft. Zo u vanuit dat nieuwe IP-adresbereik verbinding maken met de HANA-grote instance-eenheden in uw client. U een Azure-ondersteuningsaanvraag openen om de nieuwe virtuele netwerkadresruimte toegevoegd te krijgen. Nadat u een bevestiging hebt ontvangen, voert u de volgende stappen uit.

Zie Een virtueel netwerk maken met [de Azure-portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)als u een extra subnet wilt maken vanuit de Azure-portal. Zie Een virtueel netwerk [maken met PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)als u er een wilt maken met PowerShell.

## <a name="add-virtual-networks"></a>Virtuele netwerken toevoegen

Nadat u in eerste instantie een of meer virtuele Azure-netwerken hebt verbonden, u aanvullende netwerken verbinden die toegang hebben tot SAP HANA op Azure (grote exemplaren). Dien eerst een Azure-ondersteuningsaanvraag in. Neem in dat verzoek de specifieke informatie op die de specifieke Azure-implementatie identificeert. Neem ook het IP-adresruimtebereik of -bereik van de azure-locatieadresruimte voor virtuele netwerken op. SAP HANA op Microsoft Service Management biedt vervolgens de nodige informatie die u nodig hebt om de extra virtuele netwerken en Azure ExpressRoute met elkaar te verbinden. Voor elk virtueel netwerk hebt u een unieke autorisatiesleutel nodig om verbinding te maken met het ExpressRoute-circuit met grote exemplaren van hana.

## <a name="increase-expressroute-circuit-bandwidth"></a>Bandbreedte expressroute-circuit vergroten

Raadpleeg SAP HANA over Microsoft Service Management. Als ze u adviseren om de bandbreedte van het SAP HANA op Azure (large instances) ExpressRoute-circuit te vergroten, maakt u een Azure-ondersteuningsaanvraag. (U een verhoging aanvragen voor een enkele circuitbandbreedte tot maximaal 10 Gbps.) U ontvangt dan een melding nadat de bewerking is voltooid; u hoeft niets anders te doen om deze hogere snelheid in Azure in te schakelen.

## <a name="add-an-additional-expressroute-circuit"></a>Voeg een extra ExpressRoute-circuit toe

Raadpleeg SAP HANA over Microsoft Service Management. Als ze u adviseren om een extra ExpressRoute-circuit toe te voegen, maakt u een Azure-ondersteuningsaanvraag (inclusief een verzoek om autorisatiegegevens te krijgen om verbinding te maken met het nieuwe circuit). Voordat u de aanvraag indient, moet u de adresruimte definiëren die op de virtuele netwerken wordt gebruikt. SAP HANA op Microsoft Service Management kan dan autorisatie verlenen.

Wanneer het nieuwe circuit wordt gemaakt en de SAP HANA op Microsoft Service Management-configuratie is voltooid, ontvangt u een melding met de informatie die u nodig hebt om verder te gaan. U geen virtuele Azure-netwerken verbinden met dit aanvullende circuit als ze al zijn verbonden met een ander SAP HANA op Azure (large instance) ExpressRoute-circuit in dezelfde Azure-regio.

## <a name="delete-a-subnet"></a>Een subnet verwijderen

Als u een subnet voor virtueel netwerk wilt verwijderen, u de Azure-portal, PowerShell of Azure CLI gebruiken. Als uw IP-adresbereik voor een Azure-netwerk of adresruimte een geaggregeerd bereik was, wordt er geen follow-up voor u met Microsoft uitgevoerd. (Houd er echter rekening mee dat het virtuele netwerk nog steeds de adresruimte van de BGP-route voortplant die het verwijderde subnet bevat.) Mogelijk hebt u het azure-virtuele netwerkadresbereik of adresruimte gedefinieerd als meerdere IP-adresbereiken, waarvan er één is toegewezen aan uw verwijderde subnet. Zorg ervoor dat u dat uit uw virtuele netwerkadresruimte verwijdert. Informeer SAP HANA over Microsoft Service Management om het uit de reeksen te verwijderen waarmee SAP HANA op Azure (grote exemplaren) mag communiceren.

Zie [Een subnet verwijderen](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)voor meer informatie .

## <a name="delete-a-virtual-network"></a>Een virtueel netwerk verwijderen

Zie Een [virtueel netwerk verwijderen](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)voor informatie.

SAP HANA op Microsoft Service Management verwijdert de bestaande autorisaties op het SAP HANA op Azure (large instances) ExpressRoute-circuit. Het verwijdert ook het IP-adresbereik van azure's of adresruimte voor de communicatie met HANA-grote exemplaren.

Nadat u het virtuele netwerk hebt verwijderd, opent u een Azure-ondersteuningsverzoek om het IP-adresbereik of de te verwijderen bereiken op te geven.

Verwijder de ExpressRoute-verbinding, de virtuele netwerkgateway, het openbare IP-netwerkgateway en het virtuele netwerk.

## <a name="delete-an-expressroute-circuit"></a>Een ExpressRoute-circuit verwijderen

Als u een extra SAP HANA op azure(large instances) ExpressRoute-circuit wilt verwijderen, opent u een Azure-ondersteuningsaanvraag met SAP HANA op Microsoft Service Management. Verzoek dat het circuit wordt verwijderd. Binnen het Azure-abonnement u het virtuele netwerk verwijderen of behouden, indien nodig. U moet echter de verbinding tussen het HANA-circuit voor grote exemplaren ExpressRoute en de gekoppelde virtuele netwerkgateway verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [SAP HANA (Large Instances) installeren en configureren in Azure](hana-installation.md)
