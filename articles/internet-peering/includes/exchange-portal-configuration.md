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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774495"
---
1. Vul op de pagina **Een peering maken** onder tabblad **Configuratie** de velden in zoals hieronder wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering-configuratie - Exchange](../media/setup-exchange-conf-tab.png)

    * Selecteer *Exchange* **voor peeringtype**.
    * Selecteer **SKU** als *Basisvrij*.
    * Kies de **metrolocatie** voor de plaats waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** en u gebruikt portal voor het eerst om peering op die locatie in te stellen, worden uw bestaande peeringverbindingen weergegeven in de sectie **Peering-verbindingen** zoals hieronder weergegeven. Microsoft converteert deze peeringverbindingen automatisch naar Azure-bronnen, zodat u ze samen met de nieuwe verbindingen op één plaats beheren. Zie [Een verouderde Exchange-peering converteren naar Azure-bron met behulp van de portal](../howto-legacy-exchange-portal.md) voor meer informatie.
        >

1. Klik **onder Peering-verbindingen**op **Nieuw maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u verbindingsinstellingen wilt configureren/wijzigen, klikt u op de knop Bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Exchange Bewerken](../media/setup-exchange-conf-tab-edit.png)

    * Als u een regel wilt verwijderen, klikt u op **...** knop > **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Exchange Bewerken](../media/setup-exchange-conf-tab-delete.png)

    * U bent verplicht om alle instellingen voor een verbinding zoals hieronder weergegeven.

         > [!div class="mx-imgBorder"]
         > ![Peering-configuratie - Exchange-verbinding](../media/setup-exchange-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waar de verbinding moet worden ingesteld.
        1. Voer iPv4- en **IPv6 address**IPv6-adres in de velden **IPv4-** en IPv6-adres in dat met behulp van de neighbor-opdracht in Microsoft-routers zou worden geconfigureerd.
        1. Voer het aantal IPv4- en IPv6-voorvoegsels in dat u adverteert in de velden **Maximaal geadverteerde IPv4-adressen** en **Maximaal geadverteerde IPv6-adressen.**
        1. Klik op **OK** om de verbindingsinstellingen op te slaan.

1. Herhaal bovenstaande stap om meer verbindingen toe te voegen in elke faciliteit waar Microsoft is verbonden met uw netwerk, binnen de eerder geselecteerde **Metro.**

1. Nadat u alle vereiste verbindingen hebt toegevoegd, klikt u op **Controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Peeringconfiguratie definitief](../media/setup-exchange-conf-tab-final.png)

1. Houd er rekening mee dat portal de basisvalidatie van de ingevoerde informatie uitvoert. Dit wordt weergegeven in een lint aan de bovenkant, als *Laatste validatie uitvoeren...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Validatie voor peering](../media/setup-direct-review-tab-validation.png)

1. Nadat deze wordt ingeschakeld in *Validatie geslaagd,* controleert u uw gegevens en dient u de aanvraag in door op **Maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **Vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering verzenden](../media/setup-exchange-review-tab-submit.png)

1. Zodra u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt hieronder weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering Succes](../media/setup-direct-success.png)
