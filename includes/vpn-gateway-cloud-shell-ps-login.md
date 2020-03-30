---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133599"
---
Open uw PowerShell-console met verhoogde bevoegdheden.

Als u Azure PowerShell lokaal uitvoert, maakt u verbinding met uw Azure-account. De *cmdlet Connect-AzAccount* vraagt u om referenties. Na verificatie worden uw accountinstellingen gedownload, zodat deze beschikbaar zijn voor Azure PowerShell. Als u azure cloud shell gebruikt, hoeft u *Connect-AzAccount*niet uit te voeren. Azure Cloud Shell maakt automatisch verbinding met uw Azure-account.

```azurepowershell
Connect-AzAccount
```

Als u meer dan één abonnement hebt, krijgt u een lijst met uw Azure-abonnementen.

```azurepowershell-interactive
Get-AzSubscription
```

Geef het abonnement op dat u wilt gebruiken.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```