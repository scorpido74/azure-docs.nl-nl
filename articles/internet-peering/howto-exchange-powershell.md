---
title: Een Exchange-peering maken of wijzigen met PowerShell
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678601"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Een Exchange-peering maken of wijzigen met PowerShell

In dit artikel wordt beschreven hoe u een Microsoft Exchange-peering maakt met PowerShell-cmdlets en het implementatiemodel Resource Manager. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [Azure-portal.](howto-exchange-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.
* Zie [Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell](howto-legacy-exchange-powershell.md)als u al Exchange-peerings met Microsoft hebt.

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>De lijst met ondersteunde peeringlocaties voor Exchange-peering
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Een Exchange-peering maken
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange-peering genereren
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Een Exchange-peering deprovisionen

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een Direct-peering maken of wijzigen met PowerShell](howto-direct-powershell.md)
* [Een verouderde Direct-peering converteren naar een Azure-bron met PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.
