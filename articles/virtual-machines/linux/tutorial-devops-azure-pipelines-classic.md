---
title: Zelf studie-Rolling implementaties configureren voor virtuele machines van Azure Linux
description: In deze zelf studie leert u hoe u een pijp lijn met doorlopende implementatie (CD) kunt instellen. Met deze pijp lijn wordt een groep virtuele Azure Linux-machines incrementeel bijgewerkt met behulp van de implementatie strategie met rollen.
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
ms.openlocfilehash: 28f093bc464a45862d3b253d628b7ae03810f81a
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871229"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-de strategie voor Rolling implementatie configureren voor virtuele Azure Linux-machines

Azure DevOps is een ingebouwde Azure-service waarmee elk deel van het DevOps-proces voor elke Azure-resource wordt geautomatiseerd. Of uw app gebruikmaakt van virtuele machines, Web-apps, Kubernetes of een andere resource, u kunt infra structuur als code (IaaC), continue integratie, continue tests, continue levering en continue bewaking met Azure en Azure DevOps implementeren.

![De Azure Portal met Azure DevOps geselecteerd onder Services](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS)-CI/CD configureren

Azure-pijp lijnen bieden een volledig uitgeruste set hulpprogram ma's voor CI/CD-automatisering voor implementaties op virtuele machines. U kunt een pijp lijn voor continue levering configureren voor een Azure-VM vanuit de Azure Portal.

In dit artikel wordt beschreven hoe u een CI/CD-pijp lijn instelt voor implementaties met een rolling van de Azure Portal. De Azure Portal biedt ook ondersteuning voor andere strategieën, zoals [Canarische](https://aka.ms/AA7jdrz) en [Blue-groen](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD op virtuele machines configureren

U kunt virtuele machines als doelen toevoegen aan een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). U kunt deze vervolgens richten op updates voor alle machines. Nadat u op machines hebt geïmplementeerd, kunt u de **implementatie geschiedenis** binnen een implementatie groep bekijken. Met deze weer gave kunt u de virtuele machine traceren naar de pijp lijn en vervolgens naar de door voer.

### <a name="rolling-deployments"></a>Rolling implementaties

Bij elke iteratie vervangt een rolling implementatie exemplaren van de vorige versie van de toepassing. Ze worden vervangen door exemplaren van de nieuwe versie op een vaste set machines (Rolling set). De volgende procedure laat zien hoe u een rolling update kunt configureren voor virtuele machines.

Met de optie continue levering kunt u Rolling updates configureren voor uw virtuele machines in de Azure Portal. Dit is de stapsgewijze stapsgewijze instructies:

1. Meld u aan bij de Azure Portal en navigeer naar een virtuele machine.
1. Selecteer in het meest linkse deel venster van de VM-instellingen **continue levering**. Selecteer vervolgens **configureren**.

   ![Het deel venster continue levering met de knop configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Selecteer in het configuratie scherm de optie **Azure DevOps Organization** om een bestaand account te kiezen of maak een nieuwe. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  

   ![Het deel venster continu bezorgen](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen. Dev, testen, bedoeld en productie zijn voor beelden. U kunt een nieuwe implementatie groep maken of een bestaande implementeren.
1. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Het gepubliceerde pakket moet een implementatie script met de naam Deploy. ps1 of deploy.sh hebben in de map deployscripts in de hoofdmap van het pakket. De pijp lijn voert dit implementatie script uit.
1. Selecteer in **implementatie strategie** **Rolling**.
1. U kunt desgewenst elke machine met de bijbehorende rol labelen. De Tags web en DB zijn voor beelden. Deze Tags helpen u bij het richten op alleen Vm's met een specifieke rol.
1. Selecteer **OK** om de doorlopende bezorgings pijplijn te configureren.
1. Nadat de configuratie is voltooid, hebt u een doorlopende pijp lijn geconfigureerd om te implementeren op de virtuele machine.  

   ![Het deel venster continue levering met implementatie geschiedenis](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. De implementatie Details voor de virtuele machine worden weer gegeven. U kunt de koppeling selecteren om naar de pijp lijn te gaan, **versie 1** om de implementatie te bekijken of **bewerken** om de release-pijplijn definitie te wijzigen.

1. Als u meerdere Vm's configureert, herhaalt u stap 2 tot en met 4 om andere Vm's toe te voegen aan de implementatie groep. Als u een implementatie groep selecteert die al een pijplijn uitvoering heeft, worden de Vm's net aan de implementatie groep toegevoegd. Er worden geen nieuwe pijp lijnen gemaakt.
1. Nadat de configuratie is voltooid, selecteert u de pijplijn definitie, gaat u naar de Azure DevOps-organisatie en selecteert u **bewerken** voor de release pijplijn.

   ![De voortschrijdende pijp lijn bewerken](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selecteer **1 taak, 1 taak** in de **ontwikkelings** fase. Selecteer de **implementatie** fase.

   ![Taken voor het uitvoeren van een pijp lijn met de geselecteerde taak implementeren](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. In het rechter deel venster configuratie kunt u het aantal machines opgeven dat u parallel wilt implementeren in elke iteratie. Als u op meerdere machines tegelijk wilt implementeren, kunt u het aantal machines opgeven als een percentage met behulp van de schuif regelaar.  

1. De taak Deploy-script uitvoeren voert standaard het implementatie script Deploy. ps1 of deploy.sh uit. Het script bevindt zich in de map deployscripts in de hoofdmap van het gepubliceerde pakket.

   ![Het deel venster artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatie strategieën

- [De strategie voor de distributie van de Canarische implementatie configureren](https://aka.ms/AA7jdrz)
- [De implementatie strategie voor blauw-groen configureren](https://aka.ms/AA83fwu)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

U kunt eenvoudig aan de slag met Azure. Met Azure DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service door het volgende te selecteren:

- Een toepassings taal
- Een runtime
- Een Azure-service
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Aanvullende bronnen

- [Implementeren op virtuele machines van Azure met behulp van Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
