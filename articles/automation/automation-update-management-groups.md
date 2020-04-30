---
title: Dynamische groepen met Azure Automation Updatebeheer gebruiken
description: In dit artikel wordt beschreven hoe dynamische groepen werken met Azure Automation Updatebeheer.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617448"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Met Updatebeheer kunt u een dynamische groep van Azure-of niet-Azure-Vm's richten op update-implementaties. Deze groepen, die door query's worden gedefinieerd, worden geëvalueerd tijdens de implementatie, zodat u de implementatie niet hoeft te bewerken om computers toe te voegen.

## <a name="azure-machines"></a>Azure-machines

Dynamische groepen werken niet met klassieke Vm's. Bij het definiëren van uw query kunnen de volgende items samen worden gebruikt om een dynamische groep te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Als u de resultaten van een dynamische groep wilt bekijken, klikt u op **voor beeld**. In het voor beeld wordt het groepslid maatschap op de huidige tijd weer gegeven. In het voor beeld wordt gezocht naar computers die de tag `Role` voor de groep **BackendServer**hebben. Als deze tag wordt toegevoegd aan meer computers, worden deze toegevoegd aan toekomstige implementaties op basis van die groep.

![Preview-groepen](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Niet-Azure-machines

Voor niet-Azure-machines worden opgeslagen Zoek opdrachten, ook wel computer groepen genoemd, gebruikt om de dynamische groep te maken. Zie [een computer groep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoek opdracht. Zodra de groep is gemaakt, kunt u deze selecteren in de lijst met opgeslagen Zoek opdrachten. Klik op **voor beeld** om op dat moment een voor beeld van de computers in de opgeslagen zoek opdracht te bekijken.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een dynamische groep hebt gemaakt, kunt u [een update-implementatie maken](automation-tutorial-update-management.md).
