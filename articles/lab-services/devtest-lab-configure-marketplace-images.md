---
title: Installatie kopie-instellingen voor Azure Marketplace configureren in Azure DevTest Labs
description: Configureren welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van een virtuele machine in Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169558"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Installatie kopie-instellingen voor Azure Marketplace configureren in Azure DevTest Labs
DevTest Labs biedt ondersteuning voor het maken van Vm's op basis van installatie kopieën van Azure Marketplace, afhankelijk van hoe u Azure Marketplace-installatie kopieën hebt geconfigureerd om te worden gebruikt in uw Lab. In dit artikel wordt beschreven hoe u kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van Vm's in een lab. Dit zorgt ervoor dat uw team alleen toegang heeft tot de Marketplace-installatie kopieën die ze nodig hebben. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecteren welke installatie kopieën van Azure Marketplace zijn toegestaan bij het maken van een VM
1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het gewenste Lab. 
4. Selecteer op de Blade van het lab de optie **configuratie en beleid**.
5. Selecteer de optie **Marketplace-installatie kopieën**op de Blade **configuratie en beleid** van de **virtuele machine**.
6. Geef op of u wilt dat alle gekwalificeerde Azure Marketplace-installatie kopieën beschikbaar zijn voor gebruik als basis van een nieuwe virtuele machine. Als u **Ja**selecteert, zijn alle Azure Marketplace-installatie kopieën die voldoen aan de volgende criteria, toegestaan in het Lab:
   
   * De installatie kopie maakt één virtuele machine **en**
   * De installatie kopie gebruikt Azure Resource Manager voor het inrichten van Vm's **en**
   * De installatie kopie vereist geen aanschaf van een extra licentie plan
     
     Als u geen installatie kopieën wilt toestaan of als u wilt opgeven welke installatie kopieën kunnen worden gebruikt, selecteert u **Nee**.
     
     ![Optie waarmee alle Marketplace-installatie kopieën kunnen worden gebruikt als basis installatie kopieën voor virtuele machines](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Als u **Nee** naar de vorige stap selecteert, is het selectie vakje **toegestane installatie kopieën/alles selecteren** ingeschakeld. 
   U kunt deze optie samen met het zoekvak gebruiken om snel alle items die in de lijst worden weer gegeven, te selecteren of te deselecteren.
   * Selecteer de installatie kopieën van Azure Marketplace die u afzonderlijk wilt toestaan voor het maken van VM'S door het selectie vakje van elke afbeelding in te scha kelen.
   * Selecteer niets in de lijst als u geen installatie kopieën van Azure Marketplace wilt toestaan die in het lab moeten worden gebruikt.
   
     ![U kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt als basis installatie kopieën voor virtuele machines](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u hebt geconfigureerd hoe Azure Marketplace-installatie kopieën zijn toegestaan tijdens het maken van een virtuele machine, is de volgende stap het [toevoegen van een virtuele machine aan uw Lab](devtest-lab-add-vm.md).

