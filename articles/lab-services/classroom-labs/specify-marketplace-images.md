---
title: Marketplace-installatie kopieën opgeven voor een lab in Azure Lab Services
description: In dit artikel wordt beschreven hoe u de Marketplace-installatie kopieën kunt opgeven die door Lab Creator kunnen worden gebruikt om Labs te maken in een Lab-account in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444665"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers
Als eigenaar van een labaccount kunt u de Marketplace-installatiekopieën opgeven die labmakers kunnen gebruiken in het labaccount. 

## <a name="select-images-available-for-labs"></a>Beschik bare installatie kopieën voor Labs selecteren
Selecteer **Marketplace-installatiekopieën** in het menu aan de linkerkant. Standaard ziet u de volledige lijst met installatiekopieën (zowel ingeschakelde als uitgeschakelde). U kunt de lijst filteren om alleen ingeschakelde/uitgeschakelde installatiekopieën te bekijken door de optie **Alleen ingeschakeld**/**Alleen uitgeschakeld** in de vervolgkeuzelijst bovenaan te selecteren. 
    
![Pagina Microsoft Azure Marketplace-installatiekopieën](../media/tutorial-setup-lab-account/marketplace-images-page.png)

De Marketplace-installatiekopieën die worden weergegeven in de lijst, zijn de enige die voldoen aan de volgende voorwaarden:
    
- Hiermee wordt een enkele VM gemaakt.
- Maakt gebruik van Azure Resource Manager om VM’s in te richten
- Hiervoor hoeft u geen extra licentieabonnement aan te schaffen

## <a name="disable-images-for-a-lab"></a>Installatie kopieën voor een Lab uitschakelen 
Als u één installatie kopie voor een Lab wilt uitschakelen, selecteert u **... (weglatings tekens)** in de laatste kolom en selecteer **installatie kopie uitschakelen**. 

![Een installatiekopie uitschakelen](../media/tutorial-setup-lab-account/disable-one-image.png) 

U kunt ook het selectie vakje voor de naam van de installatie kopie selecteren en **geselecteerde installatie kopieën** op de werk balk uitschakelen selecteren. 

Als u meerdere installatie kopieën tegelijk wilt uitschakelen, selecteert u de selectie vakjes voor de naam van de afbeelding en selecteert u **geselecteerde afbeeldingen** op de werk balk uitschakelen. 

![Meerdere installatiekopieën uitschakelen](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Installatie kopieën inschakelen voor een Lab
Als u een uitgeschakelde afbeelding wilt inschakelen, selecteert u **... (weglatings tekens)** in de laatste kolom en selecteer **afbeelding inschakelen**. U kunt ook het selectie vakje voor de naam van de installatie kopie selecteren en **geselecteerde installatie kopieën** op de werk balk inschakelen. 

Als u meerdere installatie kopieën tegelijk wilt uitschakelen, selecteert u de selectie vakjes voor de naam van de afbeelding en selecteert u **geselecteerde afbeeldingen** op de werk balk inschakelen. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)
