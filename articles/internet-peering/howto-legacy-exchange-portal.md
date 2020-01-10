---
title: Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal
titleSuffix: Azure
description: Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775197"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Een verouderde uitwisseling van Exchange naar Azure-resource converteren met behulp van de portal

In dit artikel wordt beschreven hoe u een bestaande verouderde uitwisseling van Exchange naar Azure-resource converteert met behulp van de portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en [Exchange-peering-instructies](walkthrough-exchange-all.md) voordat u begint met de configuratie.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren naar Azure-resource

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Verouderde uitwisseling van peering converteren

U kunt verouderde peering-verbindingen converteren met behulp van **peering** -resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Exchange-peering controleren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
