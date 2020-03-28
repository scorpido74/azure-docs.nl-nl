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
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77912525"
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
Azure Pipelines biedt een complete, volledig uitgeruste set CI/CD-automatiseringstools voor implementaties naar virtuele machines. U een pijplijn voor continue levering voor een Azure VM rechtstreeks vanuit Azure-portal configureren. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijplijn voor implementaties met meerdere machines vanuit Azure-portal. CI/CD configureren op virtuele machines.

Virtuele machines kunnen worden toegevoegd als doelen aan een [implementatiegroep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden gericht op multi-machine rolling updates. Implementatiegeschiedenisweergaven binnen implementatiegroepen bieden traceerbaarheid van VM naar de pijplijn en vervolgens naar de commit. 
 
**Rolling updates**: Een rolling deployment vervangt exemplaren van de vorige versie van een toepassing door exemplaren van de nieuwe versie van de toepassing op een vaste set machines (rolling set) in elke iteratie. Laten we eens kijken hoe u een rolling update configureren voor virtuele machines.  
U rolling updates configureren voor uw**virtuele machines**binnen de Azure-portal met behulp van continue levering optie. 

Hier is de stap-voor-stap walkthrough. 
1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine. 
2. Navigeer in het linkerdeelvenster vm naar het menu **Continue bezorging.**  Klik vervolgens op **Configureren**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. Klik in het configuratiepaneel op 'Azure DevOps Organization' om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijplijn wilt configureren.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Een implementatiegroep is een logische set implementatiedoelmachines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld 'Dev', 'Test', 'UAT' en 'Productie'. U een nieuwe implementatiegroep maken of een bestaande implementatiegroep selecteren. U de machine optioneel taggen met de rol. Bijvoorbeeld 'web', 'db' etc.  
5. Klik op **OK** in het dialoogvenster om de pijplijn voor continue levering te configureren. 
6. Zodra u klaar bent, hebt u een continue leveringspijplijn geconfigureerd om te implementeren op de virtuele machine.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. U zult zien dat de implementatie naar de virtuele machine aan de gang is. U op de koppeling klikken om naar de pijplijn te navigeren. Klik op **Release-1** om de implementatie weer te geven. U ook op de **bewerking** klikken om de definitie van de releasepijplijn te wijzigen. 
8. Als u meerdere VM's moet configureren, herhaalt u de stappen 2-5, voordat andere VM's aan de implementatiegroep moeten worden toegevoegd. 
9. Als u klaar bent, klikt u op de pijplijndefinitie, navigeert u naar de Azure DevOps-organisatie en klikt u op **Releasepipeline bewerken.** 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Klik op de link **1 taak, 1 taak** in **dev** fase. Klik op de **implementatiefase.**  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. In het configuratiedeelvenster aan de rechterkant u zien dat de pijplijn standaard is geconfigureerd om een rolling-update uit te werken naar alle doelen parallel. U de implementaties één voor één of in procenten configureren met behulp van de schuifregelaar.  
  
  
**Canary** vermindert het risico door de wijziging langzaam uit te rollen naar een kleine subset van gebruikers. Naarmate u meer vertrouwen krijgt in de nieuwe versie, u beginnen met het vrijgeven van het naar meer servers in uw infrastructuur en routering meer gebruikers naar het. U kanarieimplementaties configureren naar uw**virtuele machines**met de Azure-portal met behulp van continue leveringsoptie. Hier is de stap-voor-stap walkthrough. 
1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-5 in de vorige sectie om meerdere VM's toe te voegen aan de implementatiegroep. 
3. Voeg een aangepaste tag toe aan de VM's die deel moeten uitmaken van kanarieimplementaties. Bijvoorbeeld "kanarie".
4. Zodra de pijplijn is geconfigureerd voor de VM's, klikt u op de pijplijn, start azure DevOps-organisatie, **bewerk de** pijplijn en navigeert u naar de **dev-fase.** Tag toevoegen aan het filter "kanarie". 
5. Voeg een andere implementatiegroepfase toe en configureer de fase met de tags om de resterende VM's in de implementatiegroep te targeten.  
6. Configureer optioneel een handmatige validatiestap die de kanarieimplementaties kan promoten/afwijzen. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-Green** vermindert de downtime van implementatie door identieke stand-byomgevingen te hebben. Op elk moment is een van de omgevingen live. Terwijl u zich voorbereidt op een nieuwe release, doet u uw laatste testfase in de groene omgeving. Zodra de software werkt in de groene omgeving, schakel het verkeer, zodat alle inkomende verzoeken naar de groene omgeving - de blauwe omgeving is nu niet actief.
U blauwgroene implementaties configureren op uw**virtuele machines**vanuit de Azure-portal met behulp van de optie continue levering. 

Hier is de stap-voor-stap walkthrough. 

1. Meld u aan bij uw Azure-portal en navigeer naar een virtuele machine 
2. Volg de stappen 2-5 onder de sectie **Updates rollen** om meerdere VM's toe te voegen aan de implementatiegroep. Voeg een aangepaste tag toe aan de VM's die deel moeten uitmaken van blauwgroene implementaties. Bijvoorbeeld 'blauw' of 'groen' voor de VM's die voor de stand-byrol zijn. 
3. Zodra de pijplijn is geconfigureerd voor de VM's, klikt u op de pijplijn, start Azure DevOps-organisatie, **Bewerk** de pijplijn, navigeer t u naar de **dev-fase.** Tag toevoegen aan het filter "groen". 
4. Voeg een agentloze fase toe, configureer de fase met handmatige validatiestap en een api-stap invoke-REST om de tags te verwisselen. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga eenvoudiger dan ooit aan de slag met Azure.
 
Met DevOps Projects start u met het uitvoeren van uw toepassing op elke Azure-service in slechts drie stappen: selecteer een toepassingstaal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende bronnen 
- [Implementeren op Azure Virtual Machines met Het DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
