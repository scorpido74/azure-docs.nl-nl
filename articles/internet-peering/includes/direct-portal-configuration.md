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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775275"
---
1. Vul op de pagina **een peering maken** onder **configuratie** tabblad de velden in zoals hieronder wordt weer gegeven.

    * Selecteer voor **type peering**de optie *direct*.
    * Kies voor **micro soft-netwerk** *AS8075*. Maak geen peering met ASN 8069. Het is gereserveerd voor speciale toepassingen en wordt alleen gebruikt door [micro soft-peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als *gratis basis*versie. Selecteer *Premium gratis* omdat deze is gereserveerd voor speciale toepassingen.
    * Kies de locatie van de **metro lijn** waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peering-verbindingen met micro soft hebt in de geselecteerde **metro** locatie en u de portal voor het eerst gebruikt om peering op die locatie in te stellen, worden uw bestaande peering-verbindingen weer gegeven in de sectie **peering Connections** , zoals hieronder wordt weer gegeven. Micro soft converteert deze peering-verbindingen automatisch naar de Azure-resource, zodat u ze allemaal kunt beheren, samen met de nieuwe verbindingen, op één plek. Zie [een verouderde directe peering converteren naar Azure-resource met behulp van de portal](../howto-legacy-direct-portal.md) voor meer informatie.
        >

1. Klik onder **peering verbindingen**op **nieuwe maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u de verbindings instellingen wilt configureren/wijzigen, klikt u op de knop bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![peering-configuratie-direct bewerken](../media/setup-direct-conf-tab-edit.png)
    
    * Als u een regel wilt verwijderen, klikt u op de knop **...** > **verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![peering-configuratie-direct bewerken](../media/setup-direct-conf-tab-delete.png)

    * U moet alle instellingen voor een verbinding opgeven, zoals hieronder wordt weer gegeven.

         > [!div class="mx-imgBorder"]
         > ![peering-configuratie-directe verbinding](../media/setup-direct-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waarbij de verbinding moet worden ingesteld.
        1. **Sessie adres provider** wordt gebruikt om te bepalen wie het subnet nodig heeft voor het instellen van een BGP-sessie tussen uw netwerk en micro soft. Als u een subnet kunt opgeven, kiest u *peer*. Else Kies **micro soft** en [micro soft-peering](mailto:peering@microsoft.com) neemt contact met u op. Houd er rekening mee dat de aanvraag van de peering langer moet worden verwerkt door micro soft. In sommige gevallen kan micro soft mogelijk geen subnetten leveren die ertoe leiden dat de aanvraag niet kan worden geweigerd.
        1. Als u **sessie adres provider** hebt gekozen *als peer*, voert u het IPv4-en IPv6-adres in naast prefix masker in de velden voor het **IPv4-voor voegsel** en het **IPv6-voor** voegsel van de sessie.
        1. Voer het aantal IPv4-en IPv6-voor voegsels in dat u wilt adverteren in de velden **Maxi maal aantal** geadverteerde IPv4-adressen en **Maxi maal aantal geadverteerde IPv6-adressen** .
        1. Pas de schuif regelaar **totale band breedte** aan om de band breedte voor de verbinding weer te geven.
        1. Klik op **OK** om uw Verbindings instellingen op te slaan.

1. Herhaal de bovenstaande stap om meer verbindingen toe te voegen aan elke faciliteit waar micro soft zich in de geselecteerde **metro lijn** bevindt met uw netwerk.

1. Nadat u alle vereiste verbindingen hebt toegevoegd, klikt u op **controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![tabblad Configuratie van peering eind](../media/setup-direct-conf-tab-final.png)

1. Houd er rekening mee dat de basis validatie van de gegevens die u hebt ingevoerd in de portal wordt uitgevoerd. Dit wordt weer gegeven in een lint bovenaan, zoals het *uitvoeren van definitieve validatie...* .

    > [!div class="mx-imgBorder"]
    > ![tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat de validatie is *voltooid*, controleert u uw gegevens en verzendt u de aanvraag door op **maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![peering](../media/setup-direct-review-tab-submit.png) verzenden

1. Wanneer u de aanvraag hebt ingediend, wacht u totdat de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![peering geslaagd](../media/setup-direct-success.png)
