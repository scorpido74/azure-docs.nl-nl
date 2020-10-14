---
title: Azure Virtual WAN gebruiken om een punt-naar-site-verbinding te maken met Azure | Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een punt-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 84f8563a6b03f10f4cbc647426c350d9fac52780
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812674"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Zelfstudie: Een VPN-verbinding voor gebruikers maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv2)- of OpenVPN. Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAN maken
> * Een P2S-configuratie maken
> * Een hub maken
> * DNS-servers opgeven
> * Een VPN-clientprofiel downloaden
> * Uw virtuele WAN weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Een P2S-configuratie maken

Een punt-naar-site-configuratie (P2S) definieert de parameters om verbinding te maken met externe clients.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Hub maken met punt-naar-site-gateway

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-servers opgeven

Met de virtuele VPN-gateways voor WAN-gebruikers kunt u maximaal vijf DNS-servers opgeven. U kunt dit configureren tijdens het maken van de hub of wijzigen op een later tijdstip. Ga hiervoor naar de virtuele hub. Selecteer onder **Gebruikers-VPN (punt-naar-site)** **configureren** en voer het/de IP-adress(sen) van de DNS-server in het/de tekstvak(ken) **Aangepaste DNS-servers** in.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Aangepaste DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren

Gebruik het gedownloade profiel om de clients voor externe toegang te configureren. De procedure verschilt per besturingssysteem. Volg daarom de instructies voor uw systeem.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
1. Op de pagina **Overzicht** vertegenwoordigt elk punt op de kaart een hub.
1. In de sectie **Hubs en verbindingen** vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
