---
title: Azure Application Gateway gebruiken met virtuele VMware-machines
description: Beschrijft hoe u de Azure-toepassingsgateway gebruiken om binnenkomend webverkeer voor webservers in virtuele VMware-machines te beheren en de CloudSimple Private Cloud-omgeving te winnen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015453"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Azure Application Gateway gebruiken met virtuele VMware-machines in de CloudSimple Private Cloud-omgeving

U de Azure Application Gateway gebruiken om binnenkomend webverkeer te beheren voor uw webservers die worden uitgevoerd in virtuele VMware-machines binnen uw CloudSimple Private Cloud-omgeving.

Door gebruik te maken van Azure Application Gateway in een publiek-private hybride implementatie, u webverkeer naar uw toepassingen beheren, een veilige front-end bieden en SSL-verwerking voor hun services die in de VMware-omgeving worden uitgevoerd, ontladen. Azure Application Gateway leidt binnenkomend webverkeer naar backendpoolexemplaren die zich in VMware-omgevingen bevinden volgens geconfigureerde regels en statussondes.

Voor deze Azure Application Gateway-oplossing moet u:

* U dient een Azure-abonnement te hebben.
* Maak en configureer een virtueel Azure-netwerk en een subnet binnen het virtuele netwerk.
* Maak en configureer NSG-regels en peer uw vNet met ExpressRoute naar uw CloudSimple Private Cloud.
* Maak & Uw Private Cloud configureren.
* Maak & Uw Azure Application Gateway configureren.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway-implementatiescenario

In dit scenario wordt de Azure Application Gateway uitgevoerd in uw virtuele Azure-netwerk. Het virtuele netwerk is via een ExpressRoute-circuit verbonden met uw Private Cloud. Alle subnetten in de Private Cloud zijn IP bereikbaar via de virtuele netwerksubnetten.

![Azure load balancer in het virtuele Azure-netwerk](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

Het implementatieproces bestaat uit de volgende taken:

1. [Controleren of aan de vereisten is voldaan](#1-verify-prerequisites)
2. [Uw virtuele Azure-verbinding verbinden met de Private Cloud](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Een Azure-toepassingsgateway implementeren](#3-deploy-an-azure-application-gateway)
4. [Vm-groep webservermaken en configureren in uw private cloud](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Vereisten verifiëren

Controleer of aan deze voorwaarden is voldaan:

* Er is al een Azure Resource Manager en een virtueel netwerk gemaakt.
* Er is al een speciaal subnet (voor Application Gateway) binnen uw virtuele Azure-netwerk gemaakt.
* Er is al een CloudSimple Private Cloud gemaakt.
* Er is geen IP-conflict tussen IP-subnetten in het virtuele netwerk en subnetten in de Private Cloud.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Verbind uw Virtuele Azure-netwerk met uw Private Cloud

Als u uw Virtuele Azure-netwerk wilt verbinden met uw Private Cloud, volgt u dit proces.

1. [Kopieer in de CloudSimple-portal de peering-informatie van ExpressRoute.](virtual-network-connection.md)

2. [Configureer een virtuele netwerkgateway voor uw virtuele Azure-netwerk.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Koppel uw virtuele netwerk aan het CloudSimple ExpressRoute-circuit.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

4. [Gebruik de peering-informatie die u hebt gekopieerd om uw virtuele netwerk te koppelen aan het ExpressRoute-circuit.](virtual-network-connection.md)

## <a name="3-deploy-an-azure-application-gateway"></a>3. Een Azure-toepassingsgateway implementeren

De gedetailleerde instructies hiervoor zijn beschikbaar in [Een toepassingsgateway maken met routeringsregels op basis van paden met behulp van de Azure-portal.](../application-gateway/create-url-route-portal.md) Hier volgt een overzicht van de vereiste stappen:

1. Maak een virtueel netwerk in uw abonnements- en resourcegroep.
2. Maak een subnet (te gebruiken als het speciale subnet) binnen uw virtuele netwerk.
3. Een standaardtoepassingsgateway maken (optioneel WAF inschakelen): klik op de startpagina van de Azure-portal op **Gateway voor resourcenetwerken** > **Networking** > **vanaf** de linkerkant van de pagina. Selecteer de standaard SKU en grootte en geef Azure-abonnement, resourcegroep en locatiegegevens op. Maak indien nodig een nieuw openbaar IP-adres voor deze toepassingsgateway en geeft u details over het virtuele netwerk en het speciale subnet voor de toepassingsgateway.
4. Voeg een backend pool met virtuele machines toe en voeg deze toe aan de gateway van uw toepassing.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Een vm-groep van een webserver maken en configureren in uw private cloud

Maak in vCenter VM's met het besturingssysteem en de webserver van uw keuze (zoals Windows/IIS of Linux/Apache). Kies een subnet/VLAN dat is aangewezen voor de weblaag in uw Private Cloud. Controleer of ten minste één vNIC van de vm's van de webserver zich op het subnet van de weblaag bevindt.
