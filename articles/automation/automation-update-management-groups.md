---
title: Dynamische groepen met Azure Automation Updatebeheer gebruiken
description: In dit artikel leest u hoe u dynamische groepen met Azure Automation Updatebeheer kunt gebruiken.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830936"
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

Als u met Updatebeheer wilt werken, raadpleegt u [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md).
