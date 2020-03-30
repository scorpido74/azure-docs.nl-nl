---
title: Exchange-peering maken of bewerken met de portal
titleSuffix: Azure
description: Exchange-peering maken of bewerken met de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774573"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Exchange-peering maken of bewerken met de portal

In dit artikel wordt beschreven hoe u een Microsoft Exchange-peering maakt met behulp van de portal. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-exchange-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.
* Als u Exchange-peeringen met Microsoft al hebt, die niet zijn geconverteerd naar Azure-bronnen, verwijzen naar [Een verouderde Exchange-peering converteren naar Azure-bron met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Een Exchange-peering maken

U een nieuwe peeringaanvraag maken met behulp van **peering-bron.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Een Exchange-peering verifiëren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Een Exchange-peering deprovisionen
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Direct-peering maken of bewerken met de portal](howto-direct-portal.md)
* [Een verouderde Direct-peering converteren in een Azure-resource met de portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)
