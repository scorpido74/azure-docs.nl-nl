---
title: Dynamische groepen gebruiken met Azure Automation Update Management
description: In dit artikel wordt beschreven hoe dynamische groepen werken met Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617448"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Met Updatebeheer u een dynamische groep Azure- of niet-Azure VM's targeten voor update-implementaties. Deze groepen, gedefinieerd door query's, worden geëvalueerd op implementatietijd, zodat u uw implementatie niet hoeft te bewerken om machines toe te voegen.

## <a name="azure-machines"></a>Azure-machines

Dynamische groepen werken niet met klassieke VM's. Bij het definiëren van uw query kunnen de volgende items samen worden gebruikt om een dynamische groep in te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Als u een voorbeeld van de resultaten van een dynamische groep wilt bekijken, klikt u op **Voorbeeld**. In de preview wordt het groepslidmaatschap op dit moment weergegeven. In het voorbeeld zoeken we naar machines `Role` met de tag voor de groep **BackendServer**. Als er meer machines zijn toegevoegd aan deze tag, worden ze toegevoegd aan toekomstige implementaties tegen die groep.

![voorbeeldgroepen](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Niet-Azure-machines

Voor niet-Azure-machines worden opgeslagen zoekopdrachten, ook wel computergroepen genoemd, gebruikt om de dynamische groep te maken. Zie [Een computergroep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoekopdracht. Zodra uw groep is gemaakt, u deze selecteren in de lijst met opgeslagen zoekopdrachten. Klik **op Voorbeeld** om een voorbeeld te bekijken van de computers in de opgeslagen zoekopdracht op dat moment.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een dynamische groep hebt gemaakt, u [een update-implementatie maken.](automation-tutorial-update-management.md)
