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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775275"
---
1. Vul op de pagina **Een peering maken** onder tabblad **Configuratie** de velden in zoals hieronder wordt weergegeven.

    * Selecteer *Direct* **voor peeringtype**.
    * Kies VOOR **Microsoft-netwerk** *AS8075*. Maak geen peering met ASN 8069. Het is gereserveerd voor speciale toepassingen en alleen gebruikt door [Microsoft peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als *Basisvrij*. Selecteer Premium *Free* niet omdat het is gereserveerd voor speciale toepassingen.
    * Kies de **metrolocatie** voor de plaats waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** en u gebruikt portal voor het eerst om peering op die locatie in te stellen, worden uw bestaande peeringverbindingen weergegeven in de sectie **Peering-verbindingen** zoals hieronder weergegeven. Microsoft converteert deze peeringverbindingen automatisch naar Azure-bronnen, zodat u ze samen met de nieuwe verbindingen op één plaats beheren. Zie [Een verouderde direct peering converteren naar Azure-bron met behulp van de portal](../howto-legacy-direct-portal.md) voor meer informatie.
        >

1. Klik **onder Peering-verbindingen**op **Nieuw maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u verbindingsinstellingen wilt configureren/wijzigen, klikt u op de knop Bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Direct bewerken](../media/setup-direct-conf-tab-edit.png)
    
    * Als u een regel wilt verwijderen, klikt u op **...** knop > **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Direct bewerken](../media/setup-direct-conf-tab-delete.png)

    * U bent verplicht om alle instellingen voor een verbinding zoals hieronder weergegeven.

         > [!div class="mx-imgBorder"]
         > ![Peering-configuratie - Directe verbinding](../media/setup-direct-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waar de verbinding moet worden ingesteld.
        1. **Sessieadresprovider** wordt gebruikt om te bepalen wie het subnet biedt dat nodig is om BGP-sessie tussen uw netwerk en Microsoft in te stellen. Als u subnet verstrekken, kiest u *Peer*. Anders kiezen **Microsoft** en [Microsoft peering](mailto:peering@microsoft.com) zal contact met u opnemen. Houd er rekening mee dat het langer duurt voordat Microsoft het peering-verzoek verwerkt. In sommige gevallen kan Microsoft mogelijk geen subnetten leveren, wat zal resulteren in weigering van aanvragen.
        1. Als u **Sessieadresprovider** als *Peer*hebt gekozen, voert u respectievelijk IPv4- en IPv6-adres in, samen met het voorvoegselmasker in de velden **Session IPv4 Prefix** en **Session IPv6 Prefix.**
        1. Voer het aantal IPv4- en IPv6-voorvoegsels in dat u adverteert in de velden **Maximaal geadverteerde IPv4-adressen** en **Maximaal geadverteerde IPv6-adressen.**
        1. Pas de schuifregelaar **Totale bandbreedte aan** om de bandbreedte voor de verbinding weer te geven.
        1. Klik op **OK** om de verbindingsinstellingen op te slaan.

1. Herhaal bovenstaande stap om meer verbindingen toe te voegen in elke faciliteit waar Microsoft is verbonden met uw netwerk, binnen de eerder geselecteerde **Metro.**

1. Nadat u alle vereiste verbindingen hebt toegevoegd, klikt u op **Controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringconfiguratie definitief](../media/setup-direct-conf-tab-final.png)

1. Houd er rekening mee dat portal de basisvalidatie van de ingevoerde informatie uitvoert. Dit wordt weergegeven in een lint aan de bovenkant, als *Laatste validatie uitvoeren...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Validatie voor peering](../media/setup-direct-review-tab-validation.png)

1. Nadat deze wordt ingeschakeld in *Validatie geslaagd,* controleert u uw gegevens en dient u de aanvraag in door op **Maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **Vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering verzenden](../media/setup-direct-review-tab-submit.png)

1. Zodra u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt hieronder weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering Succes](../media/setup-direct-success.png)
