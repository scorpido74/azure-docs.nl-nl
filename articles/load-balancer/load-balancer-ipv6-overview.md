---
title: Overzicht van IPv6 - Azure Load Balancer
description: Ga met dit leerpad aan de slag met IPv6-ondersteuning voor Azure Load Balancer en vm's die in balans zijn met de belasting.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: d2966f32fcf7f8be2a93d1639f0a63f49768c306
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981848"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Overzicht van IPv6 voor Azure Load Balancer


>[!NOTE] 
>Deze inhoud is vervangen door [IPv6 voor Azure VNet Overview](https://docs.microsoft.com/azure/virtual-network/ipv6-overview). Azure raadt nieuwe IPv6-implementaties aan de nieuwe IPv6-functies voor Virtual Networks te gebruiken.

>[!NOTE]
>Azure Load Balancer ondersteunt twee verschillende typen: Basic en Standard. In dit artikel wordt Basic Load Balancer beschreven. Zie Overzicht van Standaard load [balancer](load-balancer-standard-overview.md)voor meer informatie over Standard Load Balancer.

Basic SKU-internetgebaseerde load balancers kunnen worden geïmplementeerd met een IPv6-adres. Naast IPv4-connectiviteit maakt dit de volgende mogelijkheden mogelijk:

* Native end-to-end IPv6-connectiviteit tussen openbare internetclients en Azure Virtual Machines (VM's) via de load balancer.
* Native end-to-end IPv6-uitgaande connectiviteit tussen VM's en openbare IPv6-clients met IPv6-functionaliteit.

De volgende afbeelding illustreert de IPv6-functionaliteit voor Azure Load Balancer.

![Azure Load Balancer met IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Eenmaal geïmplementeerd kan een Internetclient met IPv4- of IPv6-client communiceren met de openbare IPv4- of IPv6-adressen (of hostnamen) van de Azure-internetgestuurde load balancer. De load balancer leidt de IPv6-pakketten naar de privé IPv6-adressen van de VM's met behulp van network address translation (NAT). De IPv6-internetclient kan niet rechtstreeks communiceren met het IPv6-adres van de VM's.

## <a name="features"></a>Functies

Native IPv6-ondersteuning voor VM's die zijn geïmplementeerd via Azure Resource Manager biedt:

1. IPv6-services voor IPv6-clients op internet met laadbalans
2. Native IPv6- en IPv4-eindpunten op VM's ('dual stacked')
3. Binnenkomende en uitgaande native IPv6-verbindingen
4. Ondersteunde protocollen zoals TCP, UDP en HTTP(S) maken een volledig scala aan servicearchitecturen mogelijk

## <a name="benefits"></a>Voordelen

Deze functionaliteit biedt de volgende voordelen:

* Voldoen aan overheidsvoorschriften die vereisen dat nieuwe toepassingen toegankelijk zijn voor IPv6-clients
* Mobile en Internet of Things (IOT)-ontwikkelaars in staat stellen om dual-stacked (IPv4+IPv6) Azure Virtual Machines te gebruiken om de groeiende mobiele & IOT-markten aan te pakken

## <a name="details-and-limitations"></a>Details en beperkingen

Details

* De Azure DNS-service bevat zowel IPv4 A- als IPv6 AAAA-naamrecords en reageert met beide records voor de load balancer. De client kiest met welk adres (IPv4 of IPv6) hij wil communiceren.
* Wanneer een VM een verbinding met een openbaar iPv6-apparaat met internetinitieert, is het iPv6-adres van de VM netwerkadres (NAT) vertaald naar het openbare IPv6-adres van de load balancer.
* VM's waarop het Linux-besturingssysteem wordt uitgevoerd, moeten zijn geconfigureerd om een IPv6 IP-adres via DHCP te ontvangen. Veel van de Linux-afbeeldingen in de Azure Gallery zijn al geconfigureerd om IPv6 zonder wijziging te ondersteunen. Zie [DHCPv6 configureren voor Linux VM's voor](load-balancer-ipv6-for-linux.md) meer informatie
* Als u ervoor kiest om een health probe te gebruiken met uw load balancer, maakt u een IPv4-sonde en gebruikt u deze met zowel de IPv4- als de IPv6-eindpunten. Als de service op uw VM uitvalt, worden zowel de IPv4- als de IPv6-eindpunten uit de rotatie gehaald.

Beperkingen

* U geen IPv6-taakverdelingsregels toevoegen aan de Azure-portal. De regels kunnen alleen worden gemaakt via de sjabloon, CLI, PowerShell.
* U mag bestaande VM's niet upgraden om IPv6-adressen te gebruiken. U moet nieuwe VM's implementeren.
* Eén IPv6-adres kan worden toegewezen aan één netwerkinterface in elke vm.
* De openbare IPv6-adressen kunnen niet aan een vm worden toegewezen. Ze kunnen alleen worden toegewezen aan een load balancer.
* U de reverse DNS-lookup niet configureren voor uw openbare IPv6-adressen.
* De VM's met de IPv6-adressen kunnen geen lid zijn van een Azure Cloud Service. Ze kunnen worden verbonden met een Azure Virtual Network (VNet) en met elkaar communiceren via hun IPv4-adressen.
* Privé-IPv6-adressen kunnen worden geïmplementeerd op afzonderlijke VM's in een resourcegroep, maar kunnen niet worden geïmplementeerd in een resourcegroep via schaalsets.
* Azure VM's kunnen geen verbinding maken via IPv6 met andere VM's, andere Azure-services of on-premises apparaten. Ze kunnen alleen communiceren met de Azure load balancer via IPv6. Ze kunnen echter communiceren met deze andere bronnen met IPv4.
* Network Security Group (NSG) bescherming voor IPv4 wordt ondersteund in dual-stack (IPv4+ IPv6) implementaties. NSG's zijn niet van toepassing op de IPv6-eindpunten.
* Het IPv6-eindpunt op de VM wordt niet rechtstreeks aan het internet blootgesteld. Het is achter een load balancer. Alleen de poorten die in de regels voor load balancer zijn opgegeven, zijn toegankelijk via IPv6.
* Als u de parameter IdleTimeout voor IPv6 **wijzigt, wordt deze momenteel niet ondersteund.** De standaardinstelling is vier minuten.
* Het wijzigen van de parameter loadDistributionMethod voor IPv6 **wordt momenteel niet ondersteund.**
* Gereserveerde IPv6 IP's (waarbij IPAllocationMethod = statisch) **momenteel niet worden ondersteund**.
* NAT64 (vertaling van IPv6 naar IPv4) wordt niet ondersteund.
* Het toevoegen van een secundaire NIC die verwijst naar een IPv6-subnet aan een back-endpool **wordt momenteel niet ondersteund**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van een load balancer met IPv6.

* [Beschikbaarheid van IPv6 per regio](https://go.microsoft.com/fwlink/?linkid=828357)
* [Een load balancer implementeren met IPv6 met behulp van een sjabloon](load-balancer-ipv6-internet-template.md)
* [Een load balancer implementeren met IPv6 met Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Een load balancer implementeren met IPv6 met Azure CLI](load-balancer-ipv6-internet-cli.md)
