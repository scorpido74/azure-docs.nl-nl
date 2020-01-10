---
title: Een Exchange-peering maken of wijzigen met Power shell
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774404"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Een Exchange-peering maken of wijzigen met Power shell

In dit artikel wordt beschreven hoe u een micro soft Exchange-peering maakt met behulp van Power shell-cmdlets en het Resource Manager-implementatie model. In dit artikel leest u ook hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en [Exchange-peering-instructies](walkthrough-exchange-all.md) voordat u begint met de configuratie.
* Als u Exchange-peers met micro soft al hebt, die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde uitwisseling van Exchange naar Azure-resource converteren met behulp van Power shell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>De lijst met ondersteunde peering locaties voor uitwisseling van peering ophalen
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Een Exchange-peering maken
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Exchange-peering ophalen
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Inrichting van een Exchange-peering ongedaan maken

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md)
* [Een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```powershell
Get-Help Get-AzPeering -detailed
```

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
