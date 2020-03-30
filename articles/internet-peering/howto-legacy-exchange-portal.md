---
title: Een verouderde Direct-peering converteren in een Azure-resource met de portal
titleSuffix: Azure
description: Een verouderde Direct-peering converteren in een Azure-resource met de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775197"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Een verouderde Exchange-peering converteren in een Azure-resource met de portal

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar Azure-bron met behulp van de portal.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-legacy-exchange-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren in een Azure-resource

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Oudere Exchange-peering converteren

U oudere peeringverbindingen converteren met **peering-bron.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange-peering verifiëren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of bewerken met de portal](howto-exchange-portal.md)
