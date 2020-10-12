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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175865"
---
U kunt controleren of uw verbinding is geslaagd met behulp van de opdracht [az network vpn-connection show](/cli/azure/network/vpn-connection). In het voorbeeld verwijst "--name" naar de naam van de verbinding die u wilt testen. Zolang het tot stand brengen van de verbinding bezig is, is de verbindingsstatus "Verbinding maken". Wanneer de verbinding tot stand is gebracht, verandert de status in "Verbonden".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
