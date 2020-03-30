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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775210"
---
1. Vul op de pagina **Een peering maken** onder tabblad **Configuratie** de velden in zoals hieronder wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering-configuratie - Exchange](../media/setup-exchange-conf-tab.png)

    * Selecteer *Exchange* **voor peeringtype**.
    * Selecteer **SKU** als *Basisvrij*.
    * Kies de **Metro-locatie** voor de plaats waar u peering wilt converteren naar Azure-bron. Als u peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** die niet zijn geconverteerd naar Azure-bron, worden dergelijke verbindingen weergegeven in de sectie **Peering-verbindingen** zoals hieronder weergegeven. U deze peeringverbindingen nu converteren naar Azure-bron.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Exchange - Legacy-verbindingen](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > U de instellingen voor oudere peeringverbindingen niet wijzigen. Als u extra peeringverbindingen met Microsoft wilt toevoegen op de geselecteerde **Metro-locatie,** u dit doen door op Nieuwe knop **maken** te klikken. Zie [Een Exchange-peering maken of wijzigen met behulp van de portal](../howto-exchange-portal.md) voor meer informatie.
        >

1. Klik op **Review + maak**. Houd er rekening mee dat portal de basisvalidatie van de ingevoerde informatie uitvoert. Dit wordt weergegeven in een lint aan de bovenkant, als *Laatste validatie uitvoeren...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Validatie voor peering](../media/setup-direct-review-tab-validation.png)

1. Nadat deze wordt ingeschakeld in *Validatie geslaagd,* controleert u uw gegevens en dient u de aanvraag in door op **Maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **Vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering verzenden](../media/setup-exchange-review-tab-submit.png)

1. Zodra u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt hieronder weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering Succes](../media/setup-direct-success.png)
