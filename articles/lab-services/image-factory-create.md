---
title: Een afbeeldingsfabriek maken in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel ziet u hoe u een aangepaste afbeeldingsfabriek instelt met behulp van voorbeeldscripts die beschikbaar zijn in de Git-opslagplaats (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759445"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Een aangepaste afbeeldingsfabriek maken in Azure DevTest Labs
In dit artikel ziet u hoe u een aangepaste afbeeldingsfabriek instelt met behulp van voorbeeldscripts die beschikbaar zijn in de [Git-opslagplaats.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)

## <a name="whats-an-image-factory"></a>Wat is een beeldfabriek?
Een beeldfabriek is een configuratie-als-code-oplossing die afbeeldingen regelmatig automatisch bouwt en distribueert met alle gewenste configuraties. De beelden in de beeldfabriek zijn altijd up-to-date, en het voortdurende onderhoud is bijna nul zodra het hele proces is geautomatiseerd. En omdat alle vereiste configuraties al in de afbeelding staan, bespaart dit de tijd om het systeem handmatig te configureren nadat een VM is gemaakt met het basisbesturingssysteem.

De belangrijke accelerator om een ontwikkelaar desktop naar een kant-en-klare status in DevTest Labs is met behulp van aangepaste afbeeldingen. Het nadeel van aangepaste afbeeldingen is dat er iets extra's te onderhouden in het lab. Proefversies van producten verlopen bijvoorbeeld na verloop van tijd (of) nieuw uitgebrachte beveiligingsupdates worden niet toegepast, waardoor we de aangepaste afbeelding periodiek moeten vernieuwen. Met een afbeeldingsfabriek hebt u een definitie van de afbeelding ingecheckt in broncodebeheer en hebt u een geautomatiseerd proces om aangepaste afbeeldingen te produceren op basis van de definitie.

De oplossing maakt de snelheid van het maken van virtuele machines van aangepaste afbeeldingen mogelijk, terwijl extra lopende onderhoudskosten worden geëlimineerd. Met deze oplossing u automatisch aangepaste afbeeldingen maken, distribueren naar andere DevTest Labs en de oude afbeeldingen met pensioen laten gaan. In de volgende video leert u over de beeldfabriek en hoe deze wordt geïmplementeerd met DevTest Labs.  Alle Azure Powershell-scripts zijn vrij beschikbaar [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)en bevinden zich hier: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Beeld op hoog niveau van de oplossing
De oplossing maakt de snelheid van het maken van virtuele machines van aangepaste afbeeldingen mogelijk, terwijl extra lopende onderhoudskosten worden geëlimineerd. Met deze oplossing u automatisch aangepaste afbeeldingen maken en distribueren naar andere DevTest Labs. U gebruikt Azure DevOps (voorheen Visual Studio Team Services) als orchestration-engine voor het automatiseren van alle bewerkingen in de DevTest Labs.

![Beeld op hoog niveau van de oplossing](./media/create-image-factory/high-level-view-of-solution.png)

Er is een [VSTS-extensie voor DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) waarmee u deze afzonderlijke stappen uitvoeren:

- Aangepaste installatiekopie maken
- VM maken
- VM verwijderen
- Omgeving maken
- Omgeving verwijderen
- Omgeving bevolken

Het gebruik van de DevTest Labs-extensie is een eenvoudige manier om aan de slag te gaan met het automatisch maken van aangepaste afbeeldingen in DevTest Labs.

Er is een alternatieve implementatie met PowerShell-script voor een complexer scenario. Met PowerShell u een beeldfabriek volledig automatiseren op basis van DevTest Labs die kan worden gebruikt in uw continuous integration and continuous delivery (CI/CD) toolchain. De principes die in deze alternatieve oplossing worden gevolgd zijn:

- Veelvoorkomende updates vereisen geen wijzigingen in de afbeeldingsfabriek. (bijvoorbeeld het toevoegen van een nieuw type aangepaste afbeelding, het automatisch terugtrekken van oude afbeeldingen, het toevoegen van een nieuw 'eindpunt' DevTest Labs om aangepaste afbeeldingen te ontvangen, enzovoort.)
- Veelvoorkomende wijzigingen worden ondersteund door broncodebeheer (infrastructuur als code)
- DevTest Labs die aangepaste afbeeldingen ontvangt, bevinden zich mogelijk niet in hetzelfde Azure-abonnement (labsspanabonnementen)
- PowerShell-scripts moeten herbruikbaar zijn, zodat we extra fabrieken kunnen draaien als dat nodig is

## <a name="next-steps"></a>Volgende stappen
Ga verder naar het volgende artikel in deze sectie: [Een afbeeldingsfabriek uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md)
