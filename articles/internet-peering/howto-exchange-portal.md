---
title: Een Exchange-peering maken of wijzigen met behulp van de portal
titleSuffix: Azure
description: Een Exchange-peering maken of wijzigen met behulp van de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774573"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Een Exchange-peering maken of wijzigen met behulp van de portal

In dit artikel wordt beschreven hoe u een micro soft Exchange-peering maakt met behulp van de portal. In dit artikel wordt ook uitgelegd hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en [Exchange-peering-instructies](walkthrough-exchange-all.md) voordat u begint met de configuratie.
* Als u Exchange-peers met micro soft al hebt, die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde uitwisseling van Exchange naar Azure-resource converteren met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Een Exchange-peering maken en inrichten

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Meld u aan bij de portal en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Een Exchange-peering maken

U kunt een nieuwe aanvraag voor peering maken met behulp van **peering** resource.

#### <a name="launch-resource-and-configure-basic-settings"></a>Resource starten en basis instellingen configureren
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Verbindingen configureren en verzenden
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Een Exchange-peering controleren
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Een Exchange-peering wijzigen
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Inrichting van een Exchange-peering ongedaan maken
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)
* [Een verouderde directe peering naar een Azure-resource converteren met behulp van de portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
