---
title: Zelf studie-Rolling implementaties voor Azure Linux Virtual Machines configureren
description: In deze zelf studie leert u hoe u een CD-pijp lijn (continue implementatie) kunt instellen die een groep Azure-Linux Virtual Machines incrementeel bijwerkt met behulp van de implementatie strategie voor rollen
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
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113477"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-een strategie voor Rolling implementatie configureren voor Azure Linux Virtual Machines

Azure DevOps is een ingebouwde Azure-service die elk deel van het DevOps-proces automatiseert met continue integratie en continue levering voor elke Azure-resource.
Of uw app gebruikmaakt van virtuele machines, Web-apps, Kubernetes of een andere resource, u kunt de infra structuur implementeren als code, continue integratie, continue tests, continue levering en continue bewaking met Azure en Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS-CI/CD configureren 
Azure-pijp lijnen bieden een volledige, volledig uitgeruste set hulpprogram ma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt direct vanuit het Azure Portal een continue leverings pijplijn configureren voor een Azure-VM. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijp lijn voor implementaties met meerdere machines van Azure Portal. U kunt ook een kijkje nemen op andere strategieën, zoals [Canarische](https://aka.ms/AA7jdrz) en [Blue-groen](https://aka.ms/AA83fwu), die out-of-box worden ondersteund vanuit Azure Portal. 


**CI/CD op Virtual Machines configureren**

Virtuele machines kunnen worden toegevoegd als doelen aan een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden gericht op het bijwerken van meerdere machines. Zodra de **implementatie geschiedenis** binnen een implementatie groep is geïmplementeerd, biedt de-configuratie een traceer baarheid van de VM naar de pijp lijn en vervolgens de door voer. 
 

**Rolling implementaties**: bij een rolling implementatie worden exemplaren van de vorige versie van een toepassing vervangen door exemplaren van de nieuwe versie van de toepassing op een vaste set machines (Rolling set) in elke iteratie. Laten we eens kijken hoe u een rolling update naar virtuele machines kunt configureren.  
U kunt Rolling updates configureren voor uw**virtuele machines**in de Azure Portal met behulp van de optie continue levering. 

Hier volgt stapsgewijze instructies. 
1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine. 
2. Ga in het linkerdeel venster van de VM naar het menu **doorlopende levering** . Klik vervolgens op **configureren**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Klik in het deel venster configuratie op ' Azure DevOps-organisatie ' om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld ' dev ', ' test ', ' bedoeld ' en ' productie '. U kunt een nieuwe implementatie groep maken of een bestaande implementatie groep selecteren. 
5. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Houd er rekening mee dat het gepubliceerde pakket een implementatie script moet hebben voor _Deploy. ps1_ of _Deploy.sh_ in de `deployscripts` map van package root. Dit implementatie script wordt tijdens runtime uitgevoerd door de Azure DevOps-pijp lijn.
6. Selecteer de implementatie strategie van uw keuze. In dit geval kunt u ' Rolling ' selecteren.
7. Desgewenst kunt u de machine met de rol labelen. Bijvoorbeeld ' Web ', ' db ' enzovoort. Dit helpt u Vm's te richten die alleen specifieke rollen hebben.
8. Klik op **OK** in het dialoog venster om de doorlopende bezorgings pijplijn te configureren. 
9. Als u klaar bent, beschikt u over een continue leverings pijplijn die is geconfigureerd voor implementatie op de virtuele machine.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. U ziet dat de implementatie van de virtuele machine wordt uitgevoerd. U kunt klikken op de koppeling om naar de pijp lijn te gaan. Klik op **Release-1** om de implementatie weer te geven. U kunt ook op de **bewerken** klikken om de release pijplijn definitie te wijzigen. 
11. Als u meerdere Vm's wilt configureren, herhaalt u de stappen 2-4 voor andere virtuele machines die aan de implementatie groep moeten worden toegevoegd. Houd er rekening mee dat als u een implementatie groep selecteert waarvoor al een pijp lijn wordt uitgevoerd, de VM wordt toegevoegd aan de implementatie groep zonder nieuwe pijp lijnen te maken. 
12. Als u klaar bent, klikt u op de pijplijn definitie, gaat u naar de Azure DevOps-organisatie en klikt u op release pijplijn **bewerken** . 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Klik op de taak koppeling **1, 1 taak** in **dev** -fase. Klik op **implementatie** fase.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. In het deel venster configuratie aan de rechter kant kunt u het aantal machines opgeven dat u parallel wilt implementeren in elke iteratie. Als u op meerdere machines tegelijk wilt implementeren, kunt u deze met behulp van de schuif regelaar opgeven in termen van percentage.  

15. De taak Deploy-script uitvoeren voert standaard het implementatie script _Deploy. ps1_ of _Deploy.sh_ in de map ' deployscripts ' uit in de hoofdmap van het gepubliceerde pakket.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatie strategieën

- [Strategie voor distributie op Canarische configureren](https://aka.ms/AA7jdrz)
- [Strategie voor de implementatie van blauw en groen configureren](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga gemakkelijker dan ooit aan de slag met Azure.
 
Met DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service: Selecteer een toepassings taal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende bronnen 
- [Implementeren in azure Virtual Machines met behulp van DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
