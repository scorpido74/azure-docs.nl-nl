---
title: Azure Marketplace-afbeeldingsinstellingen configureren in Azure DevTest Labs
description: Configureren welke Azure Marketplace-afbeeldingen kunnen worden gebruikt bij het maken van een VM in Azure DevTest Labs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169558"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure Marketplace-afbeeldingsinstellingen configureren in Azure DevTest Labs
DevTest Labs ondersteunt het maken van VM's op basis van Azure Marketplace-afbeeldingen, afhankelijk van hoe u Azure Marketplace-afbeeldingen hebt geconfigureerd om in uw lab te worden gebruikt. In dit artikel ziet u hoe u opgeven welke, indien van toepassing, Azure Marketplace-afbeeldingen kunnen worden gebruikt bij het maken van VM's in een lab. Dit zorgt ervoor dat je team alleen toegang heeft tot de Marketplace-afbeeldingen die ze nodig hebben. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecteer welke Azure Marketplace-afbeeldingen zijn toegestaan bij het maken van een vm
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met laboratoria het gewenste lab. 
4. Selecteer **Configuratie en beleid**op het blad van het lab.
5. Selecteer **Marketplace-afbeeldingen**op **het configuratie- en beleidsblad** van het lab onder **Virtual Machine Bases.**
6. Geef op of u wilt dat alle gekwalificeerde Azure Marketplace-afbeeldingen beschikbaar zijn voor gebruik als basis van een nieuwe virtuele machine. Als u **Ja**selecteert, zijn alle Azure Marketplace-afbeeldingen die aan alle volgende criteria voldoen, toegestaan in het lab:
   
   * De afbeelding maakt één vm **en**
   * De afbeelding maakt gebruik van Azure Resource Manager om VM's in te richten **en**
   * De afbeelding vereist geen aankoop van een extra licentieplan
     
     Als u wilt dat er geen afbeeldingen worden toegestaan of als u wilt opgeven welke afbeeldingen kunnen worden gebruikt, selecteert u **Nee**.
     
     ![Optie om alle Marketplace-afbeeldingen te gebruiken als basisafbeeldingen voor VM's](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Als u **Nee** bij de vorige stap selecteert, is het selectievakje **Toegestane afbeeldingen/Alles inschakelen** ingeschakeld. 
   U deze optie samen met het zoekvak gebruiken om snel alle items in de lijst te selecteren of uit te schakelen.
   * Schakel de Azure Marketplace-afbeeldingen in die u wilt toestaan voor het afzonderlijk maken van vm's door het bijbehorende selectievakje van elke afbeelding aan te vinken.
   * Selecteer niets in de lijst als u niet wilt toestaan dat Azure Marketplace-afbeeldingen in het lab worden gebruikt.
   
     ![U opgeven welke Azure Marketplace-afbeeldingen kunnen worden gebruikt als basisafbeeldingen voor VM's](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u hebt geconfigureerd hoe Azure Marketplace-afbeeldingen zijn toegestaan bij het maken van een vm, is de volgende stap het [toevoegen van een VM aan uw lab.](devtest-lab-add-vm.md)

