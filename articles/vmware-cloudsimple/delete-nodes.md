---
title: Knooppunten voor VMware-oplossing verwijderen door CloudSimple - Azure
description: Meer informatie over het verwijderen van knooppunten uit uw VMWare met CloudSimple-implementatie
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024735"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Knooppunten verwijderen uit Azure VMware-oplossing door CloudSimple

CloudSimple-knooppunten worden gemeten zodra ze zijn gemaakt.  Knooppunten moeten worden verwijderd om te stoppen met het meten van de knooppunten.  U verwijdert de knooppunten die niet worden gebruikt uit azure-portal.

## <a name="before-you-begin"></a>Voordat u begint

Een knooppunt kan alleen worden verwijderd onder de volgende voorwaarden:

* Een private cloud die met de knooppunten is gemaakt, wordt verwijderd.  Zie [Een Azure VMware-oplossing verwijderen door CloudSimple Private Cloud](delete-private-cloud.md)als u een private cloud wilt verwijderen.
* Het knooppunt is uit de Private Cloud verwijderd door de Private Cloud te verkleinen.  Zie [Azure VMware-oplossing krimpen door CloudSimple Private Cloud](shrink-private-cloud.md)als u een Private Cloud wilt verkleinen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="delete-cloudsimple-node"></a>CloudSimple-knooppunt verwijderen

1. Selecteer **Alle services**.

2. Zoeken naar **CloudSimple-knooppunten**.

   ![Zoeken in CloudSimple-knooppunten](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knooppunten**.

4. Selecteer knooppunten die niet tot een private cloud behoren om te verwijderen.  **De** kolom PRIVATE CLOUD NAME toont de naam Private Cloud waartoe een knooppunt behoort.  Als een knooppunt niet wordt gebruikt door een private cloud, is de waarde leeg. 

    ![CloudSimple-knooppunten selecteren](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Alleen knooppunten die geen deel uitmaken van de Private Cloud kunnen worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Private Cloud](cloudsimple-private-cloud.md)
