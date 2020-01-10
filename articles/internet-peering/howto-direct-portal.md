---
title: Een directe peering maken of wijzigen met behulp van de portal
titleSuffix: Azure
description: Een directe peering maken of wijzigen met behulp van de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775327"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Een directe peering maken of wijzigen met behulp van de portal

In dit artikel wordt beschreven hoe u een micro soft direct-peering maakt met behulp van de portal. In dit artikel wordt ook uitgelegd hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [direct peering-scenario](walkthrough-direct-all.md) voordat u begint met de configuratie.
* Als u rechtstreeks peering met micro soft hebt, die niet wordt geconverteerd naar Azure-resources, raadpleegt u [een verouderde directe peering converteren naar Azure-resource met behulp van de portal](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Een directe peering maken en inrichten

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Een directe peering maken

U kunt een nieuwe aanvraag voor peering maken met behulp van **peering** resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Een directe peering wijzigen
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Inrichting van een directe peering ongedaan maken
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Maak of wijzig Exchange-peering met behulp van de portal](howto-exchange-portal.md).
* [Converteer een verouderde uitwisseling van Exchange naar Azure-resource met behulp van de portal](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
