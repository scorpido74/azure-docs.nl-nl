---
title: Azure Firewall gedwongen tunneling
description: U gedwongen tunneling configureren om internetgebonden verkeer te routeren naar een extra firewall of een virtueel netwerktoestel voor verdere verwerking.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597270"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall gedwongen tunneling (preview)

U Azure Firewall configureren om al het internetgebonden verkeer naar een aangewezen volgende hop te leiden in plaats van rechtstreeks naar het internet te gaan. U bijvoorbeeld een on-premises firewall of ander netwerkvirtueel toestel (NVA) hebben om netwerkverkeer te verwerken voordat het wordt doorgegeven aan het internet.

> [!IMPORTANT]
> Azure Firewall geforceerde tunneling is momenteel in openbare preview.
>
> Deze openbare preview-versie wordt aangeboden zonder serviceovereenkomst en moet niet worden gebruikt voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar op alle Azure-locaties. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Gedwongen tunneling is standaard niet toegestaan op Azure Firewall om ervoor te zorgen dat aan alle uitgaande Azure-afhankelijkheden wordt voldaan. UDR-configuraties (User Defined Route) op het *AzureFirewallSubnet* met een standaardroute die niet rechtstreeks naar het internet gaat, zijn uitgeschakeld.

## <a name="forced-tunneling-configuration"></a>Geforceerde tunnelconfiguratie

Om gedwongen tunneling te ondersteunen, wordt servicemanagementverkeer gescheiden van het verkeer van klanten. Een extra speciaal subnet met de naam *AzureFirewallManagementSubnet* (minimale subnetgrootte /26) is vereist met een eigen gekoppeld openbaar IP-adres. De enige route die op dit subnet is toegestaan, is een standaardroute naar het internet en de verspreiding van de BGP-route moet worden uitgeschakeld.

Als u een standaardroute hebt die via BGP wordt geadverteerd om verkeer naar on-premises te dwingen, moet u het *AzureFirewallSubnet* en *AzureFirewallManagementSubnet* maken voordat u uw firewall implementeert en een UDR hebt met een standaardroute naar internet en **de verspreiding van de virtuele netwerkgatewayroute.**

Binnen deze configuratie kan het *AzureFirewallSubnet* nu routes opnemen naar elke on-premise firewall of NVA om verkeer te verwerken voordat het wordt doorgegeven aan het internet. U deze routes ook publiceren via BGP naar *AzureFirewallSubnet* als de verspreiding van **virtuele netwerkgatewayroutes** is ingeschakeld op dit subnet.

U bijvoorbeeld een standaardroute maken op het *AzureFirewallSubnet* met uw VPN-gateway als de volgende hop om naar uw on-premise-apparaat te gaan. U ook **de verspreiding van virtuele netwerkgatewayroutes** inschakelen om de juiste routes naar het on-premise netwerk te krijgen.

![Verspreiding van virtuele netwerkgatewayroutes](media/forced-tunneling/route-propagation.png)

Zodra u Azure Firewall configureert om gedwongen tunneling te ondersteunen, u de configuratie niet meer ongedaan maken. Als u alle andere IP-configuraties op uw firewall verwijdert, wordt ook de BEHEER-IP-configuratie verwijderd en wordt de firewall toegewezen. Het openbare IP-adres dat is toegewezen aan de BEHEER-IP-configuratie kan niet worden verwijderd, maar u wel een ander openbaar IP-adres toewijzen.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)