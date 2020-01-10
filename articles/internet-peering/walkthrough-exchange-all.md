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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775171"
---
# <a name="exchange-peering-walkthrough"></a>Overzicht van Exchange-peering

In deze sectie worden de stappen beschreven die u moet volgen om een uitwisseling van Exchange-peering in te stellen en te beheren.

## <a name="create-an-exchange-peering"></a>Een Exchange-peering maken
> [!div class="mx-imgBorder"]
> ![Exchange-peering werk stroom en verbindings status](./media/exchange-peering.png)

De volgende stappen moeten worden gevolgd om een uitwisseling van Exchange-peering in te richten:
1. Bekijk het micro soft- [beleid voor peering](https://peering.azurewebsites.net/peering) om inzicht te krijgen in de vereisten voor Exchange-peering.
1. Micro soft-peering-locatie en peering Facility-id zoeken in [PeeringDB](https://www.peeringdb.com/net/694)
1. Vraag uitwisseling van Exchange aan voor een peering-locatie met behulp van de instructies in [maken en een Exchange-peering wijzigen met behulp van Power shell](howto-exchange-powershell.md) -artikel voor meer informatie.
1. Nadat u een aanvraag voor peering hebt ingediend, controleert micro soft de aanvraag en neemt u contact met u op als dat nodig is.
1. Na goed keuring wordt de verbindings status gewijzigd in goedgekeurd
1. De BGP-sessie aan het eind configureren en micro soft op de hoogte stellen
1. Er wordt een BGP-sessie ingericht met alle beleids regels weigeren en end-to-end valideren.
1. Als dit lukt, ontvangt u een melding dat de verbindings status van de peering actief is.
1. Het verkeer wordt dan via de nieuwe peering toegestaan.

Houd er rekening mee dat verbindings statussen niet moeten worden verward met standaard [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) -sessie statussen.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren naar Azure-resource
De volgende stappen moeten worden gevolgd om een verouderde uitwisseling van Exchange naar Azure-resource te converteren:
1. Volg de instructies in [een verouderde Exchange-peering converteren naar Azure resource](howto-legacy-exchange-powershell.md)
1. Nadat u de conversie aanvraag hebt ingediend, wordt de aanvraag door micro soft gecontroleerd en wordt u indien nodig contact met u opgenomen.
1. Na goed keuring ziet u de Exchange-peering met de verbindings status actief.

## <a name="deprovision-exchange-peering"></a>Inrichting van Exchange-peering ongedaan maken
Neem contact op met [micro soft-peering](mailto:peering@microsoft.com) voor het ongedaan maken van de inrichting van Exchange

Wanneer een uitwisseling van peering is ingesteld voor het ongedaan maken van de inrichting, wordt de verbindings status weer geven als **PendingRemove**

> [!NOTE]
> Als u de Power shell-cmdlet uitvoert om de Exchange-peering te verwijderen wanneer de verbindings status ProvisioningStarted of ProvisioningCompleted, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [vereisten voor het instellen van peering met micro soft](prerequisites.md).
