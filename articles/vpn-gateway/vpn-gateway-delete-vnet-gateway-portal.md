---
title: 'Azure VPN-gateway: een gateway verwijderen: portal'
description: Een virtuele netwerkgateway verwijderen met behulp van de Azure-portal in het implementatiemodel ResourceBeheer.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863662"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Een virtuele netwerkgateway verwijderen via de portal

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassiek)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

In dit artikel vindt u de instructies voor het verwijderen van een Azure VPN-gateways die zijn geïmplementeerd met behulp van het implementatiemodel Resource Manager. Er zijn een paar verschillende benaderingen die u nemen wanneer u een virtuele netwerkgateway voor een VPN-gateway-configuratie wilt verwijderen.

- Als u alles wilt verwijderen en opnieuw wilt beginnen, zoals in het geval van een testomgeving, u de brongroep verwijderen. Wanneer u een brongroep verwijdert, worden alle bronnen binnen de groep verwijderd. Deze methode wordt alleen aanbevolen als u geen van de resources in de resourcegroep wilt behouden. U niet selectief verwijderen slechts een paar middelen met behulp van deze aanpak.

- Als u een deel van de resources in uw resourcegroep wilt behouden, wordt het verwijderen van een virtuele netwerkgateway iets ingewikkelder. Voordat u de virtuele netwerkgateway verwijderen, moet u eerst bronnen verwijderen die afhankelijk zijn van de gateway. Welke stappen u volgt, is afhankelijk van het type verbindingen dat u hebt gemaakt en de afhankelijke resources voor elke verbinding.

> [!IMPORTANT]
> In de onderstaande instructies wordt beschreven hoe azure VPN-gateways kunnen worden verwijderd die zijn geïmplementeerd met behulp van het implementatiemodel Voor Resource Beheer. Als u een VPN-gateway wilt verwijderen die is geïmplementeerd met behulp van het klassieke implementatiemodel, gebruikt u Azure PowerShell zoals [hier](vpn-gateway-delete-vnet-gateway-classic-powershell.md)beschreven.


## <a name="delete-a-vpn-gateway"></a>Een VPN-gateway verwijderen

Als u een virtuele netwerkgateway wilt verwijderen, moet u eerst elke bron verwijderen die betrekking heeft op de virtuele netwerkgateway. Resources moeten in een bepaalde volgorde worden verwijderd vanwege afhankelijkheden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Op dit moment wordt de virtuele netwerkgateway verwijderd. Met de volgende stappen u resources verwijderen die niet meer worden gebruikt.

### <a name="to-delete-the-local-network-gateway"></a>De lokale netwerkgateway verwijderen

1. Zoek in **Alle bronnen**de lokale netwerkgateways die aan elke verbinding zijn gekoppeld.
2. Klik op het **overzichtsblad** voor de lokale netwerkgateway op **Verwijderen**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>De bron voor openbaar IP-adres voor de gateway verwijderen

1. Zoek in **Alle resources**de bron openbaar IP-adres die aan de gateway is gekoppeld. Als de virtuele netwerkgateway actief actief was, ziet u twee openbare IP-adressen. 
2. Klik op de pagina **Overzicht** voor het openbare IP-adres op **Verwijderen**en **vervolgens Ja** om te bevestigen.

### <a name="to-delete-the-gateway-subnet"></a>Het subnet van de gateway verwijderen

1. Zoek **in Alle bronnen**het virtuele netwerk. 
2. Klik op het **subnetsblad** op het **GatewaySubnet**en klik vervolgens op **Verwijderen**. 
3. Klik **op Ja** om te bevestigen dat u het gateway-subnet wilt verwijderen.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Een VPN-gateway verwijderen door de brongroep te verwijderen

Als u zich geen zorgen maakt over het behouden van een van uw resources in de resourcegroep en u wilt gewoon opnieuw beginnen, u een hele brongroep verwijderen. Dit is een snelle manier om alles te verwijderen. De volgende stappen zijn alleen van toepassing op het implementatiemodel resourcebeheer.

1. Zoek in **Alle bronnen**de brongroep en klik om het blad te openen.
2. Klik **op Verwijderen**. Bekijk op het blad Verwijderen de betreffende resources. Zorg ervoor dat u al deze bronnen wilt verwijderen. Zo niet, gebruik dan de stappen in Een VPN-gateway boven aan dit artikel verwijderen.
3. Als u verder wilt gaan, typt u de naam van de brongroep die u wilt verwijderen en klikt u op **Verwijderen**.
