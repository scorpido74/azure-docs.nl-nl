---
title: Een Exchange-peering maken of wijzigen met behulp van de Azure-portal
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met behulp van de Azure-portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680958"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Een Exchange-peering maken of wijzigen met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u een Microsoft Exchange-peering maakt met behulp van de Azure-portal. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u wilt, u deze handleiding voltooien met [PowerShell.](howto-exchange-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.
* Zie [Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de portal](howto-legacy-exchange-portal.md)als u al Exchange-peerings met Microsoft hebt.

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Een Exchange-peering maken

U een nieuwe peeringaanvraag maken met behulp van de **peering-bron.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>De bron starten en basisinstellingen configureren
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

* [Een Direct-peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
* [Een verouderde Direct-peering converteren naar een Azure-bron met behulp van de portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.
