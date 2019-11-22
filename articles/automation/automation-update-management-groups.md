---
title: Dynamische groepen gebruiken met Azure Updatebeheer
description: In dit artikel wordt beschreven hoe dynamische groepen werken met Azure Automation Updatebeheer.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 022c2061febb54666acee1cfed4ec595421660a3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278723"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dynamische groepen gebruiken met Updatebeheer

Updatebeheer biedt de mogelijkheid om een dynamische groep Azure-of niet-Azure Vm's te richten op update-implementaties. Deze groepen worden geëvalueerd op het moment van implementatie, zodat u uw implementatie niet hoeft te bewerken om machines toe te voegen.

## <a name="azure-machines"></a>Azure-machines

Deze groepen worden gedefinieerd door een query, wanneer de implementatie van een update wordt gestart, worden de leden van die groep geëvalueerd. Dynamische groepen werken niet met klassieke Vm's. Bij het definiëren van uw query kunnen de volgende items samen worden gebruikt om de dynamische groep te vullen:

* Abonnement
* Resourcegroepen
* Locaties
* Tags

![Groepen selecteren](./media/automation-update-management/select-groups.png)

Als u de resultaten van een dynamische groep wilt bekijken, klikt u op de knop **Preview** . In deze preview wordt op dat moment het groepslid maatschap weer gegeven, in dit voor beeld wordt gezocht naar machines met de label **functie** is gelijk aan **BackendServer**. Als deze tag wordt toegevoegd aan meer computers, worden deze toegevoegd aan toekomstige implementaties op basis van die groep.

![Preview-groepen](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Niet-Azure-machines

Voor niet-Azure-machines worden opgeslagen Zoek opdrachten ook wel computer groepen genoemd. deze worden gebruikt om de dynamische groep te maken. Zie [een computer groep maken](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)voor meer informatie over het maken van een opgeslagen zoek opdracht. Nadat de groep is gemaakt, kunt u deze selecteren in de lijst met opgeslagen Zoek opdrachten. Klik op **voor beeld** om op dat moment een voor beeld van de computers in de opgeslagen zoek opdracht te bekijken.

![Groepen selecteren](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u een dynamische groep hebt gemaakt, kunt u [een update-implementatie maken](automation-tutorial-update-management.md)
