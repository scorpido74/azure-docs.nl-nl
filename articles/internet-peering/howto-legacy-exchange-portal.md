---
title: Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de Azure-portal
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de Azure-portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678545"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar een Azure-bron met behulp van de Azure-portal.

Als u wilt, u deze handleiding voltooien met [PowerShell.](howto-legacy-exchange-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Een verouderde Exchange-peering converteren naar een Azure-bron

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Oudere Exchange-peering converteren

U oudere peeringverbindingen converteren met behulp van de **peering-bron.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>De bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange-peering verifiëren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
