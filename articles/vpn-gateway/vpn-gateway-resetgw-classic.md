---
title: Een Azure VPN-gateway opnieuw instellen om de IPsec-tunnel opnieuw in te stellen
description: In dit artikel u uw Azure VPN-gateway opnieuw instellen om IPsec-tunnels te herstellen. Het artikel is van toepassing op VPN-gateways in zowel de klassieke als de implementatiemodellen van Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244418"
---
# <a name="reset-a-vpn-gateway"></a>Een VPN-gateway opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer Site-to-Site VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Met dit artikel u uw VPN-gateway opnieuw instellen.

### <a name="what-happens-during-a-reset"></a>Wat gebeurt er tijdens een reset?

Een VPN-gateway bestaat uit twee VM-exemplaren die worden uitgevoerd in een active-standby-configuratie. Wanneer u de gateway opnieuw instelt, wordt de gateway opnieuw opgestart en worden de cross-premises configuraties opnieuw ingesteld. De gateway behoudt hetzelfde openbare IP-adres. Dat betekent dat u de VPN-routerconfiguratie niet hoeft bij te werken met een nieuw openbaar IP-adres voor de Azure VPN-gateway.

Wanneer u de opdracht uitgeeft om de gateway opnieuw in te stellen, wordt het huidige actieve exemplaar van de Azure VPN-gateway onmiddellijk opnieuw opgestart. Er zal een korte kloof zijn tijdens de failover van de actieve instantie (opnieuw opgestart), naar de stand-by instantie. Deze onderbreking zou niet langer dan een minuut moeten duren.

Als de verbinding na de eerste keer opnieuw opstarten niet is hersteld, voert u deze opdracht opnieuw uit om de tweede VM-instantie (de nieuwe actieve gateway) opnieuw op te starten. Als de twee keer opnieuw opstarten direct na elkaar wordt aangevraagd, duurt het iets langer omdat beide VM-exemplaren (actief en stand-by) opnieuw worden opgestart. Dit zal leiden tot een langere voorsprong op de VPN-connectiviteit, tot 30 tot 45 minuten voor VM's om de reboots te voltooien.

Als u na twee reboots nog steeds problemen ondervindt met cross-premises connectiviteitsproblemen, opent u een ondersteuningsverzoek van de Azure-portal.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Controleer voordat u de gateway opnieuw instelt de hieronder vermelde belangrijke zaken voor elke S2S-VPN-tunnel (site-naar-site) met IPsec. Als items niet overeenkomen, wordt de verbinding met S2SVPN-tunnels verbroken. Het verifiëren en corrigeren van de configuraties voor uw on-premises en Azure VPN-gateways bespaart u onnodige reboots en onderbrekingen voor de andere werkende verbindingen op de gateways.

Controleer de volgende items voordat u de gateway opnieuw instelt:

* Controleer of de internet-IP-adressen (VIP's) voor zowel de Azure VPN-gateway als de on-premises VPN-gateway correct zijn geconfigureerd in zowel het Azure- als het lokale VPN-beleid.
* De vooraf gedeelde sleutel moet hetzelfde zijn in de Azure- en de on-premises VPN-gateway.
* Als u een specifieke IPsec/IKE-configuratie, zoals versleuteling, hash-algoritmen en PFS (Perfect Forward Secrecy) toepast, moet u controleren of de Azure- en on-premises VPN-gateway dezelfde configuratie hebben.

## <a name="azure-portal"></a><a name="portal"></a>Azure-portal

U een VPN-gateway voor Resource Manager opnieuw instellen via de Azure-portal. Als u een klassieke gateway opnieuw wilt instellen, raadpleegt u de [PowerShell-stappen.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Resource Manager-implementatiemodel

1. Open de [Azure-portal](https://portal.azure.com) en navigeer naar de virtuele netwerkgateway van Resource Manager die u opnieuw wilt instellen.
2. Klik op het blad voor de virtuele netwerkgateway op 'Opnieuw instellen'.

   ![VPN-gatewayblade opnieuw instellen](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Klik op het mes opnieuw instellen op de knop **Opnieuw instellen.**

## <a name="powershell"></a><a name="ps"></a>Powershell

### <a name="resource-manager-deployment-model"></a>Resource Manager-implementatiemodel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De cmdlet voor het opnieuw instellen van een gateway is **Reset-AzVirtualNetworkGateway**. Voordat u een reset uitvoert, moet u ervoor zorgen dat u de nieuwste versie van de [PowerShell Az-cmdlets hebt.](https://docs.microsoft.com/powershell/module/az.network) In het volgende voorbeeld wordt een virtuele netwerkgateway met de naam VNet1GW in de testrg1-brongroep opnieuw ingesteld:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultaat:

Wanneer u een retourresultaat ontvangt, u ervan uitgaan dat de gatewayreset is geslaagd. Er is echter niets in het retourresultaat dat expliciet aangeeft dat de reset succesvol is. Als u de geschiedenis goed wilt bekijken om precies te zien wanneer de gateway is gereset, u die informatie bekijken in de [Azure-portal.](https://portal.azure.com) Navigeer in de portal naar **'GatewayName' > Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassiek implementatiemodel

De cmdlet voor het opnieuw instellen van een gateway is **Reset-AzureVNetGateway**. De Azure PowerShell-cmdlets voor Servicebeheer moeten lokaal op uw bureaublad worden geïnstalleerd. U Azure Cloud Shell niet gebruiken. Voordat u een reset uitvoert, moet u ervoor zorgen dat u de nieuwste versie van de [PowerShell-cmdlets (Service Management) hebt.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Controleer bij het gebruik van deze opdracht of u de volledige naam van het virtuele netwerk gebruikt. Klassieke VNets die zijn gemaakt met behulp van de portal hebben een lange naam die nodig is voor PowerShell. U de lange naam bekijken met 'Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml'.

In het volgende voorbeeld wordt de gateway voor een virtueel netwerk met de naam "Group TestRG1 TestVNet1" (dat wordt weergegeven als simpelweg "TestVNet1" in de portal) opnieuw ingesteld:

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Resultaat:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>Azure-CLI

Als u de gateway wilt resetten, gebruikt u de opdracht [vnet-gateway reset van het AZ-netwerk.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) In het volgende voorbeeld wordt een virtuele netwerkgateway met de naam VNet5GW in de testrg5-brongroep opnieuw ingesteld:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultaat:

Wanneer u een retourresultaat ontvangt, u ervan uitgaan dat de gatewayreset is geslaagd. Er is echter niets in het retourresultaat dat expliciet aangeeft dat de reset succesvol is. Als u de geschiedenis goed wilt bekijken om precies te zien wanneer de gateway is gereset, u die informatie bekijken in de [Azure-portal.](https://portal.azure.com) Navigeer in de portal naar **'GatewayName' > Resource Health**.
