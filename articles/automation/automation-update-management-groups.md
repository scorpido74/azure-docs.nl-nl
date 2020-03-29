---
title: Dynamische groepen gebruiken met Azure Update Management
description: In dit artikel wordt beschreven hoe dynamische groepen werken met Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420402"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Updatebeheer biedt de mogelijkheid om een dynamische groep Azure- of niet-Azure VM's te targeten voor update-implementaties. Deze groepen worden geëvalueerd tijdens de implementatie, zodat u uw implementatie niet hoeft te bewerken om machines toe te voegen.

## <a name="azure-machines"></a>Azure-machines

Deze groepen worden gedefinieerd door een query, wanneer een update-implementatie begint, worden de leden van die groep geëvalueerd. Dynamische groepen werken niet met klassieke VM's. Bij het definiëren van uw query kunnen de volgende items samen worden gebruikt om de dynamische groep in te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Als u een voorbeeld wilt bekijken van de resultaten van een dynamische groep, klikt u op de knop **Voorbeeld.** In deze preview ziet u het groepslidmaatschap op dat moment, in dit voorbeeld zoeken we naar machines met de **tagrol** die gelijk is aan **BackendServer.** Als er meer machines zijn toegevoegd, worden deze toegevoegd aan toekomstige implementaties tegen die groep.

![voorbeeldgroepen](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Niet-Azure-machines

Voor niet-Azure-machines worden opgeslagen zoekopdrachten die ook wel computergroepen worden genoemd, gebruikt om de dynamische groep te maken. Zie [Een computergroep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoekopdracht. Zodra uw groep is gemaakt, u deze selecteren in de lijst met opgeslagen zoekopdrachten. Klik **op Voorbeeld** om een voorbeeld te bekijken van de computers in de opgeslagen zoekopdracht op dat moment.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een dynamische groep hebt gemaakt, u [een update-implementatie maken](automation-tutorial-update-management.md)
