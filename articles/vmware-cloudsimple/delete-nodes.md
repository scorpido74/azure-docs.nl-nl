---
title: Knoop punten voor VMware-oplossingen (AVS) verwijderen-Azure
description: Meer informatie over het verwijderen van knoop punten uit uw VMWare met AVS-implementatie
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024735"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Knoop punten uit de Azure VMware-oplossing verwijderen per AVS

AVS-knoop punten worden gemeten wanneer ze worden gemaakt. Knoop punten moeten worden verwijderd om het meten van de knoop punten te stoppen. U verwijdert de knoop punten die niet van Azure Portal worden gebruikt.

## <a name="before-you-begin"></a>Voordat u begint

Een knoop punt kan alleen worden verwijderd onder de volgende omstandigheden:

* Een automatische AVS-Cloud die is gemaakt met de knoop punten wordt verwijderd. Zie [een Azure VMware-oplossing verwijderen door de privécloud](delete-private-cloud.md)voor meer informatie over het verwijderen van een AVS-privécloud.
* Het knoop punt is verwijderd uit de cloud van de AVS door de Privécloud te verkleinen. Als u een Privécloud wilt verkleinen, raadpleegt u [Azure VMware-oplossing verkleinen door de privécloud van de Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>AVS-knoop punt verwijderen

1. Selecteer **Alle services**.

2. Zoeken naar **AVS-knoop punten**.

   ![AVS-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer de **AVS-knoop punten**.

4. Selecteer knoop punten die geen deel uitmaken van een Privécloud die u wilt verwijderen. De naam kolom van de automatische **AVS-Cloud** geeft de naam van de privécloud met een knoop punt weer. Als een knoop punt niet wordt gebruikt door een Privécloud in de Cloud, is de waarde leeg. 

    ![AVS-knoop punten selecteren](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Alleen knoop punten die geen deel uitmaken van de AVS-Privécloud, kunnen worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de Privécloud van de [Cloud](cloudsimple-private-cloud.md)
