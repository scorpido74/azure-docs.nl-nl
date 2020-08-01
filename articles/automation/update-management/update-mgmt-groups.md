---
title: Dynamische groepen met Azure Automation Updatebeheer gebruiken
description: In dit artikel leest u hoe u dynamische groepen met Azure Automation Updatebeheer kunt gebruiken.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 20e6d26808964c8e697c694bd796af2851e7ca48
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450191"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Met Updatebeheer kunt u een dynamische groep van Azure-of niet-Azure-Vm's richten op update-implementaties. Als u een dynamische groep gebruikt, hoeft u uw implementatie niet te bewerken om machines bij te werken.

> [!NOTE]
> Dynamische groepen werken niet met klassieke Vm's.

U kunt dynamische groepen voor Azure-of niet-Azure-machines definiëren op basis van **Update beheer** in de Azure Portal. Zie [updates voor virtuele machines beheren](update-mgmt-manage-updates-for-vm.md).

Een dynamische groep wordt gedefinieerd door een query die Azure Automation evalueert tijdens de implementatie. Zelfs als een dynamische groeps query een groot aantal machines ophaalt, kan Azure Automation Maxi maal 1000 machines tegelijk verwerken. Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

> [!NOTE]
> Als u verwacht dat u meer dan 1000 computers wilt bijwerken, raden we u aan om de updates te splitsen in meerdere update planningen. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Dynamische groepen voor Azure-machines definiëren

Bij het definiëren van een dynamische groeps query voor Azure-machines, kunt u de volgende items gebruiken om de dynamische groep te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/update-mgmt-groups/select-groups.png)

Klik op **voor beeld**om de resultaten van uw dynamische groeps query te bekijken. In het voor beeld wordt het groepslid maatschap op de huidige tijd weer gegeven. In het voor beeld wordt gezocht naar computers die de tag `Role` voor de groep **BackendServer**hebben. Als deze tag wordt toegevoegd aan meer computers, worden deze toegevoegd aan toekomstige implementaties op basis van die groep.

![Preview-groepen](./media/update-mgmt-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Dynamische groepen voor niet-Azure-machines definiëren

Een dynamische groep voor niet-Azure-machines gebruikt opgeslagen Zoek opdrachten, ook wel computer groepen genoemd. Zie [een computer groep maken](../../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoek opdracht. Zodra uw opgeslagen zoek opdracht is gemaakt, kunt u deze selecteren in de lijst met opgeslagen Zoek **opdrachten in het** Azure Portal. Klik op **voor beeld** om de computers in de opgeslagen zoek opdracht te bekijken.

![Groepen selecteren](./media/update-mgmt-groups/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

U kunt [Azure monitor logboeken opvragen](update-mgmt-query-logs.md) om update-evaluaties, implementaties en andere gerelateerde beheer taken te analyseren. Het bevat vooraf gedefinieerde query's waarmee u aan de slag kunt gaan.
