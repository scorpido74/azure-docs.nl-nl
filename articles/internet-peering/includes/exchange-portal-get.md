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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774547"
---
Zodra **peering-bron** is geïmplementeerd, u deze bekijken door de onderstaande stappen te volgen.

1. Ga naar **Resourcegroepen** en klik op de resourcegroep die u hebt geselecteerd terwijl u **Peering-bron** maakt. U het veld *Filter* gebruiken als u te veel resourcegroepen hebt.

    > [!div class="mx-imgBorder"]
    > ![Peering-resourcegroep](../media/setup-direct-get-resourcegroup.png)

1. Klik op de **peeringbron** die u hebt gemaakt.

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-get-open.png)

1. De **overzichtspagina** toont informatie op hoog niveau. Bekijk hieronder de onderstaande informatie.

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-get-overview.png)

1. Klik aan de linkerkant op **ASN-informatie** om informatie te bekijken die is ingediend tijdens het maken van PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-get-asninfo.png)

1. Klik aan de linkerkant op **Verbindingen**. Bekijk bovenaan een overzicht van peeringverbindingen tussen uw ASN en Microsoft, in verschillende faciliteiten in de metro. U ook terecht komen bij het overzicht van verbindingen van de pagina **Overzicht,** door te klikken op **Verbindingen** in het middelste deelvenster zoals hierboven gemarkeerd.

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-get-connectionssummary.png)

    * **Verbindingsstatus** komt overeen met de status van de peeringverbinding die is ingesteld. De statussen die in dit veld worden weergegeven, volgen het statusdiagram dat wordt weergegeven in [Exchange-peering-walkthrough](../walkthrough-exchange-all.md)
    * **De status van de IPv4-sessie** en **de IPv6-sessie** komen overeen met de IPv4- en IPv6 BGP-sessiestatus.  
    * Wanneer u een rij bovenaan selecteert, worden in de sectie ***Verbinding*** aan de onderkant details voor elke verbinding weergegeven. U op pijlmarkeringen klikken om de subsecties ***Configuratie,*** ***IPv4-adres*** en ***IPv6-adres*** uit te vouwen

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-get-connectionsipv4.png)
