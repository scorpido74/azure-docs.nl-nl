---
title: Een proximity-plaatsings groep maken met behulp van de portal
description: Meer informatie over het maken van een proximity-plaatsings groep met behulp van de Azure Portal.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180249"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Een proximity-plaatsings groep maken met behulp van de portal

Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u deze implementeren in een [proximity-plaatsings groep](co-location.md#proximity-placement-groups).

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.


## <a name="create-the-proximity-placement-group"></a>De plaatsings groep voor proximity maken

1. Typ **proximity placement Group** in de zoek opdracht.
1. Selecteer onder **Services** in de zoek resultaten **proximity placement groups**.
1. Selecteer op de pagina **proximity placement groups** de optie **toevoegen**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details**of het juiste abonnement is geselecteerd.
1. Selecteer in **resource groep** de optie **nieuwe maken** om een nieuwe groep te maken of selecteer een bestaande resource groep in de vervolg keuzelijst.
1. Selecteer in **regio** de locatie waar u de Proximity-plaatsings groep wilt maken.
1. Typ in **proximity-naam voor plaatsings groep** een naam en selecteer vervolgens **controleren + maken**.
1. Nadat de validatie is geslaagd, selecteert u **maken** om de Proximity-plaatsings groep te maken.

    ![Scherm afbeelding van het maken van een proximity-plaatsings groep](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Een VM maken

1. Ga tijdens het maken van een virtuele machine in de portal naar het tabblad **Geavanceerd** . 
1. Selecteer in de selectie voor de **plaatsings groep** de juiste plaatsings groep. 

    ![Scherm afbeelding van de sectie locatie van proximity-groep bij het maken van een nieuwe virtuele machine in de portal](./media/ppg/vm-ppg.png)

1. Wanneer u klaar bent met het maken van alle andere vereiste selecties, selecteert u **controleren + maken**.
1. Nadat de validatie is door gegeven, selecteert u **maken** om de virtuele machine in de plaatsings groep te implementeren.




## <a name="next-steps"></a>Volgende stappen

U kunt ook de [Azure PowerShell](proximity-placement-groups.md) gebruiken om proximity-plaatsings groepen te maken.

