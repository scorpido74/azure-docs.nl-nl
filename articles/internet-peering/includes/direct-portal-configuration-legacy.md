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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775067"
---
1. Vul op de pagina **Een peering maken** onder tabblad **Configuratie** de velden in zoals hieronder wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering-configuratie - Direct](../media/setup-direct-conf-tab.png)

    * Selecteer *Direct* **voor peeringtype**.
    * Kies VOOR **Microsoft-netwerk** *AS8075*. Selecteer ASN 8069 niet. Het is gereserveerd voor speciale toepassingen en alleen gebruikt door [Microsoft peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als *Basisvrij*. Selecteer Premium *Free* niet omdat het is gereserveerd voor speciale toepassingen.
    * Kies de **Metro-locatie** voor de plaats waar u peering wilt converteren naar Azure-bron. Als u peeringverbindingen met Microsoft hebt op de geselecteerde **Metro-locatie** die niet zijn geconverteerd naar Azure-bron, worden dergelijke verbindingen weergegeven in de sectie **Peering-verbindingen** zoals hieronder weergegeven. U deze peeringverbindingen nu converteren naar Azure-bron.

        > [!div class="mx-imgBorder"]
        > ![Peering-configuratie - Direct - Legacy-verbindingen](../media/setup-directlegacy-conf-tab.png)

1. Als u bandbreedte moet bijwerken, klikt u op de knop Bewerken voor een regel, zoals hieronder gemarkeerd, om verbindingsinstellingen te wijzigen.

    > [!div class="mx-imgBorder"]
    > ![Peering-configuratie - Direct bewerken](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Als u extra peeringverbindingen met Microsoft wilt toevoegen op de geselecteerde **Metro-locatie,** u dit doen door op Nieuwe knop **maken** te klikken. Zie [Een direct peering maken of wijzigen met behulp van de portal](../howto-direct-portal.md) voor meer informatie.
    >

1. Klik op **Review + maak**. Houd er rekening mee dat portal de basisvalidatie van de ingevoerde informatie uitvoert. Dit wordt weergegeven in een lint aan de bovenkant, als *Laatste validatie uitvoeren...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Validatie voor peering](../media/setup-direct-review-tab-validation.png)

1. Nadat deze wordt ingeschakeld in *Validatie geslaagd,* controleert u uw gegevens en dient u de aanvraag in door op **Maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **Vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![Peering verzenden](../media/setup-direct-review-tab-submit.png)

1. Zodra u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [Microsoft peering](mailto:peering@microsoft.com). Een succesvolle implementatie wordt hieronder weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering Succes](../media/setup-direct-success.png)
