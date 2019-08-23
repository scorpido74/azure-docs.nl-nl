---
title: Knoop punten voor VMware-oplossing verwijderen door CloudSimple-Azure
description: Meer informatie over het verwijderen van knoop punten uit uw VMWare met CloudSimple-implementatie
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972820"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Knoop punten uit de Azure VMware-oplossing verwijderen door CloudSimple

CloudSimple-knoop punten worden gemeten als ze eenmaal zijn gemaakt.  Knoop punten moeten worden verwijderd om het meten van de knoop punten te stoppen.  U verwijdert de knoop punten die niet van Azure Portal worden gebruikt.

## <a name="before-you-begin"></a>Voordat u begint

Een knoop punt kan alleen worden verwijderd onder de volgende omstandigheden:

* Een Privécloud die is gemaakt met de knoop punten wordt verwijderd.  Als u een Privécloud wilt verwijderen, raadpleegt u [een Azure VMware-oplossing verwijderen via CloudSimple Private Cloud](delete-private-cloud.md).
* Het knoop punt is uit de privécloud verwijderd door de Privécloud te verkleinen.  Als u een Privécloud wilt verkleinen, raadpleegt u [Azure VMware-oplossing verkleinen door CloudSimple privécloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>CloudSimple-knoop punt verwijderen

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple-knoop punten**.

   ![CloudSimple-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knoop punten**.

4. Selecteer knoop punten die geen deel uitmaken van een Privécloud om deze te verwijderen.  Kolom **naam privécloud** bevat de naam van de privécloud waarvan een knoop punt deel uitmaakt.  Als een knoop punt niet door een Privécloud wordt gebruikt, is de waarde leeg. 

    ![CloudSimple-knoop punten selecteren](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Alleen knoop punten die geen deel uitmaken van de Privécloud, kunnen worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [privécloud](cloudsimple-private-cloud.md)
