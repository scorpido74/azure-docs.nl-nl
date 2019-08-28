---
title: Een virtuele machine maken met een statisch openbaar IP-adres - Azure portal | Microsoft Docs
description: Informatie over het maken van een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f6914a9894db07a40b372a8c247a7623c3957d86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692430"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Een virtuele machine maken met een statisch openbaar IP-adres met behulp van de Azure portal

U kunt een virtuele machine maken met een statisch openbaar IP-adres. Een openbaar IP-adres kunt u communiceren met een virtuele machine via internet. Toewijzen van een statisch openbaar IP-adres, in plaats van een dynamisch adres, om ervoor te zorgen dat het adres nooit verandert. Meer informatie over [statische openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Een openbaar IP-adres toegewezen aan een bestaande virtuele machine van dynamisch in statisch of om te werken met particuliere IP-adressen, Zie [toevoegen, wijzigen of verwijderen-IP-adressen](virtual-network-network-interface-addresses.md). Openbare IP-adressen hebben een [nominale kosten in rekening gebracht](https://azure.microsoft.com/pricing/details/ip-addresses), en er is een [limiet](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) aan het aantal openbare IP-adressen die u per abonnement kunt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute**, en selecteer vervolgens **virtuele machine met Windows Server 2016**, of een ander besturingssysteem van uw keuze.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Name|myVM|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Locatie| Selecteer **US - oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Onder **instellingen**, selecteer **openbaar IP-adres**.
6. Voer *myPublicIpAddress*, selecteer **statische**, en selecteer vervolgens **OK**, zoals wordt weergegeven in de volgende afbeelding:

   ![Selecteer statisch](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Als het openbare IP-adres een standaard-SKU moet, selecteert u **Standard** onder **SKU**. Meer informatie over [openbaar IP-adres SKU's](virtual-network-ip-addresses-overview-arm.md#sku). Als de virtuele machine wordt toegevoegd aan de groep back-end van een openbare Azure Load Balancer, de SKU van het openbare IP-adres van de virtuele machine, moet overeenkomen met de SKU van het openbare IP-adres van de load balancer. Zie voor meer informatie, [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Selecteer een poort of er zijn geen poorten onder **openbare binnenkomende poorten selecteren**. Portal 3389 is ingeschakeld, om externe toegang naar de Windows Server-machine via internet. Poort 3389 te openen vanaf het internet wordt niet aanbevolen voor productieworkloads.

   ![Selecteer een poort](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Accepteer de overige standaardinstellingen en selecteer **OK**.
8. Op de pagina**Overzicht** selecteert u **Maken**. De virtuele machine duurt een paar minuten om te implementeren.
9. Wanneer de virtuele machine is geïmplementeerd, voer *myPublicIpAddress* in het zoekvak boven aan de portal. Wanneer **myPublicIpAddress** wordt weergegeven in de zoekresultaten, selecteert u dit.
10. U vindt het openbare IP-adres dat is toegewezen en dat het adres is toegewezen aan de **myVM** virtuele machine, zoals wordt weergegeven in de volgende afbeelding:

    ![Het openbare IP-adres weergeven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure toegewezen openbaar IP-adres van de adressen die worden gebruikt in de regio die u hebt gemaakt met de virtuele machine in. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Selecteer **configuratie** om te bevestigen dat de toewijzing is **statische**.

    ![Het openbare IP-adres weergeven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> De instellingen voor het IP-adres binnen het besturingssysteem van de virtuele machine mag niet worden gewijzigd. Het besturingssysteem is geen informatie over Azure openbare IP-adressen. Hoewel u privé IP-adresinstellingen aan het besturingssysteem toevoegen kunt, wordt aangeraden niet te doen, tenzij die nodig zijn, en niet pas na het lezen [een privé IP-adres toevoegen aan een besturingssysteem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Meer informatie over alle [openbare IP-adresinstellingen](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) met een Azure-machine
- Meer informatie over het maken van [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuele machines