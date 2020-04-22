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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678817"
---
1. Ga naar **Resourcegroepen**en selecteer de resourcegroep die u hebt geselecteerd toen u de **peeringbron** hebt gemaakt. Gebruik het vak **Filter** als u te veel resourcegroepen hebt.

    > [!div class="mx-imgBorder"]
    > ![Resourcegroepen](../media/setup-direct-get-resourcegroup.png)

1. Selecteer de **peeringbron die** u hebt gemaakt.

    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-get-open.png)

1. De **overzichtspagina** toont informatie op hoog niveau, zoals hier wordt weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Deelvenster Overzicht van peeringbronnen](../media/setup-direct-get-overview.png)

1. Selecteer aan de linkerkant **ASN-informatie** om de informatie weer te geven die is ingediend toen u PeerAsn hebt gemaakt.

    > [!div class="mx-imgBorder"]
    > ![Asn-informatie over peeringbronnen](../media/setup-direct-get-asninfo.png)

1. **Selecteer**Verbindingen aan de linkerkant . Bovenaan het scherm ziet u een overzicht van peeringverbindingen tussen uw ASN en Microsoft, in verschillende faciliteiten in de metro. U ook toegang krijgen tot het verbindingsoverzicht op de pagina **Overzicht** door **Verbindingen** in het midden van het deelvenster te selecteren, zoals weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering-bronverbindingen](../media/setup-direct-get-connectionssummary.png)

    * **Verbindingsstatus** komt overeen met de status van de instelling voor peeringverbindingen. De statussen die in dit veld worden weergegeven, volgen het statusdiagram dat wordt weergegeven in de [direct peering-walkthrough](../walkthrough-direct-all.md).
    * De status **van de IPv4-sessie** en **de IPv6-sessie** komen overeen met de IPv4- en IPv6 BGP-sessiestatus. 
    * Wanneer u een rij boven aan het scherm selecteert, worden in de sectie **Verbinding** aan de onderkant details voor elke verbinding weergegeven. Selecteer de pijlen om **configuratie,** **IPv4-adres**en **IPv6-adres**uit te vouwen .
