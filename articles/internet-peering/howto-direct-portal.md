---
title: Direct-peering maken of bewerken met de portal
titleSuffix: Azure
description: Direct-peering maken of bewerken met de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775327"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Direct-peering maken of bewerken met de portal

In dit artikel wordt beschreven hoe u een Microsoft Direct-peering maakt met behulp van de portal. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-direct-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [directe peering-walkthrough](walkthrough-direct-all.md) voordat u met de configuratie begint.
* Als u al direct peeren met Microsoft hebt, die niet zijn geconverteerd naar Azure-bronnen, verwijzen naar [Een verouderde directe peering converteren naar Azure-bron met behulp van de portal](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Een direct peering maken en inrichten

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Een direct peering maken

U een nieuwe peeringaanvraag maken met behulp van **peering-bron.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct peering verifiëren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Een direct peering wijzigen
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Een direct peering deprovisionen
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md).
* [Een verouderde Exchange-peering converteren naar Azure-bron met behulp van de portal](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)
