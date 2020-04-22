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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680961"
---
1. Vul op de pagina **Een peering maken** op het tabblad **Configuratie** de vakken in zoals weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Een Peering-pagina Exchange-peeringtype maken](../media/setup-exchange-conf-tab.png)

    * Selecteer **Exchange** **voor peeringtype**.
    * Selecteer **SKU** als **Basisvrij**.
    * Selecteer de **metrolocatie** waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** en u de portal voor het eerst gebruikt om peering op die locatie in te stellen, worden uw bestaande peeringverbindingen weergegeven in de sectie **Peering-verbindingen** zoals weergegeven. Microsoft converteert deze peeringverbindingen automatisch naar een Azure-bron, zodat u ze samen met de nieuwe verbindingen op één plek beheren. Zie [Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de portal](../howto-legacy-exchange-portal.md)voor meer informatie.
        >

1. Selecteer **onder Peering-verbindingen**De optie **Nieuw maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u verbindingsinstellingen wilt configureren of wijzigen, selecteert u de knop Bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Knop Bewerken](../media/setup-exchange-conf-tab-edit.png)

    * Als u een regel wilt verwijderen, selecteert u **...**  >  **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![De knop Verwijderen](../media/setup-exchange-conf-tab-delete.png)

    * U moet alle instellingen voor een verbinding opgeven, zoals hier wordt weergegeven.

         > [!div class="mx-imgBorder"]
         > ![Pagina Exchange Peering Connection](../media/setup-exchange-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waar de verbinding moet worden ingesteld.
        1. Voer in de vakken **IPv4-adres** en **IPv6** respectievelijk de IPv4- en IPv6-adressen in die in Microsoft-routers worden geconfigureerd met behulp van de neighbor-opdracht.
        1. Voer het aantal IPv4- en IPv6-voorvoegsels in dat u adverteert in respectievelijk de **maximaal geadverteerde IPv4-adressen** en de maximaal **geadverteerde IPv6-adressen.**
        1. Selecteer **OK** om uw verbindingsinstellingen op te slaan.

1. Herhaal de stap om meer verbindingen toe te voegen in elke faciliteit waar Microsoft is verbonden met uw netwerk, binnen de eerder geselecteerde **Metro.**

1. Nadat u alle vereiste verbindingen hebt toegevoegd, selecteert u **Controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringconfiguratie](../media/setup-exchange-conf-tab-final.png)

1. Merk op dat de portal de basisvalidatie van de ingevoerde informatie uitvoert. Een lint bovenaan geeft het bericht *Laatste validatie uit...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringvalidatie](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in *Validatie is geslaagd,* controleert u uw gegevens. Dien de aanvraag in door **Maken te**selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **Vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering indiening](../media/setup-exchange-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt weergegeven zoals hier wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering succes](../media/setup-direct-success.png)
