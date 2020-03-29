---
title: Een gelicentieerde afbeelding inschakelen in uw lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het inschakelen van een gelicentieerde afbeelding in Azure DevTest Labs met behulp van de Azure-portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294252"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Een gelicentieerde afbeelding in uw lab inschakelen in Azure DevTest Labs

In Azure DevTest Labs is een gelicentieerde afbeelding een afbeelding die algemene voorwaarden bevat - meestal van een derde partij - die moeten worden geaccepteerd voordat de afbeelding toegankelijk is voor gebruikers in het lab. In de volgende secties wordt beschreven hoe u met gelicentieerde afbeeldingen werken, zodat ze beschikbaar zijn voor het maken van virtuele machines.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Bepalen of een gelicentieerde afbeelding beschikbaar is voor gebruikers
De eerste stap om gebruikers toe te staan VM's te maken op basis van een gelicentieerde afbeelding, is ervoor te zorgen dat de algemene voorwaarden zijn geaccepteerd voor de gelicentieerde afbeelding. In de volgende stappen ziet u hoe u de aanbiedingsstatus van een gelicentieerde afbeelding bekijken en, indien nodig, de algemene voorwaarden accepteren.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met laboratoria het gewenste lab.  

1. Selecteer **Configuratie en beleid**in het linkerdeelvenster onder **INSTELLINGEN**.

1. Selecteer **Marketplace-afbeeldingen**in het linkerpaneel onder **VIRTUELE MACHINEBASES**. 

    ![Menu-item Marketplace-afbeeldingen](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Er wordt een lijst weergegeven met alle beschikbare marketplace-afbeeldingen, inclusief de **AANBIEDINGsstatus** voor elke afbeelding.

    ![Lijst met marktplaatsafbeeldingen met aanbiedingsstatus voor elke afbeelding](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Een gelicentieerde afbeelding toont een aanbiedingsstatus van 
    
    - **Geaccepteerde voorwaarden:** de gelicentieerde afbeelding is beschikbaar voor gebruikers om VM's te maken. 
    - **Voorwaarden beoordeling nodig:** de gelicentieerde afbeelding is momenteel niet beschikbaar voor gebruikers. De algemene voorwaarden van de licentie moeten worden geaccepteerd voordat labgebruikers deze kunnen gebruiken om VM's te maken. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Een gelicentieerde afbeelding beschikbaar maken voor labgebruikers
Om ervoor te zorgen dat een gelicentieerde afbeelding beschikbaar is voor labgebruikers, moet een labeigenaar met beheerdersmachtigingen eerst de algemene voorwaarden voor die gelicentieerde afbeelding accepteren. Door programmatische implementatie in te schakelen voor het abonnement dat is gekoppeld aan een gelicentieerde afbeelding, worden automatisch de wettelijke voorwaarden en privacyverklaringen voor die afbeelding geaccepteerd. [Werken met Marketplace Images in Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) biedt aanvullende informatie over de programmatische implementatie van marketplace-afbeeldingen.

U programmatische implementatie voor een gelicentieerde afbeelding inschakelen door de volgende stappen te volgen:

1. Ga in de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)naar de lijst met **Marketplace-afbeeldingen**.

1. Identificeer een gelicentieerde afbeelding waarvoor u wilt dat gebruikers toegang hebben, maar waarvan de voorwaarden niet zijn geaccepteerd. U ziet bijvoorbeeld een virtuele machine voor Data Science met een status van **geaccepteerde voorwaarden** of **beoordeling van termen die nodig zijn.**

    ![Venster Programmatische implementatie configureren](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Data Science VM's zijn Azure Virtual Machine-afbeeldingen, vooraf geïnstalleerd, geconfigureerd en getest met verschillende populaire hulpprogramma's die vaak worden gebruikt voor gegevensanalyse, machine learning en AI-training. [Inleiding tot Azure Data Science Virtual Machine voor Linux en Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) biedt veel informatie over DSVMs.
   >
   >

1. Selecteer in de kolom **AANBIEDINGSSTATUS** voor de afbeelding **de optie Termencontrole nodig**.

1. Selecteer in het venster Programmatische implementatie configureren de optie **Inschakelen**.

    ![Venster Programmatische implementatie configureren](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Mogelijk worden er meerdere abonnementen weergegeven in het venster Programmatische implementatie configureren. Zorg ervoor dat u programmatische implementatie alleen inschakelt voor het beoogde abonnement.
   >
   >


1. Selecteer **Opslaan**. 

    In de lijst met marktplaatsafbeeldingen toont die afbeelding nu **Geaccepteerde voorwaarden** en is beschikbaar voor gebruikers om virtuele machines te maken.

> [!NOTE]
> Gebruikers kunnen een aangepaste afbeelding maken op basis van een gelicentieerde afbeelding. Zie [Een aangepaste afbeelding maken uit een VHD-bestand](devtest-lab-create-template.md) voor meer informatie.
>
>


## <a name="related-blog-posts"></a>Gerelateerde blogberichten

- [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Aangepaste afbeeldingen kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste installatiekopie maken vanaf een virtuele machine](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Een aangepaste afbeelding maken op basis van een VHD-bestand](devtest-lab-create-template.md)
- [Een vm toevoegen aan uw lab](devtest-lab-add-vm.md)