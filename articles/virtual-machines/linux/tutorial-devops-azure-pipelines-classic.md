---
title: Zelf studie-geïntegreerde DevOps voor IaaS en PaaS op Azure
description: In deze zelf studie leert u hoe u doorlopende integratie (CI) en doorlopende implementatie (CD) van een app naar Azure-Vm's kunt instellen met behulp van Azure-pijp lijnen.
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
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912525"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Zelf studie: geïntegreerde DevOps voor IaaS en PaaS op Azure

Met end-to-end-oplossingen in azure kunnen teams DevOps-procedures implementeren in elk van de fasen van de levens cyclus van toepassingen: plannen, ontwikkelen, leveren en werken. 

Hieronder vindt u enkele van de Azure-Services die de Cloud werkbelastingen vereenvoudigen en kunnen worden gecombineerd om verbluffend krachtige scenario's mogelijk te maken.
Deze technologieën, in combi natie met mensen en processen, zorgen ervoor dat teams voortdurend waarde aan klanten kunnen leveren. 

- Azure: https://portal.azure.com-portal voor het bouwen van Cloud werkbelastingen. Alles beheren en bewaken van eenvoudige web-apps tot complexe Cloud toepassingen 
- Azure DevOps: https://dev.azure.com – slimmer plannen, beter samen werken en sneller verzenden met een set moderne ontwikkel services 
- Azure Machine Learning studio: https://ml.azure.com-prep-gegevens, trainen en implementeren van machine learning modellen 
 

Azure DevOps is een ingebouwde Azure-service die elk deel van het DevOps-proces automatiseert met continue integratie en continue levering voor elke Azure-resource.
Of uw app gebruikmaakt van virtuele machines, Web-apps, Kubernetes of een andere resource, u kunt implementeren, infra structuur als code, continue integratie, continue testen, continue levering en doorlopende bewaking met Azure en Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS-CI/CD configureren 
Azure-pijp lijnen bieden een volledige, volledig uitgeruste set hulpprogram ma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt direct vanuit Azure Portal een continue leverings pijplijn configureren voor een Azure-VM. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijp lijn voor implementaties met meerdere machines van Azure Portal. CI/CD op Virtual Machines configureren.

Virtuele machines kunnen worden toegevoegd als doelen aan een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden gericht op het uitvoeren van updates voor meerdere machines. Implementatie geschiedenis weergaven in implementatie groepen bieden traceer baarheid van de VM naar de pijp lijn en vervolgens naar de door voer. 
 
**Rolling updates**: bij een rolling implementatie worden exemplaren van de vorige versie van een toepassing vervangen door exemplaren van de nieuwe versie van de toepassing op een vaste set machines (Rolling set) in elke iteratie. Laten we eens kijken hoe u een rolling update naar virtuele machines kunt configureren.  
U kunt Rolling updates configureren voor uw**virtuele machines**in de Azure Portal met behulp van de optie continue levering. 

Hier volgt stapsgewijze instructies. 
1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine. 
2. Ga in het linkerdeel venster van de VM naar het menu **continue levering** . Klik vervolgens op **configureren**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. Klik in het deel venster configuratie op ' Azure DevOps-organisatie ' om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld ' dev ', ' test ', ' bedoeld ' en ' productie '. U kunt een nieuwe implementatie groep maken of een bestaande implementatie groep selecteren. U kunt eventueel de machine met de rol labelen. Bijvoorbeeld ' Web ', ' db ' enzovoort.  
5. Klik op **OK** in het dialoog venster om de doorlopende bezorgings pijplijn te configureren. 
6. Als u klaar bent, beschikt u over een continue leverings pijplijn die is geconfigureerd voor implementatie op de virtuele machine.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. U ziet dat de implementatie van de virtuele machine wordt uitgevoerd. U kunt klikken op de koppeling om naar de pijp lijn te gaan. Klik op **Release-1** om de implementatie weer te geven. U kunt ook op de **bewerken** klikken om de release pijplijn definitie te wijzigen. 
8. Als u meerdere Vm's wilt configureren, herhaalt u de stappen 2-5 om andere Vm's toe te voegen aan de implementatie groep. 
9. Als u klaar bent, klikt u op de pijplijn definitie, gaat u naar de Azure DevOps-organisatie en klikt u op release pijplijn **bewerken** . 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klik op de taak koppeling **1, 1 taak** in **dev** -fase. Klik op **implementatie** fase.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. In het deel venster configuratie aan de rechter kant ziet u dat de pijp lijn standaard is geconfigureerd om een rolling update uit te voeren voor alle doelen parallel. U kunt de implementaties met behulp van de schuif regelaar zo configureren dat ze op één of een percentage worden uitgevoerd.  
  
  
De **Canarische** vermindert het risico door de wijziging in een kleine subset van gebruikers te verkorten. Wanneer u de nieuwe versie meer betrouwt, kunt u deze vrijgeven aan meer servers in uw infra structuur en meer gebruikers ernaar sturen. U kunt distributies op uw**virtuele machines**met de Azure Portal met behulp van de optie continue levering configureren. Hier volgt stapsgewijze instructies. 
1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-5 in de vorige sectie om meerdere Vm's toe te voegen aan de implementatie groep. 
3. Voeg een aangepaste tag toe aan de Vm's die deel moeten uitmaken van de distributie van de Canarische. Bijvoorbeeld ' Canarische '.
4. Zodra de pijp lijn is geconfigureerd voor de virtuele machines, klikt u op de pijp lijn, start u Azure DevOps-organisatie, **bewerkt** u de pijp lijn en navigeert u naar de **ontwikkelings** fase. Tag toevoegen aan het filter "Canarische". 
5. Voeg een andere fase van de implementatie groep toe, Configureer de fase met de labels om de resterende Vm's in de implementatie groep te richten.  
6. U kunt eventueel een hand matige validatie stap configureren die de Canarische implementaties kan verhogen of afwijzen. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blauw-groen** vermindert de implementatie downtime door een identieke stand-by omgeving te hebben. Op elk gewenst moment is een van de omgevingen Live. Tijdens de voor bereiding op een nieuwe release voert u uw laatste test fase uit in de groene omgeving. Zodra de software in de groene omgeving werkt, schakelt u het verkeer zo in dat alle inkomende aanvragen naar de groene omgeving gaan. de blauwe omgeving is nu niet-actief.
U kunt Blue-groene implementaties configureren voor uw**virtuele machines**van de Azure Portal met de optie continue levering. 

Hier volgt stapsgewijze instructies. 

1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-5 onder de sectie **Rolling updates** om meerdere vm's toe te voegen aan de implementatie groep. Voeg een aangepaste tag toe aan de Vm's die deel moeten uitmaken van Blue-groen implementaties. Bijvoorbeeld: "Blue" of "groen" voor de virtuele machines die voor de functie standaard zijn. 
3. Zodra de pijp lijn is geconfigureerd voor de virtuele machines, klikt u op de pijp lijn, start u Azure DevOps-organisatie, **bewerkt** u de pijp lijn, gaat u naar de **ontwikkelings** fase. Voeg tag toe aan het filter "groen". 
4. Voeg een agent-less-fase toe, Configureer de fase met hand matige validerings stap en een aanroep-REST-API-stap om de tags te wisselen. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga gemakkelijker dan ooit aan de slag met Azure.
 
Met DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service: Selecteer een toepassings taal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende resources 
- [Implementeren in azure Virtual Machines met behulp van DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
