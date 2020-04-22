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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678833"
---
1. Vul op de pagina **Een peering maken** op het tabblad **Configuratie** de vakken in zoals hier weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Configuratie van een peeringpagina maken](../media/setup-direct-conf-tab.png)

    * Selecteer **Direct** **voor peeringtype**.
    * Selecteer **AS8075**voor **het Microsoft-netwerk**. Selecteer ASN 8069 niet. Het is gereserveerd voor speciale toepassingen en alleen gebruikt door [Microsoft peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als **Basisvrij**. Selecteer Premium Free niet omdat het is gereserveerd voor speciale toepassingen.
    * Selecteer de **metrolocatie** waar u peering wilt converteren naar een Azure-bron. Als u peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** die niet zijn geconverteerd naar een Azure-bron, worden deze verbindingen weergegeven in de sectie **Peering-verbindingen** zoals weergegeven. U deze peeringverbindingen nu converteren naar een Azure-bron.

        > [!div class="mx-imgBorder"]
        > ![Lijst met peeringverbindingen](../media/setup-directlegacy-conf-tab.png)

1. Als u bandbreedte wilt bijwerken, selecteert u de knop Bewerken voor een regel om verbindingsinstellingen te wijzigen.

    > [!div class="mx-imgBorder"]
    > ![Knop Bewerken](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Als u extra peeringverbindingen met Microsoft wilt toevoegen op de geselecteerde **Metro-locatie,** selecteert u **Nieuw maken**. Zie [Een direct peeren maken of wijzigen met behulp van de portal](../howto-direct-portal.md)voor meer informatie.
    >

1. Selecteer **Controleren + maken**. Merk op dat de portal de basisvalidatie van de ingevoerde informatie uitvoert. Een lint bovenaan geeft het bericht *Laatste validatie uit...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringvalidatie](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in *Validatie is geslaagd,* controleert u uw gegevens. Dien de aanvraag in door **Maken te**selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **Vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering indiening](../media/setup-direct-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt weergegeven zoals hier wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering succes](../media/setup-direct-success.png)
