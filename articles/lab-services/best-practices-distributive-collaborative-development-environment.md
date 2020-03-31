---
title: Gedistribueerde samenwerkingsontwikkeling van Azure DevTest Labs-bronnen
description: Biedt best practices voor het opzetten van een gedistribueerde en collaboratieve ontwikkelomgeving om DevTest Labs-bronnen te ontwikkelen.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170111"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Aanbevolen procedures voor gedistribueerde en collaboratieve ontwikkeling van Azure DevTest Labs-bronnen
Gedistribueerde collaboratieve ontwikkeling stelt verschillende teams of mensen in staat om een codebasis te ontwikkelen en te onderhouden. Om succesvol te zijn, is het ontwikkelingsproces afhankelijk van de mogelijkheid om informatie te maken, te delen en te integreren. Dit sleutelontwikkelingsprincipe kan worden gebruikt in Azure DevTest Labs. Er zijn verschillende soorten resources binnen een lab die vaak worden verdeeld over verschillende laboratoria binnen een onderneming. De verschillende soorten resources zijn gericht op twee gebieden:

- Resources die intern zijn opgeslagen in het lab (lab-based)
- Bronnen die zijn opgeslagen in [externe opslagplaatsen die zijn verbonden met het lab](devtest-lab-add-artifact-repo.md) (code repository-based). 

Dit document beschrijft enkele aanbevolen procedures die samenwerking en distributie over meerdere teams mogelijk maken en tegelijkertijd zorgen voor aanpassing en kwaliteit op alle niveaus.

## <a name="lab-based-resources"></a>Lab-gebaseerde bronnen

### <a name="custom-virtual-machine-images"></a>Aangepaste virtuele machineafbeeldingen
U een gemeenschappelijke bron van aangepaste afbeeldingen die worden geïmplementeerd om labs op een nachtelijke basis. Zie [Afbeeldingsfabriek](image-factory-create.md)voor gedetailleerde informatie .    

### <a name="formulas"></a>Formules
[Formules](devtest-lab-manage-formulas.md) zijn labspecifiek en hebben geen distributiemechanisme. De lableden doen alle ontwikkeling van formules. 

## <a name="code-repository-based-resources"></a>Op coderepository gebaseerde bronnen
Er zijn twee verschillende functies die zijn gebaseerd op coderepositories, artefacten en omgevingen. Dit artikel gaat over de functies en hoe u het meest effectief repositories en workflow instellen om de mogelijkheid te bieden om de beschikbare artefacten en omgevingen op organisatie- of teamniveau aan te passen.  Deze werkstroom is gebaseerd op de strategie voor [het vertakken van standaardbroncode.](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops) 

### <a name="key-concepts"></a>Belangrijkste concepten
De broninformatie voor artefacten bevat metadata, scripts. De broninformatie voor omgevingen bevat metagegevens en Resource Manager-sjablonen met ondersteunende bestanden zoals PowerShell-scripts, DSC-scripts, Zip-bestanden, enzovoort.  

### <a name="repository-structure"></a>Repository-structuur  
De meest voorkomende configuratie voor broncodebeheer (SCC) is het instellen van een multi-tier structuur voor het opslaan van codebestanden (Resource Manager-sjablonen, metagegevens en scripts) die worden gebruikt in de labs. Maak in het bijzonder verschillende opslagplaatsen om resources op te slaan die worden beheerd door de verschillende niveaus van het bedrijf:   

- Bedrijfsbrede middelen.
- Business unit/Division-wide resources
- Teamspecifieke bronnen.

Elk van deze niveaus link naar een andere repository waar de master branch is vereist om van de productiekwaliteit. De [branches](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) in elke opslagplaats zouden voor de ontwikkeling van die specifieke bronnen (artefacten of sjablonen) zijn. Deze structuur sluit goed aan bij DevTest Labs, omdat u eenvoudig meerdere repositories en meerdere branches tegelijk verbinden met de laboratoria van de organisatie. De naam van de opslagplaats is opgenomen in de gebruikersinterface (UI) om verwarring te voorkomen wanneer er identieke namen, beschrijvingen en uitgevers zijn.
     
Het volgende diagram toont twee repositories: een bedrijfsrepository die wordt onderhouden door de IT-divisie en een divisierepository die wordt onderhouden door de R-&D-divisie.

![Een voorbeeld distributieve en collaboratieve ontwikkelingsomgeving](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Deze gelaagde structuur zorgt voor ontwikkeling terwijl een hoger niveau van kwaliteit op de master branch, terwijl het hebben van meerdere repositories aangesloten op een lab zorgt voor meer flexibiliteit.

## <a name="next-steps"></a>Volgende stappen    
Zie de volgende artikelen:

- Een opslagplaats toevoegen aan een lab met behulp van de [Azure-portal](devtest-lab-add-artifact-repo.md) of via [azure resourcebeheersjabloon](add-artifact-repository.md)
- [DevTest Labs artefacten](devtest-lab-artifact-author.md)
- [DevTest Labs omgevingen](devtest-lab-create-environment-from-arm.md).
