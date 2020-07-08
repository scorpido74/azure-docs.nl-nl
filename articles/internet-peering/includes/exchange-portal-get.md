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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678537"
---
Nadat de **peering** -resource is geïmplementeerd, kunt u deze bekijken door de volgende stappen uit te voeren.

1. Ga naar **resource groepen**en selecteer de resource groep die u hebt geselecteerd tijdens het maken van de **peering** -resource. Gebruik het vak **filteren** als u te veel resource groepen hebt.

    > [!div class="mx-imgBorder"]
    > ![Resourcegroepen](../media/setup-direct-get-resourcegroup.png)

1. Selecteer de **peering** -resource die u hebt gemaakt.

    > [!div class="mx-imgBorder"]
    > ![Bron weergave voor peering](../media/setup-direct-get-open.png)

1. Op de pagina **overzicht** vindt u informatie op hoog niveau, zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Het deel venster Overzicht van peering-resource](../media/setup-exchange-get-overview.png)

1. Selecteer aan de linkerkant **ASN-gegevens** om de informatie weer te geven die is verzonden tijdens het maken van PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![ASN-gegevens van peering-bron](../media/setup-direct-get-asninfo.png)

1. Selecteer aan de linkerkant **verbindingen**. Boven aan het scherm ziet u een samen vatting van peering-verbindingen tussen uw ASN en micro soft, tussen verschillende faciliteiten binnen de metro. U kunt het overzicht van verbindingen ook openen op de pagina **overzicht** door **verbindingen** te selecteren in het middelste deel venster, zoals wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering-bron verbindingen](../media/setup-exchange-get-connectionssummary.png)

    * De **verbindings status** komt overeen met de status van de instelling voor de peering-verbinding. De status diagram die in dit veld wordt weer gegeven, is te zien in het [overzicht van Exchange-peering](../walkthrough-exchange-all.md).
    * De status van de **IPv4-sessie** en de **IPv6-sessie** komen respectievelijk overeen met de IPv4-en IPv6 BGP-sessie status.  
    * Wanneer u een rij boven aan het scherm selecteert, toont de sectie **verbinding** onderaan de details van elke verbinding. Selecteer de pijlen om de **configuratie**, het **IPv4-adres**en het **IPv6-adres**uit te vouwen.

    > [!div class="mx-imgBorder"]
    > ![Bron weergave voor peering](../media/setup-exchange-get-connectionsipv4.png)
