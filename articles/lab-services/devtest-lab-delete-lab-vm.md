---
title: Een lab of vm in een lab in Azure DevTest Labs verwijderen
description: In dit artikel ziet u hoe u een lab verwijdert of een vm in een lab verwijdert met behulp van de Azure Portal (Azure DevTest Labs).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270808"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Een lab of vm in een lab in Azure DevTest Labs verwijderen
In dit artikel ziet u hoe u een lab of vm in een lab verwijdert.

## <a name="delete-a-lab"></a>Een lab verwijderen
Wanneer u een instantie DevTest Labs uit een resourcegroep verwijdert, voert de de service DevTest Labs de volgende acties uit: 

- Alle resources die automatisch zijn gemaakt op het moment van het maken van het lab, worden automatisch verwijderd. De brongroep zelf wordt niet verwijderd. Als u handmatig resources in deze brongroep hebt gemaakt, verwijdert de service deze niet. 
- Alle VM's in het lab en resourcegroepen die aan deze VM's zijn gekoppeld, worden automatisch verwijderd. Wanneer u een VM in een lab maakt, maakt de service resources (schijf, netwerkinterface, openbaar IP-adres, enz.) voor de VM in een afzonderlijke brongroep. Als u echter handmatig extra resources in deze resourcegroepen maakt, verwijdert de de service DevTest Labs deze resources en de brongroep niet. 

Ga als volgt te werk om een lab te verwijderen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle resource** in menu aan de linkerkant, selecteer **DevTest Labs** voor het type service en selecteer het lab.

    ![Selecteer uw lab](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Klik op de pagina **DevTest Lab** op **Verwijderen** op de werkbalk. 

    ![De knop Verwijderen](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Voer **op** de pagina Bevestiging de **naam** van uw lab in en selecteer **Verwijderen**. 

    ![Bevestigen](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Als u de status van de bewerking wilt zien, selecteert u het pictogram **Meldingen** (Bel). 

    ![Meldingen](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Een vm in een lab verwijderen
Als ik een VM in een lab verwijder, worden sommige resources (niet alle) die zijn gemaakt op het moment van het maken van het lab verwijderd. De volgende bronnen worden niet verwijderd: 

-   Sleutelkluis in de hoofdbrongroep
-   Beschikbaarheidsset, load balancer, openbaar IP-adres in de VM-resourcegroep. Deze bronnen worden gedeeld door meerdere VM's in een resourcegroep. 

Virtuele machine, netwerkinterface en schijf die aan de VM zijn gekoppeld, worden verwijderd. 

Ga als volgt te werk om een vm in een lab te verwijderen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle resource** in menu aan de linkerkant, selecteer **DevTest Labs** voor het type service en selecteer het lab.

    ![Selecteer uw lab](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecteer **... (ellips)** voor de VM in de lijst met VM's en selecteer **Verwijderen**. 

    ![VM verwijderen in menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Selecteer **ok**in **het** bevestigingsdialoogvenster. 
5. Als u de status van de bewerking wilt zien, selecteert u het pictogram **Meldingen** (Bel). 

Als u een virtuele machine wilt **verwijderen,** selecteert u **Verwijderen op** de werkbalk zoals in de volgende afbeelding wordt weergegeven:

![VM verwijderen van VM-pagina](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Volgende stappen
Als u een lab wilt maken, raadpleegt u de volgende artikelen: 

- [Een lab maken](devtest-lab-create-lab.md)
- [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)