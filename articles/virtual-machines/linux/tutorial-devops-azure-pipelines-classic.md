---
title: Zelfstudie - Geïntegreerde ontwikkelaars voor IaaS en PaaS op Azure
description: In deze zelfstudie leert u hoe u permanente integratie (CI) en continue implementatie (CD) van een app inStelt op Azure VM's met Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885861"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Zelfstudie: Geïntegreerde ontwikkelaars voor IaaS en PaaS op Azure

Met end-to-end oplossingen op Azure kunnen teams DevOps-praktijken implementeren in elk van de fasen van de levenscyclus van toepassingen: plannen, ontwikkelen, leveren en werken. 

Hieronder vindt u enkele van de Azure Services die cloudworkloads vereenvoudigen en kunnen worden gecombineerd om verbazingwekkend krachtige scenario's mogelijk te maken.
Deze technologieën, gecombineerd met mensen en processen, stellen teams in staat om voortdurend waarde te bieden aan klanten. 

- Azure: https://portal.azure.com – Portal voor het bouwen van cloudworkloads. Alles beheren en bewaken, van eenvoudige webapps tot complexe cloudtoepassingen 
- Azure DevOps: https://dev.azure.com – Plan slimmer, werk beter samen en verzend sneller met een reeks moderne dev-services 
- Azure Machine Learning-studio: https://ml.azure.com - Gegevens voorbereiden, machine learning-modellen trainen en implementeren 
 

Azure DevOps is een ingebouwde Azure-service die elk onderdeel van het DevOps-proces automatiseert met continue integratie en continue levering voor elke Azure-bron.
Of uw app nu gebruik maakt van virtuele machines, webapps, Kubernetes of een andere bron, u infrastructuur implementeren als code, continue integratie, continue testen, continue levering en continue bewaking met Azure en Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - CI/CD configureren 
Azure Pipelines biedt een complete, volledig uitgeruste set CI/CD-automatiseringstools voor implementaties naar virtuele machines. U een pijplijn voor continue levering voor een Azure VM rechtstreeks vanuit Azure-portal configureren. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijplijn voor implementaties met meerdere machines vanuit Azure-portal. 


**CI/CD configureren op virtuele machines**

