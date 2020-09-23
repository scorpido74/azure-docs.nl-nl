---
title: 'VPN Gateway: Wijzig de IP-adres instellingen van de gateway: Azure Portal'
description: In dit artikel vindt u instructies voor het wijzigen van IP-adres voorvoegsels voor uw lokale netwerk gateway met behulp van de Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983154"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Instellingen voor lokale netwerkgateway wijzigen via Azure-portal

Soms worden de instellingen voor de AddressPrefix of GatewayIPAddress van uw lokale netwerk gateway gewijzigd. In dit artikel wordt beschreven hoe u de instellingen van uw lokale netwerk gateway wijzigt. U kunt deze instellingen ook met behulp van een andere methode wijzigen door een andere optie te selecteren in de volgende lijst:

Voordat u de verbinding verwijdert, kunt u de configuratie voor uw apparaten waarmee u verbinding maakt, downloaden om de gedefinieerde PSK te verkrijgen. Op die manier hoeft u deze niet aan de andere kant opnieuw te definiëren.

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuratie van de lokale netwerk gateway

In de onderstaande scherm afbeelding ziet u de **configuratie** pagina van een lokale netwerk gateway resource die gebruikmaakt van een openbaar IP-adres eindpunt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Gateway van het lokale netwerk configureren-IP-adres":::

Dit is dezelfde configuratie pagina met een FQDN-eind punt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Lokale netwerk gateway configureren-FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Het IP-adres van de gateway wijzigen

Als van het VPN-apparaat waarmee u verbinding wilt maken het openbare IP-adres is gewijzigd, moet u de gateway van het lokale netwerk aanpassen met deze wijziging.

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Wijzig het IP-adres in het vak **IP-adres** .
3. Klik op **Opslaan** om de instellingen op te slaan.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>De gateway-FQDN wijzigen

Als het VPN-apparaat waarmee u verbinding wilt maken, de FQDN-naam (Fully Qualified Domain Name) heeft gewijzigd, moet u de lokale netwerk gateway wijzigen om deze wijziging weer te geven.

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Wijzig de domein naam in het vak **FQDN** .
3. Klik op **Opslaan** om de instellingen op te slaan.

> ! ERAAN U kunt een lokale netwerk gateway niet wijzigen tussen het FQDN-eind punt en het eind punt van het IP-adres. U moet alle verbindingen verwijderen die zijn gekoppeld aan deze lokale netwerk gateway, een nieuwe maken met het nieuwe eind punt (IP-adres of FQDN-naam) en vervolgens de verbindingen opnieuw maken.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-adres voorvoegsels wijzigen

### <a name="to-add-additional-address-prefixes"></a>Ga als volgt te werk om aanvullende voorvoegsels toe te voegen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Voeg de IP-adres ruimte toe in het vak *extra adres bereik toevoegen* .
3. Klik op **Opslaan** om uw instellingen op te slaan.

### <a name="to-remove-address-prefixes"></a>Ga als volgt te werk om adresvoorvoegsels te verwijderen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Klik op de regel **'... '** van het voor voegsel dat u wilt verwijderen.
3. Klik op **Verwijderen**.
4. Klik op **Opslaan** om uw instellingen op te slaan.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>BGP-instellingen wijzigen

### <a name="to-add-or-update-bgp-settings"></a>BGP-instellingen toevoegen of bijwerken:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Selecteer **BGP-instellingen configureren** om de BGP-configuraties voor deze lokale netwerk gateway weer te geven of bij te werken
3. Het autonoom systeem nummer of het IP-adres van de BGP-peer toevoegen of bijwerken in de bijbehorende velden
4. Klik op **Opslaan** om uw instellingen op te slaan.

### <a name="to-remove-bgp-settings"></a>De BGP-instellingen verwijderen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Schakel het selectie vakje **BGP-instellingen configureren** uit om het bestaande IP-adres van de BGP-ASN en BGP-peer te verwijderen
3. Klik op **Opslaan** om uw instellingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

U kunt uw gateway verbinding controleren. Zie [een gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).
