---
title: Geforceerde tunneling Azure Firewall
description: U kunt geforceerde tunneling configureren om Internet-gebonden verkeer te routeren naar een extra firewall of een virtueel netwerk apparaat voor verdere verwerking.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: a467aa60b131e47e9251366369b3fae8dd95c004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84267695"
---
# <a name="azure-firewall-forced-tunneling"></a>Geforceerde tunneling Azure Firewall

Wanneer u een nieuw Azure Firewall configureert, kunt u alle Internet-gebonden verkeer routeren naar een aangewezen volgende hop in plaats van rechtstreeks naar Internet te gaan. U kunt bijvoorbeeld een on-premises edge-firewall of een ander virtueel netwerkapparaat (NVA) hebben om netwerkverkeer te verwerken voordat het wordt doorgegeven aan internet. U kunt echter geen bestaande firewall configureren voor geforceerde tunneling.

Geforceerde tunneling is standaard niet toegestaan op Azure Firewall om ervoor te zorgen dat aan alle uitgaande Azure-afhankelijkheden wordt voldaan. UDR-configuraties (door de gebruiker gedefinieerde route) op de *AzureFirewallSubnet* die een standaard route hebben die niet rechtstreeks naar Internet gaat, worden uitgeschakeld.

## <a name="forced-tunneling-configuration"></a>Configuratie van geforceerde tunneling

Voor de ondersteuning van geforceerde tunneling wordt Service Management-verkeer gescheiden van het verkeer van de klant. Een extra toegewezen subnet met de naam *AzureFirewallManagementSubnet* (minimale subnet-grootte/26) is vereist met een eigen gekoppeld openbaar IP-adres. De enige route die op dit subnet is toegestaan, is een standaard route naar Internet en de doorgifte van BGP-routes moet worden uitgeschakeld.

Als u een standaard route hebt geadverteerd via BGP om verkeer naar de on-premises af te dwingen, moet u de *AzureFirewallSubnet* en *AzureFirewallManagementSubnet* maken voordat u uw firewall implementeert en een UDR met een standaard route naar het Internet hebt en de **gateway route doorgifte van het virtuele netwerk** is uitgeschakeld.

Binnen deze configuratie kan de *AzureFirewallSubnet* nu routes naar een on-premises firewall of NVA bevatten om verkeer te verwerken voordat het wordt door gegeven aan Internet. U kunt deze routes ook via BGP naar *AzureFirewallSubnet* publiceren als de **route doorgifte van de virtuele netwerk gateway** is ingeschakeld op dit subnet.

U kunt bijvoorbeeld een standaard route maken op het *AzureFirewallSubnet* met uw VPN-gateway als de volgende hop om naar uw on-premises apparaat te gaan. U kunt ook de **route doorgifte van de virtuele netwerk gateway** inschakelen om de juiste routes naar het on-premises netwerk op te halen.

![Route doorgifte van virtuele netwerk gateway](media/forced-tunneling/route-propagation.png)

Als u geforceerde tunneling inschakelt, wordt het Internet-gebonden verkeer omgezet naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet, waardoor de bron wordt verborgen via uw on-premises firewall.

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall echter zodanig configureren dat uw open bare IP-adres bereik **niet** wordt gesnat. Raadpleeg [Azure Firewall SNAT voor privé-IP-adresbereiken](snat-private-range.md) voor meer informatie.

Zodra u Azure Firewall configureert om geforceerde tunneling te ondersteunen, kunt u de configuratie niet ongedaan maken. Als u alle andere IP-configuraties van uw firewall verwijdert, wordt de beheer-IP-configuratie ook verwijderd en wordt de toewijzing van de Firewall ongedaan gemaakt. Het open bare IP-adres dat is toegewezen aan de beheer-IP-configuratie kan niet worden verwijderd, maar u kunt een ander openbaar IP-adres toewijzen.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met Azure Portal](tutorial-hybrid-portal.md)
