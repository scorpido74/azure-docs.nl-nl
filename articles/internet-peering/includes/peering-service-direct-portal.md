---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129949"
---
1. Klik op een peering verbinding die u wilt inschakelen voor Peering Service en klik vervolgens op de **...**  >  **Knop Verbinding bewerken.**
    > [!div class="mx-imgBorder"]
    > ![Bewerken van peeringverbinding](../media/setup-direct-modify-editconnection.png)
1. Klik onder de sectie ***Gebruik voor peeringservice***op **Ingeschakeld** en **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding maken peeringservice mogelijk](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. In het scherm Overzicht ziet u de implementatiegegevens. Zodra uw implementatie is voltooid, klikt u op **Ga naar resource**.
    > [!div class="mx-imgBorder"]
    > ![Uw implementatie is voltooid](../media/setup-direct-modify-overview-deployment-complete.png)

1. U ziet dan onder Instellingen **Geregistreerde Voorvoegsels**. Klik op **Geregistreerd voorvoegsel toevoegen**.
    > [!div class="mx-imgBorder"]
    > ![Geregistreerde voorvoegsels en verbindingen](../media/setup-direct-modify-add-registered-prefix.png)
1. Een voorvoegsel registreren door een **naam** en een **voorvoegsel te** selecteren en op **Opslaan te klikken**
    > [!div class="mx-imgBorder"]
    >  ![Een voorvoegsel registreren](../media/setup-direct-modify-register-a-prefix.png) 

1. Zodra een voorvoegsel is gemaakt, ziet u het in de lijst met geregistreerde voorvoegsels. Klik op de **naam** van het voorvoegsel voor meer details.
    > [!div class="mx-imgBorder"]
    > ![Geregistreerde voorvoegsels en verbindingen](../media/setup-direct-modify-registered-prefixes.png)
1. Op de geregistreerde voorvoegselpagina ziet u de volledige details om de **voorvoegselsleutel** voor elk voorvoegsel op te nemen. Deze sleutel moet worden verstrekt aan de klant toegewezen dit voorvoegsel van hun provider ISP. De klant kan vervolgens hun voorvoegsel registreren in hun abonnement met deze sleutel.
    > [!div class="mx-imgBorder"]
    > ![Voorvoegsel met voorvoegselsleutel](../media/setup-direct-modify-registered-prefix-detail.png)