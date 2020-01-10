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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775054"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal

In dit artikel wordt beschreven hoe u een bestaande verouderde directe peering converteert naar een Azure-resource met behulp van de portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [direct peering-scenario](walkthrough-direct-all.md) voordat u begint met de configuratie.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Verouderde directe peering converteren naar Azure-resource

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Verouderde directe peering converteren

U kunt verouderde peering-verbindingen converteren met behulp van **peering** -resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).
