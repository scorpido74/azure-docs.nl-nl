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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175870"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Het IP-adres van de lokale netwerk gateway wijzigen-geen gateway verbinding

Gebruik het voorbeeld om een lokale netwerkgateway die geen gatewayverbinding heeft te wijzigen. Wanneer u deze waarde wijzigt, kunt u tegelijkertijd ook de adresvoorvoegsels wijzigen.

1. Klik op **configuratie**op de lokale netwerk gateway resource in de sectie **instellingen** .
2. Wijzig het IP-adres in het vak **IP-adres** .
3. Klik op **Opslaan** om de instellingen op te slaan.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Het IP-adres van de lokale netwerk gateway wijzigen-bestaande gateway verbinding

Als u een lokale netwerk gateway met een verbinding wilt wijzigen, moet u eerst de verbinding verwijderen. Nadat de verbinding is verwijderd, kunt u het IP-adres van de gateway wijzigen en een nieuwe verbinding maken. U kunt tegelijkertijd ook de adresvoorvoegsels wijzigen. Dit veroorzaakt enige downtime in uw VPN-verbinding. Als u het IP-adres van de gateway wijzigt, hoeft u de VPN-gateway niet te verwijderen. U hoeft alleen de verbinding te verwijderen.
 
#### <a name="1-remove-the-connection"></a>1. Verwijder de verbinding.

1. Klik op de lokale netwerk gateway resource in de sectie **instellingen** op **verbindingen**.
2. Klik op het **..** . op de regel voor de verbinding en klik vervolgens op **verwijderen**.
3. Klik op **Opslaan** om uw instellingen op te slaan.

#### <a name="2-modify-the-ip-address"></a>2. Wijzig het IP-adres.

U kunt tegelijkertijd ook de adresvoorvoegsels wijzigen.

1. Wijzig het IP-adres in het vak **IP-adres** .
2. Klik op **Opslaan** om de instellingen op te slaan.

#### <a name="3-recreate-the-connection"></a>3. Maak de verbinding opnieuw.

1. Ga naar de Virtual Network-gateway voor uw VNet. (Niet de lokale netwerk gateway.)
2. Klik op de Virtual Network-gateway in de sectie **instellingen** op **verbindingen**.
3. Klik op de **+ toevoegen** om de Blade **verbinding toevoegen** te openen.
4. Maak de verbinding opnieuw.
5. Klik op **OK** om de verbinding te maken.