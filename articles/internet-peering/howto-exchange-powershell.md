---
title: Een Exchange-peering maken of wijzigen met behulp van Power shell
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 1c0620875e7da4582156080c4a52fd6fbe0e573c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84700243"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Een Exchange-peering maken of wijzigen met behulp van Power shell

In dit artikel wordt beschreven hoe u een micro soft Exchange-peering maakt met behulp van Power shell-cmdlets en het Resource Manager-implementatie model. In dit artikel leest u ook hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) en de [Exchange peering-walkthrough](walkthrough-exchange-all.md) voordat u begint met de configuratie.
* Als u al Exchange-peers met micro soft hebt die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>De lijst met ondersteunde peering locaties voor uitwisseling van peering ophalen
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Een Exchange-peering maken
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange-peering ophalen
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Inrichting van een Exchange-peering ongedaan maken

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md)
* [Een verouderde directe peering naar een Azure-resource converteren met behulp van Power shell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle para meters verkrijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.
