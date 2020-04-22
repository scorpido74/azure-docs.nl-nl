---
title: Een direct peering maken of wijzigen met behulp van de Azure-portal
titleSuffix: Azure
description: Een direct peering maken of wijzigen met behulp van de Azure-portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681064"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Een direct peering maken of wijzigen met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u een Microsoft Direct-peering maakt met behulp van de Azure-portal. In dit artikel ziet u ook hoe u de status van de bron controleren, bijwerken of verwijderen en deprovisioneren.

Als u dat liever hebt, u deze handleiding voltooien met Azure [PowerShell.](howto-direct-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [direct peering-walkthrough](walkthrough-direct-all.md) voordat u met de configuratie begint.
* Zie [Een verouderde direct-peering naar een Azure-bron converteren naar een Azure-bron met behulp van de portal](howto-legacy-direct-portal.md)als u al directe peering-verbindingen met Microsoft hebt.

## <a name="create-and-provision-a-direct-peering"></a>Een direct peering maken en inrichten

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Een direct peering maken

U een nieuwe peeringaanvraag maken met behulp van de **peering-bron.**

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

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.
