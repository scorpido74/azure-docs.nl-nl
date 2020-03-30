---
title: Een VM maken met een statisch openbaar IP-adres - Azure-portal | Microsoft Documenten
description: Meer informatie over het maken van een vm met een statisch openbaar IP-adres met de Azure-portal.
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
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043538"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Een virtuele machine maken met een statisch openbaar IP-adres met behulp van de Azure-portal

U een virtuele machine maken met een statisch openbaar IP-adres. Met een openbaar IP-adres u vanaf het internet communiceren met een virtuele machine. Wijs een statisch openbaar IP-adres toe, in plaats van een dynamisch adres, om ervoor te zorgen dat het adres nooit verandert. Meer informatie over [statische openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#allocation-method). Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)als u een openbaar IP-adres wilt wijzigen dat is toegewezen aan een bestaande virtuele machine van dynamisch naar statisch, of om met privé-IP-adressen te werken. Openbare IP-adressen hebben een [nominale vergoeding](https://azure.microsoft.com/pricing/details/ip-addresses)en er is een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) aan het aantal openbare IP-adressen dat u per abonnement gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Berekenen**en selecteer **vervolgens Windows Server 2016 VM**of een ander besturingssysteem naar keuze.
3. Voer de volgende gegevens in of selecteer de volgende gegevens, accepteer de standaardinstellingen voor de overige instellingen en selecteer **OK:**

    |Instelling|Waarde|
    |---|---|
    |Name|myVM|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Locatie| Selecteer **VS - oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Selecteer **onder Instellingen**het **ip-adres openbaar**.
6. Typ *myPublicIpAddress*, selecteer **Statisch**en selecteer **OK,** zoals in de volgende afbeelding wordt weergegeven:

   ![Statische knop selecteren](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Als het openbare IP-adres een standaard SKU moet zijn, selecteert u **Standaard** onder **SKU**. Meer informatie over [SKU's voor openbaar IP-adres](virtual-network-ip-addresses-overview-arm.md#sku). Als de virtuele machine wordt toegevoegd aan de back-endpool van een openbare Azure Load Balancer, moet de SKU van het openbare IP-adres van de virtuele machine overeenkomen met de SKU van het openbare IP-adres van de load balancer. Zie Azure [Load Balancer](../load-balancer/concepts-limitations.md#skus)voor meer informatie.

6. Selecteer een poort of geen poorten onder **Openbare binnenkomende poorten selecteren**. Portal 3389 is geselecteerd om externe toegang tot de virtuele windows server-machine vanaf het internet in te schakelen. Het openen van poort 3389 vanaf het internet wordt niet aanbevolen voor productieworkloads.

   ![Een poort selecteren](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Accepteer de resterende standaardinstellingen en selecteer **OK**.
8. Op de pagina**Overzicht** selecteert u **Maken**. Het implementeren van de virtuele machine duurt enkele minuten.
9. Zodra de virtuele machine is geïmplementeerd, voert u *myPublicIpAddress* in het zoekvak boven aan de portal in. Wanneer **myPublicIpAddress** in de zoekresultaten wordt weergegeven, selecteert u deze.
10. U het openbare IP-adres bekijken dat is toegewezen en dat het adres is toegewezen aan de virtuele **myVM-machine,** zoals in de volgende afbeelding wordt weergegeven:

    ![Openbaar IP-adres weergeven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure heeft een openbaar IP-adres toegewezen aan adressen die worden gebruikt in de regio waarin u de virtuele machine hebt gemaakt. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

11. Selecteer **Configuratie** om te bevestigen dat de toewijzing **statisch**is .

    ![Openbaar IP-adres weergeven](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Wijzig de IP-adresinstellingen in het besturingssysteem van de virtuele machine niet. Het besturingssysteem is niet op de hoogte van openbare IP-adressen van Azure. Hoewel u privé-IP-adresinstellingen aan het besturingssysteem toevoegen, raden we u aan dit niet te doen tenzij dat nodig is, en pas na het lezen [een privé-IP-adres toevoegen aan een besturingssysteem.](virtual-network-network-interface-addresses.md#private)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ *myResourceGroup* voor **TYPE DE NAAM VAN DE RESOURCEGROEP:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Meer informatie over alle [instellingen voor openbare IP-adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over [privé-IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine
- Meer informatie over [het](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maken van virtuele Linux- en [Windows-machines](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)