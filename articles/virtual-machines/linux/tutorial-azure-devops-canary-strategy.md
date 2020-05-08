---
title: Zelf studie-distributies van de Canarische voor Azure Linux Virtual Machines configureren
description: In deze zelf studie leert u hoe u een pijp lijn met doorlopende implementatie (CD) kunt instellen. Met deze pijp lijn wordt een groep virtuele machines van Azure Linux bijgewerkt met behulp van de strategie voor de distributie van de Canarische-implementatie.
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
ms.openlocfilehash: e0fb26896b79fb23bb0f784c0f23aa3af0593c22
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871852"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-de strategie voor distributie van de Canarische-implementatie voor Azure Linux Virtual Machines configureren

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS)-CI/CD configureren

Azure-pijp lijnen bieden een volledig uitgeruste set hulpprogram ma's voor CI/CD-automatisering voor implementaties op virtuele machines. U kunt een pijp lijn voor continue levering configureren voor een Azure-VM vanuit de Azure Portal.

In dit artikel wordt beschreven hoe u een CI/CD-pijp lijn instelt die gebruikmaakt van de strategie voor de distributie van implementaties met een groot aantal machines. De Azure Portal biedt ook ondersteuning voor andere strategieën als [Rolling](https://aka.ms/AA7jlh8) en [Blue-groen](https://aka.ms/AA83fwu).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD op virtuele machines configureren

U kunt virtuele machines als doelen toevoegen aan een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups). U kunt deze vervolgens richten op updates voor alle machines. Nadat u op machines hebt geïmplementeerd, kunt u de **implementatie geschiedenis** binnen een implementatie groep bekijken. Met deze weer gave kunt u de virtuele machine traceren naar de pijp lijn en vervolgens naar de door voer.

### <a name="canary-deployments"></a>Canarische implementaties

Een distributie met een groot aantal vermindert het risico door de wijzigingen in een kleine subset van gebruikers langzaam uit te vouwen. Wanneer u de nieuwe versie betrouwt, kunt u deze vrijgeven aan meer servers in uw infra structuur en er meer gebruikers naar sturen.

U kunt met behulp van de optie continue levering distributie op uw virtuele machines vanuit de Azure Portal configureren. Dit is de stapsgewijze stapsgewijze instructies:

1. Meld u aan bij de Azure Portal en navigeer naar een virtuele machine.
1. Selecteer in het meest linkse deel venster van de VM-instellingen **continue levering**. Selecteer vervolgens **configureren**.

   ![Het deel venster continue levering met de knop configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Selecteer in het configuratie scherm de optie **Azure DevOps Organization** om een bestaand account te kiezen of maak een nieuwe. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  

   ![Het deel venster continu bezorgen](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen. Dev, testen, bedoeld en productie zijn voor beelden. U kunt een nieuwe implementatie groep maken of een bestaande implementeren.
1. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Het gepubliceerde pakket moet een implementatie script met de naam Deploy. ps1 of deploy.sh hebben in de map deployscripts in de hoofdmap van het pakket. De pijp lijn voert dit implementatie script uit.
1. Selecteer in **implementatie strategie**de optie **Canarische**.
1. Voeg een ' Canarische ' tag toe aan Vm's die deel moeten uitmaken van de distributie van de Canarische. Voeg een ' Prod-tag ' toe aan Vm's die deel uitmaken van implementaties die zijn gemaakt nadat de distributie van de Canarische is geslaagd. Tags helpen u bij het richten op alleen Vm's met een specifieke rol.

   ![Het deel venster continue levering, met de gekozen waarde voor de implementatie strategie](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selecteer **OK** om de pijp lijn voor continue levering te configureren die u wilt implementeren op de virtuele machine.

   ![De Canarische pijp lijn](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. De implementatie Details voor de virtuele machine worden weer gegeven. U kunt de koppeling selecteren om naar de release pijplijn in azure DevOps te gaan. Selecteer in de release pijplijn de optie **bewerken** om de pijplijn configuratie weer te geven. De pijp lijn heeft deze drie fasen:

   1. Deze fase is een fase van de implementatie groep. Toepassingen worden geïmplementeerd op Vm's die zijn gelabeld als ' Canarische '.
   1. In deze fase wordt de pijp lijn onderbroken en wordt gewacht op hand matige interventie om de uitvoering te hervatten.
   1. Dit is opnieuw een fase van de implementatie groep. De update wordt nu geïmplementeerd op Vm's die zijn gelabeld als ' Prod '.

      ![Het deel venster implementatie groep voor de taak distributie distributie](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Zorg ervoor dat ten minste één virtuele machine is gelabeld als ' Prod ' voordat u de pijplijn uitvoering hervat. In de derde fase van de pijp lijn worden toepassingen alleen geïmplementeerd voor virtuele machines die de tag ' Prod ' hebben.

1. Met de taak Deploy script uitvoeren wordt standaard het implementatie script Deploy. ps1 of deploy.sh uitgevoerd. Het script bevindt zich in de map deployscripts in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build pijp lijn de implementatie publiceert in de hoofdmap van het pakket.

   ![Het deel venster artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatie strategieën
- [De strategie voor Rolling implementatie configureren](https://aka.ms/AA7jlh8)
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
