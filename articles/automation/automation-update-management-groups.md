---
title: Dynamische groepen met Azure Automation Updatebeheer gebruiken
description: In dit artikel wordt beschreven hoe dynamische groepen werken met Azure Automation Updatebeheer.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690780"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Met Updatebeheer kunt u een dynamische groep van Azure-of niet-Azure-Vm's richten op update-implementaties. Als u een dynamische groep gebruikt, hoeft u uw implementatie niet te bewerken om machines bij te werken.

> [!NOTE]
> Dynamische groepen werken niet met klassieke Vm's.

U kunt dynamische groepen voor Azure-of niet-Azure-machines definiëren op basis van **Update beheer** in de Azure Portal. Zie [updates voor meerdere virtuele machines van Azure beheren](manage-update-multi.md).

Een dynamische groep wordt gedefinieerd door een query die Azure Automation evalueert tijdens de implementatie. Zelfs als een dynamische groeps query een groot aantal machines ophaalt, kan Azure Automation Maxi maal 1000 machines tegelijk verwerken. Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Als u verwacht dat u meer dan 1000 computers wilt bijwerken, raden we u aan om de updates te splitsen in meerdere update planningen. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Dynamische groepen voor Azure-machines definiëren

Bij het definiëren van een dynamische groeps query voor Azure-machines, kunt u de volgende items gebruiken om de dynamische groep te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Klik op **voor beeld**om de resultaten van uw dynamische groeps query te bekijken. In het voor beeld wordt het groepslid maatschap op de huidige tijd weer gegeven. In het voor beeld wordt gezocht naar computers die de tag `Role` voor de groep **BackendServer**hebben. Als deze tag wordt toegevoegd aan meer computers, worden deze toegevoegd aan toekomstige implementaties op basis van die groep.

![Preview-groepen](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Dynamische groepen voor niet-Azure-machines definiëren

Een dynamische groep voor niet-Azure-machines gebruikt opgeslagen Zoek opdrachten, ook wel computer groepen genoemd. Zie [een computer groep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoek opdracht. Zodra uw opgeslagen zoek opdracht is gemaakt, kunt u deze selecteren in de lijst met opgeslagen Zoek **opdrachten in het** Azure Portal. Klik op **voor beeld** om de computers in de opgeslagen zoek opdracht te bekijken.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een dynamische groep hebt gemaakt, kunt u [een update-implementatie maken](automation-tutorial-update-management.md).
