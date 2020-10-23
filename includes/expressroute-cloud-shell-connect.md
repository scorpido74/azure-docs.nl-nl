---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175768"
---
 Als u Azure Cloud Shell gebruikt, wordt u automatisch aangemeld bij uw Azure-account nadat u op Probeer het nu hebt geklikt. Als u zich lokaal wil aanmelden, opent u de PowerShell-console met verhoogde rechten en voert u de cmdlet uit om verbinding te maken.

```azurepowershell
Connect-AzAccount
```

Als u meer dan één abonnement hebt, haalt u een lijst met uw abonnementen op.

```azurepowershell-interactive
Get-AzSubscription
```

Geef het abonnement op dat u wilt gebruiken.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```