---
title: Zelf studie-distributies van de Canarische voor Azure Linux Virtual Machines configureren
description: In deze zelf studie leert u hoe u een CD-pijp lijn (continue implementatie) kunt instellen waarmee een groep virtuele machines van Azure wordt bijgewerkt met behulp van de Blue-groen implementatie strategie
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120490"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-een strategie voor blauw-groen-implementatie configureren voor Azure Linux Virtual Machines


## <a name="iaas---configure-cicd"></a>IaaS-CI/CD configureren 
Azure-pijp lijnen bieden een volledige, volledig uitgeruste set hulpprogram ma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt direct vanuit het Azure Portal een continue leverings pijplijn configureren voor een Azure-VM. Dit document bevat de stappen voor het instellen van een CI/CD-pijp lijn die gebruikmaakt van de blauw groene strategie voor implementaties met meerdere machines. U kunt ook een kijkje nemen in andere strategieën als [Rolling](https://aka.ms/AA7jlh8) en [Canarische](https://aka.ms/AA7jdrz), die out-of-box worden ondersteund vanuit Azure Portal. 

 
 **CI/CD op Virtual Machines configureren**

Virtuele machines kunnen worden toegevoegd als doelen voor een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden gericht op updates voor meerdere machines. Zodra de **implementatie geschiedenis** binnen een implementatie groep is geïmplementeerd, biedt de traceer baarheid van de virtuele machine naar de pijp lijn en vervolgens de door voer. 
 
  
**Blue-groen-implementaties**: een Blue-groen implementatie vermindert downtime door een identieke standby-omgeving te hebben. Op elk gewenst moment is een van de omgevingen Live. Tijdens de voor bereiding voor een nieuwe release voltooit u de laatste test fase in de groene omgeving. Zodra de software in de groene omgeving werkt, schakelt u het verkeer zo in dat alle inkomende aanvragen naar de groene omgeving gaan. de blauwe omgeving is nu niet-actief.
U kunt Blue-groene implementaties configureren voor uw**virtuele machines**van de Azure Portal met de optie continue levering. 

Hier volgt stapsgewijze instructies.

1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine 
2. Ga in het linkerdeel venster van de VM naar **continue levering**. Klik vervolgens op **configureren**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Klik in het configuratie scherm op **Azure DevOps-organisatie** om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld ' dev ', ' test ', ' bedoeld ' en ' productie '. U kunt een nieuwe implementatie groep maken of een bestaande implementatie groep selecteren. 
5. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Houd er rekening mee dat het gepubliceerde pakket een implementatie script moet hebben dat _Deploy. ps1_ of _Deploy.sh_ in de map in `deployscripts` de hoofdmap van het pakket bevat. Dit implementatie script wordt tijdens runtime uitgevoerd door de Azure DevOps-pijp lijn.
6. Selecteer de implementatie strategie van uw keuze. Selecteer **blauw-groen**.
7. Voeg een ' Blue ' of ' groen ' tag toe aan de Vm's die deel moeten uitmaken van Blue-groen implementaties. Als de virtuele machine een stand-by-functie heeft, moet u deze als ' groen ' markeren, anders als ' blauw ' markeren.
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Klik op **OK** om de doorlopende bezorgings pijplijn te configureren. U hebt nu een continue leverings pijplijn geconfigureerd om te implementeren op de virtuele machine.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Klik op release pijplijn **bewerken** in azure DevOps om de pijplijn configuratie weer te geven. De pijp lijn bestaat uit drie fasen. De eerste fase is een fase van de implementatie groep en wordt geïmplementeerd op Vm's die zijn gelabeld als _groen_ (stand-by-vm's). De tweede fase pauzeert de pijp lijn en wacht op hand matige interventie om de uitvoering te hervatten. Zodra een gebruiker heeft aangetoond dat de implementatie stabiel is, kan hij het verkeer omleiden naar _groene_ vm's en de pijplijn uitvoering hervatten, waardoor de _blauwe_ en _groene_ Tags in de vm's worden gewisseld. Dit zorgt ervoor dat de virtuele machines met een oudere toepassings versie worden gelabeld als _groen_ en worden geïmplementeerd in de volgende pijplijn uitvoering.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. De taak Deploy-script uitvoeren voert standaard het implementatie script _Deploy. ps1_ of _Deploy.sh_ uit in de `deployscripts` map in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build pijp lijn dit publiceert in de hoofdmap van het pakket.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Andere implementatie strategieën
- [Strategie voor Rolling implementatie configureren](https://aka.ms/AA7jlh8)
- [Strategie voor distributie op Canarische configureren](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga gemakkelijker dan ooit aan de slag met Azure.
 
Met DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service: Selecteer een toepassings taal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende bronnen 
- [Implementeren in azure Virtual Machines met behulp van DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
