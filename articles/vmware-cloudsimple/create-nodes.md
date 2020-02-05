---
title: Knoop punten inrichten voor VMware-oplossingen (AVS)-Azure
description: Meer informatie over het toevoegen van knoop punten aan uw VMWare met AVS-implementatie
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024803"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Knoop punten inrichten voor Azure VMware-oplossingen (AVS)

Knoop punten inrichten in de Azure Portal. Vervolgens kunt u betalen naar gebruik-capaciteit instellen voor uw cloud omgeving in de privécloud.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Een knoop punt toevoegen aan de privécloud van uw AVS

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-knoop punten**.

   ![AVS-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer de **AVS-knoop punten**.
4. Klik op **toevoegen** om knoop punten te maken.

    ![AVS-knoop punten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u de AVS-knoop punten wilt inrichten.
6. Selecteer de resource groep voor de knoop punten. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer het voor voegsel in om de knoop punten te identificeren.
8. Selecteer de locatie voor de knooppunt resources.
9. Selecteer de toegewezen locatie om de knooppunt resources te hosten.
10. Selecteer het [knooppunt type](cloudsimple-node.md).
11. Selecteer het aantal knoop punten dat moet worden ingericht.
12. Selecteer **Controleren + maken**.
13. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Een Privécloud maken](create-private-cloud.md)
