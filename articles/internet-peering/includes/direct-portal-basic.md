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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774638"
---
1. Klik **op Een resource maken** > **Zie alles**.

    > [!div class="mx-imgBorder"]
    > ![Zoeken naar peering](../media/setup-seeall.png)

1. Zoek *naar Peering* in het zoekvak en druk op *Enter* op het toetsenbord. Klik in de resultaten op **Peering-bron.**

    > [!div class="mx-imgBorder"]
    > ![Peering starten](../media/setup-launch.png)

1. Zodra **Peering** is gestart, bekijkt u de pagina om details te begrijpen. Klik op Maken als u klaar **bent.**

    > [!div class="mx-imgBorder"]
    > ![Peering maken](../media/setup-create.png)

1. Vul op de pagina **Een peering maken** onder het tabblad **Basisbeginselen** de velden in zoals hieronder wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Basisprincipes voor peering](../media/setup-basics-tab.png)

    * Kies uw **Azure-abonnement**.
    * Voor **resourcegroep**u een bestaande resourcegroep kiezen uit de vervolgkeuzelijst of een nieuwe groep maken door op **Nieuw maken**te klikken. We zullen een nieuwe resourcegroep maken voor dit voorbeeld.
    * **Naam** komt overeen met resource naam en kan alles wat je kiest.
    * **Regio** wordt automatisch geselecteerd als u een bestaande resourcegroep hebt gekozen in de bovenstaande stap. Als u ervoor kiest om een nieuwe resourcegroep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt laten wonen. VS - oost

        > [!NOTE]
        > Regio waar de resourcegroep zich bevindt, is onafhankelijk van de locatie waar u peering met Microsoft wilt maken. Het is echter de beste gewoonte om uw peeringbronnen te organiseren binnen resourcegroepen die zich in de dichtstbijzijnde Azure-regio's bevinden. Bijvoorbeeld: voor peerings in Ashburn, u een resource groep in *Oost-VS* of *Oost-US2*

    * Kies uw ASN in het veld **Peer ASN.**

        > [!IMPORTANT]
        > * U alleen een ASN met Validatiestaat als 'Goedgekeurd' kiezen voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt ingediend, wacht u 12 uur of zo tot de ASSOCIATIE ASN is goedgekeurd. Als de ASN die u selecteert in afwachting van validatie een foutbericht ziet. 
        > * Als u de ASN die u moet kiezen niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Controleer in dat verband of u PeerAsn al hebt gemaakt met [Associate Peer ASN voor Azure-abonnement.](../howto-subscription-association-portal.md)

        > [!div class="mx-imgBorder"]
        > ![Peering Basics gevuld](../media/setup-direct-basics-filled-tab.png)

    * Klik op **Volgende: Configuratie >** om door te gaan.
