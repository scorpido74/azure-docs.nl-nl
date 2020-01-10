---
title: Peering-service inschakelen voor directe peering met behulp van Power shell
titleSuffix: Azure
description: Peering-service inschakelen voor directe peering met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774183"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Peering-service inschakelen voor directe peering met behulp van Power shell

In dit artikel wordt beschreven hoe u [peering service](overview-peering-service.md) inschakelt op een directe peering met behulp van Power shell-cmdlets en het Resource Manager-implementatie model.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) voordat u begint met de configuratie.
* Kies een directe peering in uw abonnement waarvoor u de peering-service wilt inschakelen. Als u er nog geen hebt, kunt u een verouderde directe peering converteren of een nieuwe directe peering maken.
    * Als u een verouderde directe peering wilt converteren, volgt u de instructies in [een verouderde directe peering converteren naar Azure-resource met behulp van Power shell](howto-legacy-direct-powershell.md).
    * Als u een nieuwe directe peering wilt maken, volgt u de instructies in [een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering-service inschakelen op directe peering

### <a name= get></a>Directe peering weer geven
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>De directe peering inschakelen voor de peering-service

Nadat u direct peering in de vorige stap hebt ontvangen, schakelt u deze in voor de peering-service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Een directe peering-verbinding wijzigen

Als u de verbindings instellingen wilt wijzigen, raadpleegt u **de sectie direct peering wijzigen** in [een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md)

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van Power shell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar Azure-resource met behulp van Power shell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende resources
U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen over peering-service](service-faqs.md)voor meer informatie.