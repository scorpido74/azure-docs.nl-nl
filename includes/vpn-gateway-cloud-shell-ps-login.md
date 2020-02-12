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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133599"
---
Open de Power shell-console met verhoogde bevoegdheden.

Als u Azure PowerShell lokaal uitvoert, maakt u verbinding met uw Azure-account. Met de cmdlet *Connect-AzAccount* wordt u gevraagd referenties op te vragen. Na de verificatie worden uw account instellingen gedownload zodat ze beschikbaar zijn voor Azure PowerShell. Als u in plaats daarvan Azure Cloud Shell gebruikt, hoeft u *Connect-AzAccount*niet uit te voeren. Azure Cloud Shell maakt automatisch verbinding met uw Azure-account.

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