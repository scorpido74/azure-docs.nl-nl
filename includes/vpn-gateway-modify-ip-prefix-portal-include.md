---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175871"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - geen gatewayverbinding

#### <a name="to-add-additional-address-prefixes"></a>Ga als volgt te werk om aanvullende voorvoegsels toe te voegen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Voeg de IP-adres ruimte toe in het vak *extra adres bereik toevoegen* .
3. Klik op **Opslaan** om uw instellingen op te slaan.

#### <a name="to-remove-address-prefixes"></a>Ga als volgt te werk om adresvoorvoegsels te verwijderen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Klik op de regel **'... '** van het voor voegsel dat u wilt verwijderen.
3. Klik op **Verwijderen**.
4. Klik op **Opslaan** om uw instellingen op te slaan.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - bestaande gatewayverbinding

Als u een gatewayverbinding hebt en u IP-adresvoorvoegsels wilt toevoegen aan of verwijderen uit uw lokale netwerkgateway, moet u de volgende stappen uitvoeren in de volgorde waarin ze staan vermeld. Dit veroorzaakt enige downtime in uw VPN-verbinding. Als u IP-adresvoorvoegsels wijzigt, hoeft u de VPN-gateway niet te verwijderen. U hoeft alleen de verbinding te verwijderen.

#### <a name="1-remove-the-connection"></a>1. Verwijder de verbinding.

1. Klik op de lokale netwerk gateway resource in de sectie **instellingen** op **verbindingen**.
2. Klik op het **..** . op de regel voor elke verbinding en klik vervolgens op **verwijderen**.
3. Klik op **Opslaan** om uw instellingen op te slaan.

#### <a name="2-modify-the-address-prefixes"></a>2. Wijzig de adres voorvoegsels.

Ga als volgt te werk om aanvullende voorvoegsels toe te voegen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Voeg de IP-adres ruimte toe.
3. Klik op **Opslaan** om uw instellingen op te slaan.

Ga als volgt te werk om adresvoorvoegsels te verwijderen:

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Klik op het **..** . op de regel met het voor voegsel dat u wilt verwijderen.
3. Klik op **Verwijderen**.
4. Klik op **Opslaan** om uw instellingen op te slaan.

#### <a name="3-recreate-the-connection"></a>3. Maak de verbinding opnieuw.

1. Ga naar de Virtual Network-gateway voor uw VNet. (Niet de lokale netwerk gateway.)
2. Klik op de Virtual Network-gateway in de sectie **instellingen** op **verbindingen**.
3. Klik op de **+ toevoegen** om de Blade **verbinding toevoegen** te openen.
4. Maak de verbinding opnieuw.
5. Klik op **OK** om de verbinding te maken.