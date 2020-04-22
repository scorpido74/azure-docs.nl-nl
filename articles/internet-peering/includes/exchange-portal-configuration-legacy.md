---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678583"
---
1. Vul op de pagina **Een peering maken** op het tabblad **Configuratie** de vakken in zoals hier weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Een Peering-pagina Exchange-peeringtype maken](../media/setup-exchange-conf-tab.png)

    * Selecteer **Exchange** **voor peeringtype**.
    * Selecteer **SKU** als **Basisvrij**.
    * Selecteer de **metrolocatie** waar u peering wilt converteren naar een Azure-bron. Als u peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** die niet zijn geconverteerd naar een Azure-bron, worden deze verbindingen weergegeven in de sectie **Peering-verbindingen** zoals weergegeven. U deze peeringverbindingen nu converteren naar een Azure-bron.

        > [!div class="mx-imgBorder"]
        > ![Lijst met peeringverbindingen](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > U de instellingen voor oudere peering-verbindingen niet wijzigen. Als u extra peeringverbindingen met Microsoft wilt toevoegen op de geselecteerde **Metro-locatie,** selecteert u **Nieuw maken**. Zie [Een Exchange-peering maken of wijzigen met behulp van de portal](../howto-exchange-portal.md)voor meer informatie.
        >

1. Selecteer **Controleren + maken**. Merk op dat de portal de basisvalidatie van de ingevoerde informatie uitvoert. Een lint bovenaan geeft het bericht *Laatste validatie uit...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringvalidatie](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in *Validatie is geslaagd,* controleert u uw gegevens. Dien de aanvraag in door **Maken te**selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **Vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering indiening](../media/setup-exchange-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt weergegeven zoals weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering succes](../media/setup-direct-success.png)
