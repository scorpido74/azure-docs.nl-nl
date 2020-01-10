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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774638"
---
1. Klik op **een resource maken** > **alles weer te geven**.

    > [!div class="mx-imgBorder"]
    > ![zoeken in peering](../media/setup-seeall.png)

1. Zoek op *peering* in het zoekvak en druk op *Enter* op het toetsen bord. Klik in de resultaten op **peering** -resource.

    > [!div class="mx-imgBorder"]
    > ![peering-](../media/setup-launch.png) starten

1. Wanneer **peering** is gestart, controleert u de pagina om de details te begrijpen. Wanneer u klaar bent, klikt u op **maken**.

    > [!div class="mx-imgBorder"]
    > Peering-](../media/setup-create.png) ![maken

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de velden in zoals hieronder wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > Basis beginselen van ![peering](../media/setup-basics-tab.png)

    * Kies uw Azure- **abonnement**.
    * Voor **resource groep**kunt u een bestaande resource groep in de vervolg keuzelijst kiezen of een nieuwe groep maken door te klikken op **nieuwe maken**. Er wordt een nieuwe resource groep voor dit voor beeld gemaakt.
    * De **naam** komt overeen met de resource naam en kan alles zijn wat u kiest.
    * **Regio** wordt automatisch geselecteerd als u in de bovenstaande stap een bestaande resource groep hebt gekozen. Als u ervoor hebt gekozen om een nieuwe resource groep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt opslaan. VS - oost

        > [!NOTE]
        > De regio waar de resource groep zich bevindt, is onafhankelijk van de locatie waar u peering wilt maken met micro soft. Maar het is best practice om uw peering resources te organiseren in resource groepen die zich in de dichtstbijzijnde Azure-regio's bevinden. Bijvoorbeeld: voor peerings in Ashburn kunt u een resource groep maken in *VS-Oost* of *Oost-VS2*

    * Kies uw ASN in het veld **peer ASN** .

        > [!IMPORTANT]
        > * U kunt alleen een ASN met ValidationState als "goedgekeurd" kiezen voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt verzonden, wacht u 12 uur of dus voor ASN-koppeling goedgekeurd. Als de door u geselecteerde ASN in behandeling is, wordt er een fout bericht weer gegeven. 
        > * Als u de ASN die u wilt kiezen niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Als dit het geval is, controleert u of u PeerAsn al hebt gemaakt met [het koppelen van peer-ASN aan Azure-abonnement](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![basis beginselen van peering](../media/setup-direct-basics-filled-tab.png)

    * Klik op **volgende: configuratie >** om door te gaan.
