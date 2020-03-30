---
title: Overzicht van Exchange-peering
titleSuffix: Azure
description: Overzicht van Exchange-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775171"
---
# <a name="exchange-peering-walkthrough"></a>Overzicht van Exchange-peering

In dit gedeelte worden de stappen uitgelegd die u moet volgen om een Exchange-peering in te stellen en te beheren.

## <a name="create-an-exchange-peering"></a>Een Exchange-peering maken
> [!div class="mx-imgBorder"]
> ![Exchange-werkstroom en verbindingsstatus](./media/exchange-peering.png)

De volgende stappen moeten worden gevolgd om een Exchange-peering te bieden:
1. Bekijk [het peeringbeleid van](https://peering.azurewebsites.net/peering) Microsoft om inzicht te krijgen in de vereisten voor Exchange-peering.
1. Microsoft-peeringlocatie en peering-faciliteits-id zoeken in [PeeringDB](https://www.peeringdb.com/net/694)
1. Exchange-peering aanvragen voor een peeringlocatie met behulp van de instructies in [Exchange-peering maken en wijzigen met PowerShell-artikel](howto-exchange-powershell.md) voor meer informatie.
1. Nadat u een peering-aanvraag hebt ingediend, controleert Microsoft het verzoek en neemt u indien nodig contact met u op.
1. Na goedkeuring wijzigingen in de verbindingsstatus in Goedgekeurd
1. BGP-sessie aan het einde configureren en Microsoft hiervan op de hoogte stellen
1. We zullen BGP-sessie voorzien met DENY ALL-beleid en end-to-end valideren.
1. Als dit is gelukt, ontvangt u een melding dat de status peering-verbinding actief is.
1. Het verkeer wordt dan toegestaan door de nieuwe peering.

Houd er rekening mee dat verbindingsstatussen niet mogen worden verward met standaard [BGP-sessiestatussen.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren in een Azure-resource
De volgende stappen moeten worden gevolgd om een verouderde Exchange-peering om te zetten in Azure-bron:
1. De instructies volgen in [Een verouderde Exchange-peering converteren naar Azure-bron](howto-legacy-exchange-powershell.md)
1. Nadat u de conversieaanvraag hebt ingediend, controleert Microsoft het verzoek en neemt u indien nodig contact met u op.
1. Na goedkeuring ziet u uw Exchange-peering met de verbindingsstatus als Actief.

## <a name="deprovision-exchange-peering"></a>Exchange-peering deprovisionen
Neem contact op met [Microsoft peering](mailto:peering@microsoft.com) om Exchange-peering te deprovisioneren.

Wanneer een Exchange-peering is ingesteld voor deprovision, ziet u de verbindingsstatus als **PendingRemove**

> [!NOTE]
> Als u PowerShell-cmdlet uitvoert om het Exchange-peering te verwijderen wanneer de verbindingsstatus ProvisioningStarted of ProvisioningCompleted is, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [vereisten voor het instellen van peering met Microsoft](prerequisites.md).
