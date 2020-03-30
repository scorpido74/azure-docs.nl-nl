---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c1b5560e16b68565c37365ac9c2cba217d9b1b90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175867"
---
U controleren of uw verbinding is geslaagd met de cmdlet 'Get-AzureVNetConnection'.

1. Gebruik het volgende cmdlet-voorbeeld om de waarden aan te passen aan uw eigen waarden. De naam van het virtuele netwerk moet tussen aanhalingstekens staan als het spaties bevat.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
   ```
2. Bekijk de waarden nadat de cmdlet is voltooid. In het onderstaande voorbeeld wordt de verbindingsstatus weergegeven als 'Verbonden' en ziet u binnenkomende en uitgaande bytes.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal