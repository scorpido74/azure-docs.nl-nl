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
ms.openlocfilehash: dd2dd84cbcd50fba48011e1836cdc64a6ad5855d
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040742"
---
U kunt controleren of uw verbinding is geslaagd met behulp van de cmdlet ' Get-AzureVNetConnection '.

1. Gebruik het volgende cmdlet-voorbeeld om de waarden aan te passen aan uw eigen waarden. De naam van het virtuele netwerk moet tussen aanhalings tekens staan als deze spaties bevat.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
   ```
2. Bekijk de waarden nadat de cmdlet is voltooid. In het onderstaande voor beeld wordt de connectiviteits status weer gegeven als verbonden en ziet u inkomende en uitgaande bytes.

```output
ConnectivityState         : Connected
EgressBytesTransferred    : 181664
IngressBytesTransferred   : 182080
LastConnectionEstablished : 1/7/2016 12:40:54 AM
LastEventID               : 24401
LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                            Connected.
LastEventTimeStamp        : 1/7/2016 12:40:54 AM
LocalNetworkSiteName      : RMVNetLocal
```
