---
title: Knoop punten inrichten voor VMware-oplossing door CloudSimple-Azure
description: Meer informatie over het toevoegen van knoop punten aan uw VMWare met CloudSimple-implementatie
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5806198968d98fea4c5cbf8731358ca4041f0935
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972870"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Knoop punten inrichten voor Azure VMware-oplossing op CloudSimple

Knoop punten inrichten in de Azure Portal. Vervolgens kunt u betalen naar gebruik-capaciteit instellen voor uw persoonlijke cloud omgeving van CloudSimple.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Een knoop punt toevoegen aan uw CloudSimple-privécloud

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple-knoop punten**.

   ![CloudSimple-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knoop punten**.
4. Klik op **toevoegen** om knoop punten te maken.

    ![CloudSimple-knoop punten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u de CloudSimple-knoop punten wilt inrichten.
6. Selecteer de resource groep voor de knoop punten. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer het voor voegsel in om de knoop punten te identificeren.
8. Selecteer de locatie voor de knooppunt resources.
9. Selecteer de toegewezen locatie om de knooppunt resources te hosten.
10. Selecteer het knooppunt type. U kunt de [optie CS28 of CS36](cloudsimple-node.md)kiezen. De laatste optie omvat de maximale reken-en geheugen capaciteit.
11. Selecteer het aantal knoop punten dat moet worden ingericht.
12. Selecteer **Controleren + maken**.
13. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Privécloud maken](create-private-cloud.md)
