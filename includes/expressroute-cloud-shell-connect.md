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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175768"
---
 Als u de Azure Cloud Shell gebruikt, meldt u zich automatisch aan bij uw Azure-account nadat u op ' proberen ' hebt geklikt. Als u zich lokaal wilt aanmelden, opent u de Power shell-console met verhoogde bevoegdheden en voert u de cmdlet uit om verbinding te maken.

```azurepowershell
Connect-AzAccount
```

Als u meer dan één abonnement hebt, kunt u een lijst met uw Azure-abonnementen ophalen.

```azurepowershell-interactive
Get-AzSubscription
```

Geef het abonnement op dat u wilt gebruiken.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```