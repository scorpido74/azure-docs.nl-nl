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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774547"
---
Als de **peering** -resource is geïmplementeerd, kunt u deze bekijken door de volgende stappen uit te voeren.

1. Ga naar **resource groepen** en klik op de resource groep die u hebt geselecteerd tijdens het maken van een **peering** -resource. U kunt het *filter* veld gebruiken als u te veel resource groepen hebt.

    > [!div class="mx-imgBorder"]
    > ![peering-resource groep](../media/setup-direct-get-resourcegroup.png)

1. Klik op de **peering** -resource die u hebt gemaakt.

    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-direct-get-open.png)

1. Op de pagina **overzicht** vindt u informatie op hoog niveau. Bekijk de hieronder gemarkeerde informatie.

    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-exchange-get-overview.png)

1. Klik aan de linkerkant op **ASN-informatie** om de informatie weer te geven die is verzonden tijdens het maken van PeerAsn

    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-direct-get-asninfo.png)

1. Klik aan de linkerkant op **verbindingen**. Bekijk bovenaan een samen vatting van peering-verbindingen tussen uw ASN en micro soft, op verschillende locaties binnen de metro. U kunt ook op de pagina samen vatting van verbindingen van **overzicht** klikken op **verbindingen** in het middelste deel venster, zoals hierboven gemarkeerd.

    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-exchange-get-connectionssummary.png)

    * De **verbindings status** komt overeen met de status van de peering-verbinding die is ingesteld. De status diagram die in dit veld wordt weer gegeven, is te zien in het [overzicht van Exchange-peering](../walkthrough-exchange-all.md)
    * De status van de **IPv4-sessie** en de **IPv6-sessie** komen respectievelijk overeen met de IPv4-en IPv6 BGP-sessie status.  
    * Wanneer u op de bovenste rij selecteert, toont de sectie ***verbinding*** onderaan de details van elke verbinding. U kunt klikken op de pijl markeringen om de ***configuratie***van de subsecties, het ***IPv4-adres*** en het ***IPv6-adres*** uit te vouwen

    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-exchange-get-connectionsipv4.png)
