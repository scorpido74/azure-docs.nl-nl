---
title: Overzicht van Direct peering
titleSuffix: Azure
description: Overzicht van Direct peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775509"
---
# <a name="direct-peering-walkthrough"></a>Overzicht van Direct peering

In dit gedeelte worden de stappen uitgelegd die u moet volgen om een direct peering in te stellen en te beheren.

## <a name="create-a-direct-peering"></a>Een direct peering maken
> [!div class="mx-imgBorder"]
> ![Direct peering-werkstroom en verbindingsstatus](./media/direct-peering.png)

De volgende stappen moeten worden gevolgd om een Direct peering te voorzien:
1. Bekijk het [peeringbeleid van](https://peering.azurewebsites.net/peering) Microsoft om inzicht te krijgen in de vereisten voor direct peering.
1. Volg de instructies in [Een direct peering maken of wijzigen](howto-direct-powershell.md) om een peering-aanvraag in te dienen.
1. Nadat u een peering-verzoek hebt ingediend, neemt Microsoft contact op met uw geregistreerde e-mailadres om LOA (Autorisatiebrief) of voor andere informatie te verstrekken.
1. Zodra peering-aanvraag is goedgekeurd, wordt de verbindingsstatus gewijzigd in ProvisioningStarted.
1. Je moet:
    1. volledige bedrading volgens de LOA
    1. (optioneel) linktest uitvoeren met 169.254.0.0/16
    1. BGP-sessie configureren en ons vervolgens op de hoogte stellen.
1. Microsoft voorziet in Een BGP-sessie met DENY ALL-beleid en valideert end-to-end.
1. Als dit is gelukt, ontvangt u een melding dat de status peering-verbinding actief is.
1. Het verkeer wordt dan toegestaan door de nieuwe peering.

Houd er rekening mee dat verbindingsstatussen niet mogen worden verward met standaard [BGP-sessiestatussen.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Een verouderde Direct-peering converteren in een Azure-resource
De volgende stappen moeten worden gevolgd om een verouderde directe peering om te zetten in Azure-bron:
1. De instructies volgen in [Een verouderde direct peering naar Azure-bron converteren](howto-legacy-direct-powershell.md)
1. Nadat u de conversieaanvraag hebt ingediend, controleert Microsoft het verzoek en neemt u indien nodig contact met u op.
1. Na goedkeuring ziet u uw direct peering met een verbindingsstatus als Actief.

## <a name="deprovision-direct-peering"></a>Deprovision Direct-peering
Neem contact op met [Microsoft peering](mailto:peering@microsoft.com) team om direct peering te deprovisionen.

Wanneer een direct peering is ingesteld voor deprovision, ziet u de verbindingsstatus als **PendingRemove**

> [!NOTE]
> Als u PowerShell-cmdlet uitvoert om het direct peering te verwijderen wanneer de verbindingstaat is voorzienGestart of ProvisioningCompleted, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [vereisten voor het instellen van peering met Microsoft](prerequisites.md).
