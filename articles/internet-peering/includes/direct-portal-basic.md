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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678516"
---
1. Selecteer **Een resource maken** > **Zie alles**.

    > [!div class="mx-imgBorder"]
    > ![Zoeken naar peering](../media/setup-seeall.png)

1. Zoek **naar Peering** in het zoekvak en selecteer **Enter** op het toetsenbord. Selecteer in de resultaten een **Peering-bron.**

    > [!div class="mx-imgBorder"]
    > ![Peering starten](../media/setup-launch.png)

1. Nadat **Peering** is gestart, bekijkt u de pagina om de details te begrijpen. Wanneer u klaar bent, selecteert u **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Peering maken](../media/setup-create.png)

1. Vul op de pagina **Een peering maken** op het tabblad **Basisbeginselen** de vakken in zoals hier wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Basisbeginselen voor peering](../media/setup-basics-tab.png)

    * Selecteer uw **Azure-abonnement**.
    * Voor **resourcegroep**u een bestaande resourcegroep kiezen in de vervolgkeuzelijst of een nieuwe groep maken door **Nieuw maken te**selecteren. We maken een nieuwe resourcegroep voor dit voorbeeld.
    * **Naam** komt overeen met de naam van de resource en kan alles zijn wat je kiest.
    * **Regio** wordt automatisch geselecteerd als u een bestaande resourcegroep hebt gekozen. Als u ervoor kiest om een nieuwe resourcegroep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt laten wonen.

        > [!NOTE]
        > Het gebied waar een resourcegroep zich bevindt, is onafhankelijk van de locatie waar u peering met Microsoft wilt maken. Maar het is een aanbevolen manier om uw peeringbronnen te organiseren binnen resourcegroepen die zich in de dichtstbijzijnde Azure-regio's bevinden. Voor peerings in Ashburn u bijvoorbeeld een resourcegroep maken in Oost-VS of Oost-US2.

    * Selecteer uw ASN in het vak **Peer ASN.**

        > [!IMPORTANT]
        > * U alleen een ASN kiezen met Validatiestaat als goedgekeurd voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt ingediend, wacht u 12 uur of zo tot de ASSOCIATIE ASN is goedgekeurd. Als de ASN die u selecteert, nog in behandeling is, ziet u een foutbericht. 
        > * Als u de ASN die u moet kiezen niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Controleer in dat verband of u PeerAsn al hebt gemaakt met behulp van [Associate Peer ASN voor Azure-abonnement.](../howto-subscription-association-portal.md)

        > [!div class="mx-imgBorder"]
        > ![Peering Basics ingevuld](../media/setup-direct-basics-filled-tab.png)

    * Selecteer **Volgende: Configuratie >** om door te gaan.
