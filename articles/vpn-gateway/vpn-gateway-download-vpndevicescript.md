---
title: Vpn-apparaatconfiguratiescripts downloaden voor S2S VPN-verbindingen
description: In dit artikel u vpn-apparaatconfiguratiescripts downloaden voor S2S VPN-verbindingen met Azure VPN-gateways met Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162133"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Vpn-apparaatconfiguratiescripts downloaden voor S2S VPN-verbindingen

In dit artikel u vpn-apparaatconfiguratiescripts downloaden voor S2S VPN-verbindingen met Azure VPN-gateways met Azure Resource Manager. In het volgende diagram ziet u de werkstroom op hoog niveau.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

De volgende apparaten hebben beschikbare scripts:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Over VPN-apparaatconfiguratiescripts

Een cross-premises VPN-verbinding bestaat uit een Azure VPN-gateway, een on-premises VPN-apparaat en een IPsec S2S VPN-tunnel die de twee verbindt. De typische werkstroom bevat de volgende stappen:

1. Een Azure VPN-gateway maken en configureren (virtuele netwerkgateway)
2. Een lokale Azure-netwerkgateway maken en configureren die uw on-premises netwerk en VPN-apparaat vertegenwoordigt
3. Een Azure VPN-verbinding maken en configureren tussen de Azure VPN-gateway en de lokale netwerkgateway
4. Configureer het on-premises VPN-apparaat dat wordt vertegenwoordigd door de lokale netwerkgateway om de werkelijke S2S VPN-tunnel te maken met de Azure VPN-gateway

U stap 1 tot en met 3 uitvoeren met de [Azure-portal,](vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)of [CLI.](vpn-gateway-howto-site-to-site-resource-manager-cli.md) De laatste stap is het configureren van de on-premises VPN-apparaten buiten Azure. Met deze functie u een configuratiescript voor uw VPN-apparaat downloaden met de bijbehorende waarden van uw Azure VPN-gateway, virtueel netwerk en on-premises netwerkadresvoorvoegsels en VPN-verbindingseigenschappen, enz. U het script als uitgangspunt gebruiken of het script rechtstreeks toepassen op uw on-premises VPN-apparaten via de configuratieconsole.

> [!IMPORTANT]
> * De syntaxis voor elk configuratiescript van elk VPN-apparaat is anders en is sterk afhankelijk van de modellen en firmwareversies. Besteed speciale aandacht aan uw apparaatmodel en versie-informatie aan de beschikbare sjablonen.
> * Sommige parameterwaarden moeten uniek zijn op het apparaat en kunnen niet worden bepaald zonder toegang tot het apparaat. De door Azure gegenereerde configuratiescripts vullen deze waarden vooraf op, maar u moet ervoor zorgen dat de opgegeven waarden geldig zijn op uw apparaat. Voor voorbeelden:
>    * Interfacenummers
>    * Lijstnummers van toegangsbeheer
>    * Beleidsnamen of -nummers, enz.
> * Zoek naar het trefwoord "**REPLACE**", ingebed in het script om de parameters te vinden die u moet verifiëren voordat u het script toepast.
> * Sommige sjablonen bevatten een sectie '**OPSCHONEn'** die u toepassen om de configuraties te verwijderen. De opschoningssecties worden standaard weergegeven.

## <a name="download-the-configuration-script-from-azure-portal"></a>Het configuratiescript downloaden van Azure-portal

Maak een Azure VPN-gateway, lokale netwerkgateway en een verbindingsbron die de twee verbindt. Op de volgende pagina leidt u de stappen door:

* [Een site-naar-site-verbinding maken in Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Zodra de verbindingsbron is gemaakt, volgt u de onderstaande instructies om de configuratiescripts van het VPN-apparaat te downloaden:

1. Navigeer vanuit een browser naar de [Azure-portal](https://portal.azure.com) en meld u indien nodig aan met uw Azure-account
2. Ga naar de verbindingsbron die u hebt gemaakt. U de lijst met alle verbindingsbronnen vinden door op 'Alle services' te klikken, vervolgens op NETWERKEN en 'Verbindingen'.

    ![lijst met verbinding](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klik op de verbinding die u wilt configureren.

    ![verbindingsoverzicht](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klik op de link 'Configuratie downloaden' zoals gemarkeerd in het rood op de pagina Verbindingsoverzicht; hiermee wordt de pagina 'Downloadconfiguratie' geopend.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecteer de modelfamilie en firmwareversie voor uw VPN-apparaat en klik op de knop 'Configuratie downloaden'.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. U wordt gevraagd het gedownloade script (een tekstbestand) op te slaan in uw browser.
7. Zodra u het configuratiescript hebt gedownload, opent u het met een teksteditor en zoekt u naar het zoekwoord "VERVANGEN" om de parameters te identificeren en te onderzoeken die mogelijk moeten worden vervangen.

    ![bewerken-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Het configuratiescript downloaden met Azure PowerShell



U het configuratiescript ook downloaden met Azure PowerShell, zoals in het volgende voorbeeld wordt weergegeven:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Het configuratiescript toepassen op uw VPN-apparaat

Nadat u het configuratiescript hebt gedownload en gevalideerd, is de volgende stap het toepassen van het script op uw VPN-apparaat. De werkelijke procedure varieert op basis van uw VPN-apparaat maakt en modellen. Raadpleeg de handleidingen of de instructiepagina's voor uw VPN-apparaten.

## <a name="next-steps"></a>Volgende stappen

Doorgaan met het configureren van uw [site-naar-site-verbinding](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
