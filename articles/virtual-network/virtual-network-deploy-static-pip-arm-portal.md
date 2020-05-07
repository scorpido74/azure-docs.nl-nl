---
title: Een virtuele machine maken met een statisch openbaar IP-adres-Azure Portal | Microsoft Docs
description: Meer informatie over het maken van een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 3ef96c83b460346ba49051f6f5c4fd0dd5df34ab
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790164"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Maak een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure Portal

U kunt een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres kunt u via internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres in plaats van een dynamisch adres toe om ervoor te zorgen dat het adres nooit wordt gewijzigd. Meer informatie over [statische open bare IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over het wijzigen van een openbaar IP-adres dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch of voor gebruik van privé-IP-adressen. Open bare IP-adressen hebben een [nominale belasting](https://azure.microsoft.com/pricing/details/ip-addresses)en er geldt een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor het aantal open bare IP-adressen dat u per abonnement kunt gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute**en selecteer vervolgens **Windows Server 2016 VM**of een ander besturings systeem van uw keuze.
3. Voer de volgende informatie in of Selecteer deze, accepteer de standaard waarden voor de overige instellingen en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVM|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Locatie| Selecteer **VS - oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Selecteer onder **instellingen**het **open bare IP-adres**.
6. Voer *myPublicIpAddress*in, selecteer **static**en selecteer **OK**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Statisch selecteren](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Als het open bare IP-adres een standaard-SKU moet zijn, selecteert u **Standard** onder **SKU**. Meer informatie over [open bare IP-adres-sku's](virtual-network-ip-addresses-overview-arm.md#sku). Als de virtuele machine wordt toegevoegd aan de back-end-groep van een openbaar Azure Load Balancer, moet de SKU van het open bare IP-adres van de virtuele machine overeenkomen met de SKU van het open bare IP-adres van de load balancer. Zie [Azure Load Balancer](../load-balancer/skus.md)voor meer informatie.

6. Selecteer een poort of geen poorten onder **Selecteer open bare binnenkomende poorten**. Portal 3389 is geselecteerd om externe toegang tot de virtuele Windows Server-machine via internet in te scha kelen. Het openen van poort 3389 van Internet wordt niet aanbevolen voor productie werkbelastingen.

   ![Poort selecteren](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Accepteer de overige standaard instellingen en selecteer **OK**.
8. Op de pagina**Overzicht** selecteert u **Maken**. De implementatie van de virtuele machine duurt een paar minuten.
9. Wanneer de virtuele machine is geïmplementeerd, voert u *myPublicIpAddress* in het zoekvak boven aan de portal in. Wanneer **myPublicIpAddress** wordt weer gegeven in de zoek resultaten, selecteert u dit.
10. U kunt het open bare IP-adres weer geven dat is toegewezen en dat het adres is toegewezen aan de virtuele **myVM** -machine, zoals wordt weer gegeven in de volgende afbeelding:

    ![Openbaar IP-adres weer geven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure heeft een openbaar IP-adres toegewezen op basis van adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Selecteer **configuratie** om te bevestigen dat de toewijzing **statisch**is.

    ![Openbaar IP-adres weer geven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Wijzig de IP-adres instellingen in het besturings systeem van de virtuele machine niet. Het besturings systeem is niet op de hoogte van open bare IP-adressen van Azure. Hoewel u persoonlijke IP-adres instellingen kunt toevoegen aan het besturings systeem, wordt aangeraden dit niet te doen, tenzij dit nodig is, en pas na het lezen van [een privé-IP-adres toevoegen aan een besturings systeem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in bij **Typ de naam van de resource groep:** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [open bare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in azure
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over het maken van virtuele [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machines