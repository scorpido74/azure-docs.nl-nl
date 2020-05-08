---
title: Zelf studie-distributie van virtuele machines in azure Linux configureren
description: In deze zelf studie leert u hoe u een pijp lijn met doorlopende implementatie (CD) kunt instellen. Met deze pijp lijn wordt een groep virtuele machines van Azure Linux bijgewerkt met behulp van de strategie voor blauw-groen-implementatie.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: a98989ed48e515cafeca27ae492c83efca6002c4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871610"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-de implementatie strategie voor blauw groen voor virtuele machines van Azure Linux configureren

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS)-CI/CD configureren

Azure-pijp lijnen bieden een volledig uitgeruste set hulpprogram ma's voor CI/CD-automatisering voor implementaties op virtuele machines. U kunt een pijp lijn voor continue levering configureren voor een Azure-VM vanuit de Azure Portal.

In dit artikel wordt beschreven hoe u een CI/CD-pijp lijn instelt die gebruikmaakt van de blauw groene strategie voor implementaties met een meervoudige machine. De Azure Portal biedt ook ondersteuning voor andere strategieën als [rollen](https://aka.ms/AA7jlh8) en [Canarische](https://aka.ms/AA7jdrz).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD op virtuele machines configureren

U kunt virtuele machines als doelen toevoegen aan een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). U kunt deze vervolgens richten op updates voor alle machines. Nadat u op machines hebt geïmplementeerd, kunt u de **implementatie geschiedenis** binnen een implementatie groep bekijken. Met deze weer gave kunt u de virtuele machine traceren naar de pijp lijn en vervolgens naar de door voer.

### <a name="blue-green-deployments"></a>Blue-groene implementaties

Een Blue-groen implementatie vermindert downtime door een identieke stand-by omgeving te hebben. Er is op elk moment slechts één omgeving actief.

Tijdens de voor bereiding voor een nieuwe release voltooit u de laatste fase van de test in de groene omgeving. Nadat de software in de groene omgeving werkt, schakelt u verkeer uit zodat alle inkomende aanvragen naar de groene omgeving gaan. De blauwe omgeving is inactief.

Met de optie continue levering kunt u blauwe, groene implementaties configureren op uw virtuele machines vanuit de Azure Portal. Dit is de stapsgewijze stapsgewijze instructies:

1. Meld u aan bij de Azure Portal en navigeer naar een virtuele machine.
1. Selecteer in het meest linkse deel venster van de VM-instellingen **continue levering**. Selecteer vervolgens **configureren**.

   ![Het deel venster continue levering met de knop configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Selecteer in het configuratie scherm de optie **Azure DevOps Organization** om een bestaand account te kiezen of maak een nieuwe. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  

   ![Het deel venster continu bezorgen](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen. Dev, testen, bedoeld en productie zijn voor beelden. U kunt een nieuwe implementatie groep maken of een bestaande implementeren.
1. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Het gepubliceerde pakket moet een implementatie script met de naam Deploy. ps1 of deploy.sh hebben in de map deployscripts in de hoofdmap van het pakket. De pijp lijn voert dit implementatie script uit.
1. Selecteer in **implementatie strategie** **blauw-groen**.
1. Voeg een ' Blue ' of ' groen ' tag toe aan Vm's die deel moeten uitmaken van Blue-groen implementaties. Als een virtuele machine een stand-by-functie heeft, moet u deze als ' groen ' labelen. Als dat niet het geval is, kunt u deze als ' Blue ' labelen.

   ![Het deel venster continue levering, waarbij de waarde voor de implementatie strategie blauw-groen is gekozen](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selecteer **OK** om de pijp lijn voor continue levering te configureren die u wilt implementeren op de virtuele machine.

   ![De blauwe groene pijp lijn](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. De implementatie Details voor de virtuele machine worden weer gegeven. U kunt de koppeling selecteren om naar de release pijplijn in azure DevOps te gaan. Selecteer in de release pijplijn de optie **bewerken** om de pijplijn configuratie weer te geven. De pijp lijn heeft deze drie fasen:

   1. Deze fase is een fase van de implementatie groep. Toepassingen worden geïmplementeerd op stand-by-Vm's, die zijn gelabeld als ' groen '.
   1. In deze fase wordt de pijp lijn onderbroken en wordt gewacht op hand matige interventie om de uitvoering te hervatten. Gebruikers kunnen de uitvoering van de pijp lijn hervatten zodra ze hand matig de stabiliteit van de implementatie naar Vm's met de label "groen" hebben gegarandeerd.
   1. Deze fase verwisselt de blauwe en groene Tags in de Vm's. Dit zorgt ervoor dat Vm's met oudere toepassings versies nu als ' groen ' worden gelabeld. Tijdens de volgende pijplijn uitvoering worden toepassingen geïmplementeerd op deze Vm's.

      ![Het deel venster implementatie groep voor de taak Blue-groen implementeren](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Met de taak Deploy script uitvoeren wordt standaard het implementatie script Deploy. ps1 of deploy.sh uitgevoerd. Het script bevindt zich in de map deployscripts in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build pijp lijn de implementatie publiceert in de hoofdmap van het pakket.

   ![Het deel venster artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatie strategieën

- [De strategie voor Rolling implementatie configureren](https://aka.ms/AA7jlh8)
- [De strategie voor de distributie van de Canarische implementatie configureren](https://aka.ms/AA7jdrz)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

U kunt eenvoudig aan de slag met Azure. Met Azure DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service door het volgende te selecteren:

- Een toepassings taal
- Een runtime
- Een Azure-service

[Meer informatie](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Implementeren op virtuele machines van Azure met behulp van Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
