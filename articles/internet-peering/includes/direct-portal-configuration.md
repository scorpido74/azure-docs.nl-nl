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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681070"
---
1. Vul op de pagina **Een peering maken** op het tabblad **Configuratie** de vakken in zoals hier weergegeven.

    * Selecteer **Direct** **voor peeringtype**.
    * Selecteer **AS8075**voor **het Microsoft-netwerk**. Maak geen peering met ASN 8069. Het is gereserveerd voor speciale toepassingen en alleen gebruikt door [Microsoft peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als **Basisvrij**. Selecteer Premium Free niet omdat het is gereserveerd voor speciale toepassingen.
    * Selecteer de **metrolocatie** waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** en u de Azure-portal voor het eerst gebruikt om peering op die locatie in te stellen, worden uw bestaande peeringverbindingen weergegeven in de sectie **Peering-verbindingen** zoals weergegeven. Microsoft converteert deze peeringverbindingen automatisch naar een Azure-bron, zodat u ze allemaal, samen met de nieuwe verbindingen, op één plaats beheren. Zie [Een verouderde direct peering converteren naar een Azure-bron met behulp van de portal](../howto-legacy-direct-portal.md)voor meer informatie.
        >

1. Selecteer **onder Peering-verbindingen**De optie **Nieuw maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u verbindingsinstellingen wilt configureren of wijzigen, selecteert u de knop Bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Knop Bewerken](../media/setup-direct-conf-tab-edit.png)
    
    * Als u een regel wilt verwijderen, selecteert u **...**  >  **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![De knop Verwijderen](../media/setup-direct-conf-tab-delete.png)

    * U moet alle instellingen voor een verbinding opgeven, zoals hier wordt weergegeven.

         > [!div class="mx-imgBorder"]
         > ![Pagina Direct-peering-verbinding](../media/setup-direct-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waar de verbinding moet worden ingesteld.
        1. **Sessieadresprovider** wordt gebruikt om te bepalen wie het subnet biedt dat nodig is om de BGP-sessie tussen uw netwerk en Microsoft in te stellen. Als u het subnet verstrekken, selecteert u **Peer**. Anders neemt u contact met U op met **Microsoft** en [Microsoft peering.](mailto:peering@microsoft.com) Als u deze optie kiest, duurt het langer voordat Microsoft de peering-aanvraag verwerkt. In sommige gevallen kan Microsoft mogelijk geen subnetten leveren, wat zal resulteren in weigering van aanvragen.
        1. Als u de optie **Sessieadresprovider** als **Peer**hebt geselecteerd, voert u respectievelijk de IPv4- en IPv6-adressen in, samen met de voorvoegmaskers in het **voorvoegsel van Sessie IPv4** en **het IPv6-voorvoegsel** van sessie.
        1. Voer het aantal IPv4- en IPv6-voorvoegsels in dat u adverteert in respectievelijk de **maximaal geadverteerde IPv4-adressen** en de maximaal **geadverteerde IPv6-adressen.**
        1. Pas de schuifregelaar **Totale bandbreedte** aan om de bandbreedte voor de verbinding weer te geven.
        1. Selecteer **Opslaan** om uw verbindingsinstellingen op te slaan.

1. Herhaal de vorige stap om meer verbindingen toe te voegen in elke faciliteit waar Microsoft is verbonden met uw netwerk, binnen de **metro** die u eerder hebt geselecteerd.

1. Nadat u alle vereiste verbindingen hebt toegevoegd, selecteert u **Controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringconfiguratie definitief](../media/setup-direct-conf-tab-final.png)

1. Merk op dat de portal de basisvalidatie van de ingevoerde informatie uitvoert. Een lint bovenaan geeft het bericht *Laatste validatie uit...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringvalidatie](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in *Validatie is geslaagd,* controleert u uw gegevens. Dien de aanvraag in door **Maken te**selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **Vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering indiening](../media/setup-direct-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt weergegeven zoals hier wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering succes](../media/setup-direct-success.png)
