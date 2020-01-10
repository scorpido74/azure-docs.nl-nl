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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775210"
---
1. Vul op de pagina **een peering maken** onder **configuratie** tabblad de velden in zoals hieronder wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![peering-configuratie-Exchange-](../media/setup-exchange-conf-tab.png)

    * Selecteer *Exchange*bij **peering-type**.
    * Selecteer **SKU** als *gratis basis*versie.
    * Kies de locatie van de **metro** waar u wilt dat de peering naar de Azure-resource wordt geconverteerd. Als u peering-verbindingen hebt met micro soft in de geselecteerde **metro** locatie die niet worden geconverteerd naar een Azure-resource, worden deze verbindingen weer gegeven in de sectie **peering-verbindingen** , zoals hieronder wordt weer gegeven. U kunt deze peering-verbindingen nu converteren naar een Azure-resource.

        > [!div class="mx-imgBorder"]
        > ![peering-configuratie: Exchange-verouderde verbindingen](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > U kunt de instellingen voor verouderde peering-verbindingen niet wijzigen. Als u aanvullende peering-verbindingen met micro soft wilt toevoegen op de geselecteerde **metro** locatie, kunt u dit doen door op **nieuwe knop maken** te klikken. Zie [een Exchange-peering maken of wijzigen met behulp van de portal](../howto-exchange-portal.md) voor meer informatie.
        >

1. Klik op **controleren + maken**. Houd er rekening mee dat de basis validatie van de gegevens die u hebt ingevoerd in de portal wordt uitgevoerd. Dit wordt weer gegeven in een lint bovenaan, zoals het *uitvoeren van definitieve validatie...* .

    > [!div class="mx-imgBorder"]
    > ![tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat de validatie is *voltooid*, controleert u uw gegevens en verzendt u de aanvraag door op **maken**te klikken. Als u uw aanvraag wilt wijzigen, klikt u op **vorige** en herhaalt u de bovenstaande stappen.

    > [!div class="mx-imgBorder"]
    > ![peering](../media/setup-exchange-review-tab-submit.png) verzenden

1. Wanneer u de aanvraag hebt ingediend, wacht u totdat de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![peering geslaagd](../media/setup-direct-success.png)