Virtuele machines kunnen worden toegevoegd als doelen aan een [implementatiegroep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden getarget voor multi-machine-update. Op basis van uw vereisten u een van de kant-en-klare implementatiestrategieën kiezen, _Rolling,_ _Canary_, _Blue-Green_ of deze verder aanpassen. Eenmaal geïmplementeerd, bieden implementatiegeschiedenisweergaven binnen implementatiegroepen traceerbaarheid van VM naar de pijplijn en vervolgens naar de commit. 
 
**Rolling Deployments**: Een rolling deployment vervangt exemplaren van de vorige versie van een toepassing door exemplaren van de nieuwe versie van de toepassing op een vaste set machines (rolling set) in elke iteratie. Laten we eens kijken hoe u een rolling update configureren voor virtuele machines.  
U rolling updates configureren voor uw**virtuele machines**binnen de Azure-portal met behulp van continue levering optie. 

Hier is de stap-voor-stap walkthrough. 
1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine. 
2. Navigeer in het linkerdeelvenster vm naar het menu **Continue bezorging.**  Klik vervolgens op **Configureren**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Klik in het configuratiepaneel op 'Azure DevOps Organization' om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijplijn wilt configureren.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Een implementatiegroep is een logische set implementatiedoelmachines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld 'Dev', 'Test', 'UAT' en 'Productie'. U een nieuwe implementatiegroep maken of een bestaande implementatiegroep selecteren. 
5. Selecteer de buildpijplijn die het pakket publiceert dat moet worden geïmplementeerd op de virtuele machine. Houd er rekening mee dat het gepubliceerde pakket een implementatiescript _deploy.ps1_ of _deploy.sh_ in _de map deployscripts_ bij pakketroot moet hebben. Dit implementatiescript wordt uitgevoerd door azure devOps-pijplijn op uitvoering.
6. Selecteer de implementatiestrategie van uw keuze. In dit geval u 'Rolling' selecteren.
7. Optioneel u de machine taggen met de rol. Bijvoorbeeld 'web', 'db' etc. Zo u VM's targeten die alleen een specifieke rol hebben.
8. Klik op **OK** in het dialoogvenster om de pijplijn voor continue levering te configureren. 
9. Zodra u klaar bent, hebt u een continue leveringspijplijn geconfigureerd om te implementeren op de virtuele machine.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. U zult zien dat de implementatie naar de virtuele machine aan de gang is. U op de koppeling klikken om naar de pijplijn te navigeren. Klik op **Release-1** om de implementatie weer te geven. U ook op de **bewerking** klikken om de definitie van de releasepijplijn te wijzigen. 
11. Als u meerdere VM's moet configureren, herhaalt u de stappen 2-4 voor andere VM's die aan de implementatiegroep moeten worden toegevoegd. Houd er rekening mee dat als u een implementatiegroep selecteert waarvoor al een pijplijnrun bestaat, de VM alleen aan de implementatiegroep wordt toegevoegd zonder nieuwe pijplijnen te maken. 
12. Als u klaar bent, klikt u op de pijplijndefinitie, navigeert u naar de Azure DevOps-organisatie en klikt u op **Releasepipeline bewerken.** 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Klik op de link **1 taak, 1 taak** in **dev** fase. Klik op de **implementatiefase.**
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. In het configuratiedeelvenster aan de rechterkant u het aantal machines opgeven dat u parallel in elke iteratie wilt implementeren. Als u naar meerdere machines tegelijk wilt implementeren, u dit in termen van percentage opgeven met behulp van de schuifregelaar.  

15. Met de taak Deploy Script uitvoeren wordt standaard het implementatiescript _deploy.ps1_ uitgevoerd of _wordt deploy.sh_ in de map _deployscripts_ uitgevoerd in de hoofdmap van het gepubliceerde pakket.
  
**Canary Deployments**: Een kanarieimplementatie vermindert het risico door de wijziging langzaam uit te rollen naar een kleine subset van gebruikers. Naarmate u meer vertrouwen krijgt in de nieuwe versie, u beginnen met het vrijgeven van het naar meer servers in uw infrastructuur en routering meer gebruikers naar het. U kanarieimplementaties configureren naar uw**virtuele machines**met de Azure-portal met behulp van continue leveringsoptie. Hier is de stap-voor-stap walkthrough. 
1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-7 onder de sectie **Rolling Deployments** om meerdere VM's toe te voegen aan de implementatiegroep. Selecteer 'Canary' voor de vervolgkeuzelijst voor implementatiestrategie.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Voeg een 'kanarie'-tag toe aan de VM's die deel moeten uitmaken van kanarieimplementaties en een 'prik'-tag aan de VM's die deel uitmaken van implementaties na de implementatie van canary in geslaagd.
4. Zodra u klaar bent, hebt u een continue leveringspijplijn geconfigureerd om te implementeren op de virtuele machine.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Net als in de sectie **Rolling Deployments** u op **Releasepipeline bewerken** in Azure DevOps klikken om de pijplijnconfiguratie te bekijken. De pijplijn bestaat uit 3 fasen - de eerste fase is een DG fase en wordt geïmplementeerd in VM's die zijn gelabeld als _kanarie_. De tweede fase, pauzeert de pijplijn en wacht op handmatige interventie om de run te hervatten. Zodra een gebruiker ervan overtuigd is dat de implementatie van de kanarie stabiel is, kan hij de pijplijnrun hervatten die vervolgens de derde fase uitvoert die wordt geïmplementeerd in VM's die zijn getagd als _prik._ ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Blauw-groene implementaties:** een blauwgroene implementatie vermindert downtime door een identieke stand-byomgeving te hebben. Op elk moment is een van de omgevingen live. Terwijl u zich voorbereidt op een nieuwe release, doet u uw laatste testfase in de groene omgeving. Zodra de software werkt in de groene omgeving, schakel het verkeer, zodat alle inkomende verzoeken naar de groene omgeving - de blauwe omgeving is nu niet actief.
U blauwgroene implementaties configureren op uw**virtuele machines**vanuit de Azure-portal met behulp van de optie continue levering. 

Hier is de stap-voor-stap walkthrough.

1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-7 onder de sectie **Rolling Deployments** om meerdere VM's toe te voegen aan de implementatiegroep. Voeg een tag 'blauw' of 'groen' toe aan de VM's die deel gaan uitmaken van blauwgroene implementaties. Als de VM is voor een stand-by rol, dan moet je tag het als "groen".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Zodra u klaar bent, hebt u een continue leveringspijplijn geconfigureerd om te implementeren op de virtuele machine.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Net als bij **Rolling Deployments**u op **Releasepipeline bewerken** in Azure DevOps klikken om de pijplijnconfiguratie te bekijken. De pijplijn bestaat uit 3 fasen - de eerste fase is een DG fase en wordt geïmplementeerd in VM's die zijn gelabeld als _groen_ (stand-by VM's). De tweede fase pauzeert de pijplijn en wacht op handmatige interventie om de uitvoering te hervatten. Zodra een gebruiker ervan overtuigd is dat de implementatie stabiel is, kan hij het verkeer nu omleiden naar _groene_ VM's en de pijplijnrun hervatten die vervolgens _blauwe_ en _groene_ tags in de VM's zal verwisselen. Dit zorgt ervoor dat de VM's met oudere toepassingsversie zijn gelabeld als _groen_ en worden geïmplementeerd in de volgende pijplijnrun.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Houd er rekening mee dat deze implementatiestrategie vereist dat er elk ten minste één VM als blauw en groen is gelabeld. Zorg ervoor dat voordat u de pijplijn hervat bij handmatige interventiestap, u ten minste één vm hebt die als _blauw_is getagd.





 
## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga eenvoudiger dan ooit aan de slag met Azure.
 
Met DevOps Projects start u met het uitvoeren van uw toepassing op elke Azure-service in slechts drie stappen: selecteer een toepassingstaal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende bronnen 
- [Implementeren op Azure Virtual Machines met Het DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
