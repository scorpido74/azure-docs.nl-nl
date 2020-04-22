---
title: Een verouderde Direct-peering converteren naar een Azure-bron met behulp van de Azure-portal
titleSuffix: Azure
description: Een verouderde Direct-peering converteren naar een Azure-bron met behulp van de Azure-portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678829"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Een verouderde Direct-peering converteren naar een Azure-bron met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u een bestaande verouderde direct peering converteert naar een Azure-bron met behulp van de Azure-portal.

Als u wilt, u deze handleiding voltooien met [PowerShell.](howto-legacy-direct-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [direct peering-walkthrough](walkthrough-direct-all.md) voordat u met de configuratie begint.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Een verouderde Direct-peering converteren naar een Azure-bron

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Een verouderde Direct-peering converteren

U oudere peeringverbindingen converteren met behulp van de **peering-bron.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>De bron starten en basisinstellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct peering verifiëren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Direct-peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
