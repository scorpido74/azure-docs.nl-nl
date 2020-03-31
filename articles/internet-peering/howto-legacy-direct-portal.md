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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775054"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Een verouderde Direct-peering converteren in een Azure-resource met de portal

In dit artikel wordt beschreven hoe u een bestaande verouderde direct peering converteert naar Azure-bron met behulp van de portal.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-legacy-direct-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [directe peering-walkthrough](walkthrough-direct-all.md) voordat u met de configuratie begint.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Verouderde direct-peering converteren naar Azure-bron

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Verouderde direct peering converteren

U oudere peeringverbindingen converteren met **peering-bron.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct peering verifiëren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een direct peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).
