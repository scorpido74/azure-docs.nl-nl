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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687057"
---
1. Selecteer een peeringverbinding die u wilt inschakelen voor Azure Peering Service. Selecteer vervolgens **...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding Verbinding bewerken](../media/setup-direct-modify-editconnection.png)
1. Selecteer onder **Gebruik voor peeringservice**de optie **Ingeschakeld** en selecteer **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding Peering-service inschakelen](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. In het scherm **Overzicht** ziet u de implementatiegegevens. Nadat uw implementatie is voltooid, selecteert u **Ga naar resource**.
    > [!div class="mx-imgBorder"]
    > ![Uw implementatie is voltooid](../media/setup-direct-modify-overview-deployment-complete.png)

1. Selecteer **Geregistreerd voorvoegsel toevoegen**in het deelvenster **Geregistreerde voorvoegsels** .
    > [!div class="mx-imgBorder"]
    > ![Geregistreerd voorvoegsel toevoegen](../media/setup-direct-modify-add-registered-prefix.png)
1. Registreer een voorvoegsel door een **naam** en een **voorvoegsel te** selecteren en **Opslaan te selecteren.**
    > [!div class="mx-imgBorder"]
    >  ![Een voorvoegsel registreren](../media/setup-direct-modify-register-a-prefix.png) 

1. Nadat een voorvoegsel is gemaakt, ziet u het in de lijst **met geregistreerde voorvoegsels**. Selecteer de **naam** van het voorvoegsel om meer details te bekijken.
    > [!div class="mx-imgBorder"]
    > ![Geregistreerde voorvoegsels en verbindingen](../media/setup-direct-modify-registered-prefixes.png)
1. Op de geregistreerde voorvoegselpagina ziet u de volledige details, waaronder de **voorvoegselsleutel** voor elk voorvoegsel. Deze sleutel moet worden verstrekt aan de klant toegewezen dit voorvoegsel van hun provider ISP. De klant kan vervolgens hun voorvoegsel registreren in hun abonnement met behulp van deze sleutel.
    > [!div class="mx-imgBorder"]
    > ![Voorvoegsel met voorvoegselsleutel](../media/setup-direct-modify-registered-prefix-detail.png)