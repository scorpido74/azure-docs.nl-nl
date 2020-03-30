---
title: Een plaatsingsgroep voor nabijheid maken met behulp van de portal
description: Meer informatie over het maken van een plaatsingsgroep voor nabijheid met behulp van de Azure-portal.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180249"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Een plaatsingsgroep voor nabijheid maken met behulp van de portal

Als u VM's zo dicht mogelijk wilt plaatsen en de laagst mogelijke latentie wilt bereiken, moet u deze implementeren binnen een [plaatsingsgroep voor de nabijheid.](co-location.md#proximity-placement-groups)

Een plaatsingsgroep voor nabijheid is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen fysiek dicht bij elkaar staan. Plaatsingsgroepen voor nabijheidzijn handig voor workloads waarbij lage latentie een vereiste is.


## <a name="create-the-proximity-placement-group"></a>De plaatsingsgroep voor nabijheid maken

1. Typ **de plaatsingsgroep nabijheid** in de zoekopdracht.
1. Selecteer **onder Services** in de zoekresultaten de optie **Nabijheidsplaatsingsgroepen**.
1. Selecteer op de pagina **Plaatsingsgroepen nabijheid** de optie **Toevoegen**.
1. Controleer op het tabblad **Basisbeginselen** onder **Projectdetails**of het juiste abonnement is geselecteerd.
1. Selecteer **in de groep Resource** nieuw **maken** om een nieuwe groep te maken of selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
1. Selecteer **in Regio** de locatie waar u de plaatsingsgroep voor nabijheid wilt maken.
1. Typ in **de groepsgroep Nabijheid een** naam en selecteer Controleren + **maken**.
1. Nadat de validatie is doorgegeven, selecteert **u Maken** om de groep nabijheidsplaatsing te maken.

    ![Schermafbeelding van het maken van een plaatsingsgroep voor nabijheid](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Een virtuele machine maken

1. Ga tijdens het maken van een vm in de portal naar het tabblad **Geavanceerd.** 
1. Selecteer in de selectie **voor plaatsingsgroep Nabijheid** de juiste plaatsingsgroep. 

    ![Schermafbeelding van de sectie nabijheidsplaatsingbij het maken van een nieuwe virtuele machine in de portal](./media/ppg/vm-ppg.png)

1. Wanneer u klaar bent met het maken van alle andere vereiste selecties, selecteert u **Controleren + maken**.
1. Nadat de validatie is doorstaan, selecteert **u Maken** om de VM in de plaatsingsgroep te implementeren.




## <a name="next-steps"></a>Volgende stappen

U de [Azure PowerShell](proximity-placement-groups.md) ook gebruiken om nabijheidsplaatsingsgroepen te maken.

