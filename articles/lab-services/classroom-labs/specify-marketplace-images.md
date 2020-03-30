---
title: Marktplaatsafbeeldingen opgeven voor een lab in Azure Lab Services
description: In dit artikel ziet u hoe u de Marketplace-afbeeldingen opgeeft die de maker van het lab kan gebruiken om labs te maken in een labaccount in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444665"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers
Als eigenaar van een labaccount kunt u de Marketplace-installatiekopieën opgeven die labmakers kunnen gebruiken in het labaccount. 

## <a name="select-images-available-for-labs"></a>Afbeeldingen selecteren die beschikbaar zijn voor labs
Selecteer **Marketplace-installatiekopieën** in het menu aan de linkerkant. Standaard ziet u de volledige lijst met installatiekopieën (zowel ingeschakelde als uitgeschakelde). U de lijst filteren om alleen ingeschakelde/uitgeschakelde afbeeldingen weer te geven door **de**/optie Alleen**uitgeschakeld uitgeschakeld** selecteren te selecteren in de vervolgkeuzelijst bovenaan. 
    
![Pagina Microsoft Azure Marketplace-installatiekopieën](../media/tutorial-setup-lab-account/marketplace-images-page.png)

De Marketplace-installatiekopieën die worden weergegeven in de lijst, zijn de enige die voldoen aan de volgende voorwaarden:
    
- Hiermee wordt een enkele VM gemaakt.
- Maakt gebruik van Azure Resource Manager om VM’s in te richten
- Hiervoor hoeft u geen extra licentieabonnement aan te schaffen

## <a name="disable-images-for-a-lab"></a>Afbeeldingen uitschakelen voor een lab 
Als u één afbeelding voor een lab wilt uitschakelen, selecteert u **... (ellips)** in de laatste kolom en selecteer **Afbeelding uitschakelen**. 

![Een installatiekopie uitschakelen](../media/tutorial-setup-lab-account/disable-one-image.png) 

U ook het selectievakje voor de naam van de afbeelding inschakelen en geselecteerde afbeeldingen op de werkbalk **uitschakelen.** 

Als u meerdere afbeeldingen tegelijk wilt uitschakelen, schakelt u selectievakjes in vóór de afbeeldingsnamen en selecteert u Geselecteerde afbeeldingen op de werkbalk **uitschakelen.** 

![Meerdere installatiekopieën uitschakelen](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Afbeeldingen inschakelen voor een lab
Als u een uitgeschakelde afbeelding wilt inschakelen, selecteert u **... (ellips)** in de laatste kolom en selecteer **Afbeelding inschakelen**. U ook het selectievakje voor de naam van de afbeelding inschakelen en geselecteerde afbeeldingen op de werkbalk **inschakelen.** 

Als u meerdere afbeeldingen tegelijk wilt uitschakelen, schakelt u selectievakjes in vóór de afbeeldingsnamen en selecteert u Geselecteerde afbeeldingen op de werkbalk **inschakelen.** 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als labgebruiker toegang tot klaslokalen](how-to-use-classroom-lab.md)
