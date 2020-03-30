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
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175870"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Het IP-adres van de lokale netwerkgateway wijzigen - geen gatewayverbinding

Gebruik het voorbeeld om een lokale netwerkgateway die geen gatewayverbinding heeft te wijzigen. Wanneer u deze waarde wijzigt, kunt u tegelijkertijd ook de adresvoorvoegsels wijzigen.

1. Klik in de bron Lokale netwerkgateway in de sectie **Instellingen** op **Configuratie**.
2. Wijzig in het vak **IP-adres** het IP-adres.
3. Klik op **Opslaan** om de instellingen op te slaan.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Het IP-adres van de lokale netwerkgateway wijzigen - bestaande gatewayverbinding

Als u een lokale netwerkgateway met een verbinding wilt wijzigen, moet u eerst de verbinding verwijderen. Nadat de verbinding is verwijderd, kunt u het IP-adres van de gateway wijzigen en een nieuwe verbinding maken. U kunt tegelijkertijd ook de adresvoorvoegsels wijzigen. Dit veroorzaakt enige downtime in uw VPN-verbinding. Als u het IP-adres van de gateway wijzigt, hoeft u de VPN-gateway niet te verwijderen. U hoeft alleen de verbinding te verwijderen.
 
#### <a name="1-remove-the-connection"></a>1. Verwijder de verbinding.

1. Klik in de bron Lokale netwerkgateway in de sectie **Instellingen** op **Verbindingen**.
2. Klik op de **...** op de lijn voor de verbinding en klik vervolgens op **Verwijderen**.
3. Klik **op Opslaan** om uw instellingen op te slaan.

#### <a name="2-modify-the-ip-address"></a>2. Wijzig het IP-adres.

U kunt tegelijkertijd ook de adresvoorvoegsels wijzigen.

1. Wijzig in het vak **IP-adres** het IP-adres.
2. Klik op **Opslaan** om de instellingen op te slaan.

#### <a name="3-recreate-the-connection"></a>3. Maak de verbinding opnieuw.

1. Navigeer naar de Virtuele Netwerkgateway voor uw VNet. (Niet de lokale netwerkgateway.)
2. Klik in de virtuele netwerkgateway in de sectie **Instellingen** op **Verbindingen**.
3. Klik **op** het + Toevoegen om het **verbindingsblad toevoegen** te openen.
4. Maak je verbinding opnieuw.
5. Klik op **OK** om de verbinding te maken.