---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3c10ca35ee2f085d4ce41e862a895ff17ff63a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317715"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hoeveel VPN-clienteindpunten kan mijn punt-naar-site-configuratie hebben?

Dit is afhankelijk van de gateway-SKU. Zie [Gateway-sku's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)voor meer informatie over het aantal ondersteunde verbindingen.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Welke client besturingssystemen kan ik met punt-naar-site gebruiken?

De volgende clientbesturingssystemen worden ondersteund:

* Windows 7 (32-bits en 64-bits)
* Windows Server 2008 R2 (alleen 64-bits)
* Windows 8.1 (32-bits en 64-bits)
* Windows Server 2012 (alleen 64-bits)
* Windows Server 2012 R2 (alleen 64-bits)
* Windows Server 2016 (alleen 64-bits)
* Windows Server 2019 (alleen 64-bits)
* Windows 10
* Mac OS X-versie 10,11 of hoger
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Kan ik met punt-naar-site-functionaliteit proxy's en firewalls passeren?

Azure ondersteunt drie typen punt-naar-site VPN-opties:

* Secure Socket Tunneling Protocol (SSTP). SSTP is een eigen op SSL gebaseerde oplossing van micro soft die firewalls kan binnendringen, aangezien de meeste firewalls de uitgaande TCP-poort openen die 443 SSL gebruikt.

* OpenVPN. OpenVPN is een op SSL gebaseerde oplossing die firewalls kan binnendringen, aangezien de meeste firewalls de uitgaande TCP-poort openen die 443 SSL gebruikt.

* IKEv2 VPN. IKEv2 VPN is een op standaarden gebaseerde IPsec VPN-oplossing die gebruikmaakt van uitgaande UDP-poorten 500 en 4500 en IP-protocol nummer. 50. Firewalls openen deze poorten niet altijd, zodat de kans bestaat dat een IKEv2 VPN proxy's en firewalls niet kan passeren.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Als ik een clientcomputer die is geconfigureerd voor punt-naar-site opnieuw start, wordt de VPN-verbinding dan automatisch opnieuw tot stand gebracht?

Standaard wordt de VPN-verbinding niet automatisch op de clientcomputer hersteld.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Biedt punt-naar-site ondersteuning voor automatisch opnieuw verbinding maken en DDNS op de VPN-clients?

Automatisch opnieuw verbinding maken en DDNS worden momenteel niet ondersteund in VPN’s met punt-naar-site-verbinding.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kunnen site-naar-site- en punt-naar-site-configuraties naast elkaar worden gebruikt in hetzelfde virtuele netwerk?

Ja. Voor het Resource Manager-implementatiemodel moet u een RouteBased VPN-type hebben voor uw gateway. Voor het klassieke implementatiemodel hebt u een dynamische gateway nodig. Point-to-site wordt niet ondersteund voor VPN-gateways met statische routering of PolicyBased VPN-gateways.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Kan ik een punt-naar-site-client zo configureren dat deze op hetzelfde moment verbinding maakt met meerdere virtuele netwerk gateways?

Afhankelijk van de gebruikte VPN-client software, kunt u verbinding maken met meerdere Virtual Network gateways, op voor voor de virtuele netwerken waarmee verbinding wordt gemaakt en er zijn geen conflicterende adres ruimten tussen de clients of het netwerk van waaruit de client verbinding maakt.  Terwijl de Azure VPN-client veel VPN-verbindingen ondersteunt, kan slechts één verbinding op een bepaald moment worden aangesloten.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kan ik een punt-naar-site-client configureren om verbinding te maken met meerdere virtuele netwerken tegelijk?

Ja, punt-naar-site-verbindingen met een Virtual Network gateway die is geïmplementeerd in een VNet dat is gekoppeld aan andere VNets, kan toegang hebben tot andere peered VNets.  Als de peered VNets gebruikmaakt van de UseRemoteGateway/AllowGatewayTransit-functies, kan de punt-naar-site-client verbinding maken met die peered VNets.  Raadpleeg [Dit](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) artikel voor meer informatie.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Hoeveel doorvoer kan ik verwachten via site-naar-site- of punt-naar-site-verbindingen?

Het is moeilijk om de exacte doorvoer van de VPN-tunnels te onderhouden. IPSec en SSTP zijn cryptografisch zware VPN-protocollen. Doorvoer wordt ook beperkt door de latentie en bandbreedte tussen uw locatie en het internet. Voor een VPN-gateway met alleen IKEv2 point-to-site-VPN-verbindingen is de totale doorvoer die u kunt verwachten, afhankelijk van de Gateway-SKU. Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) voor meer informatie over doorvoer.

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Kan ik voor point-to-site elke VPN-softwareclient gebruiken die SSTP en/of IKEv2 ondersteunt?

Nee. U kunt alleen de systeemeigen VPN-client van Windows voor SSTP en de systeemeigen VPN-client van Mac voor IKEv2 gebruiken. U kunt echter de OpenVPN-client op alle platforms gebruiken om verbinding te maken via het OpenVPN-protocol. Raadpleeg de lijst met ondersteunde clientbesturingssystemen.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Biedt Azure ondersteuning voor IKEv2-VPN met Windows?

IKEv2 wordt ondersteund op Windows 10 en Server 2016. Als u IKEv2 wilt gebruiken, moet u lokaal updates installeren en een registersleutelwaarde instellen. Versies van besturings systemen voorafgaand aan Windows 10 worden niet ondersteund en kunnen alleen gebruikmaken van het SSTP-of **OpenVPN®-protocol**.

Windows 10 of Server 2016 voorbereiden voor IKEv2:

1. Installeer de update.

   | Besturingssysteemversie | Datum | Aantal/koppeling |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 versie 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versie 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versie 1709 | 22 maart 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. De registersleutelwaarde instellen. Maak of stel de REG_DWORD-sleutel 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload' in het register in op 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Wat gebeurt er als ik zowel SSTP als IKEv2 voor P2S-VPN-verbindingen configureer?

Als u zowel SSTP en IKEv2 configureert in een gemengde omgeving (bestaande uit Windows- en Mac-apparaten), probeert de VPN-client van Windows altijd als eerst de IKEv2-tunnel, maar valt de client terug op SSTP als de IKEv2-verbinding niet is geslaagd. MacOSX maakt alleen verbinding via IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Welke platformen, naast Windows en Mac, worden door Azure ondersteund voor P25-VPN?

Azure biedt voor P2S VPN ondersteuning voor Windows, Mac en Linux.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Ik heb al een Azure VPN-gateway geïmplementeerd. Kan ik er RADIUS en/of IKEv2 VPN voor inschakelen?

Ja, u kunt deze nieuwe functies op reeds geïmplementeerde gateways inschakelen met behulp van PowerShell of Azure Portal, mits de gateway-SKU die u gebruikt RADIUS en/of IKEv2 ondersteunt. Bijvoorbeeld de VPN-gateway Basic SKU ondersteunt RADIUS of IKEv2 niet.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Hoe kan ik de configuratie van een P2S-verbinding verwijderen?

Een P2S-configuratie kan worden verwijderd met behulp van Azure CLI en Power shell met behulp van de volgende opdrachten:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
