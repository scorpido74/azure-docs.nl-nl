---
title: Een Direct-peering maken of wijzigen met PowerShell
titleSuffix: Azure
description: Een Direct-peering maken of wijzigen met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680808"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Een Direct-peering maken of wijzigen met PowerShell

In dit artikel wordt beschreven hoe u een Microsoft Direct-peering maakt met PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [Azure-portal.](howto-direct-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [direct peering-walkthrough](walkthrough-direct-all.md) voordat u met de configuratie begint.
* Zie [Een verouderde direct-peering naar een Azure-bron converteren naar een Azure-bron met PowerShell](howto-legacy-direct-powershell.md)als u al directe peeringverbindingen met Microsoft hebt.

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Een direct peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Download de lijst met ondersteunde peeringlocaties voor Direct-peering
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Een direct peering maken
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct peering verifiëren
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Een direct peering wijzigen
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Een direct peering deprovisionen
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met PowerShell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.
