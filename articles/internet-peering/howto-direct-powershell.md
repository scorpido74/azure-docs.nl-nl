---
title: Een directe peering maken of wijzigen met behulp van Power shell
titleSuffix: Azure
description: Een directe peering maken of wijzigen met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774235"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Een directe peering maken of wijzigen met behulp van Power shell

In dit artikel wordt beschreven hoe u een micro soft direct-peering maakt met behulp van Power shell-cmdlets en het Resource Manager-implementatie model. In dit artikel leest u ook hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [direct peering-scenario](walkthrough-direct-all.md) voordat u begint met de configuratie.
* Als u rechtstreeks peering met micro soft hebt, die niet wordt geconverteerd naar Azure-resources, raadpleegt u [een verouderde directe peering converteren naar Azure-resource met behulp van Power shell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Een directe peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>De lijst met ondersteunde peering locaties voor directe peering ophalen
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Een directe peering maken
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Een directe peering wijzigen
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Inrichting van een directe peering ongedaan maken
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Maak of wijzig Exchange-peering met behulp van Power shell](howto-exchange-powershell.md).
* [Converteer een verouderde uitwisseling van Exchange naar Azure-resource met behulp van Power shell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```powershell
Get-Help Get-AzPeering -detailed
```

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
