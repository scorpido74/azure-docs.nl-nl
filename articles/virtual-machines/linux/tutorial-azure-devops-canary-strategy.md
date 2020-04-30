---
title: Zelf studie-distributies van de Canarische voor Azure Linux Virtual Machines configureren
description: In deze zelf studie leert u hoe u een CD-pijp lijn (continue implementatie) kunt instellen waarmee een groep van Azure Linux Virtual Machines wordt bijgewerkt met een strategie voor distributie op basis van een Canarische implementatie
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120476"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelf studie-een strategie voor distributie op het Linux Virtual Machines voor Azure configureren


## <a name="iaas---configure-cicd"></a>IaaS-CI/CD configureren 
Azure-pijp lijnen bieden een volledige, volledig uitgeruste set hulpprogram ma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt direct vanuit Azure Portal een continue leverings pijplijn configureren voor een Azure-VM. Dit document bevat de stappen die zijn gekoppeld aan het instellen van een CI/CD-pijp lijn die gebruikmaakt van de strategie voor distributie op meerdere machines. U kunt ook een kijkje nemen op andere strategieën zoals geassen en [blauw groen](https://aka.ms/AA83fwu), die out [-of-](https://aka.ms/AA7jlh8) box van Azure Portal worden ondersteund. 


**CI/CD op Virtual Machines configureren**

Virtuele machines kunnen worden toegevoegd als doelen voor een [implementatie groep](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) en kunnen worden gericht op updates voor meerdere machines. Zodra de **implementatie geschiedenis** binnen uw implementatie groep is geïmplementeerd, levert de bezorgings mogelijkheden van de VM naar de pijp lijn en vervolgens naar de door voer. 
 
  
**Canarische implementaties**: een distributie in de Canarische omgeving vermindert het risico door de wijziging in een kleine subset van gebruikers te verkorten. Wanneer u de nieuwe versie meer betrouwt, kunt u deze vrijgeven aan meer servers in uw infra structuur en meer gebruikers ernaar sturen. U kunt distributies op uw virtuele machines met de Azure Portal met behulp van de optie continue levering configureren. Hier volgt stapsgewijze instructies. 
1. Meld u aan bij uw Azure Portal en navigeer naar een virtuele machine 
2. Ga in het linkerdeel venster van de VM naar het menu **doorlopende levering** . Klik op **configureren**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Klik in het configuratie scherm op **Azure DevOps-organisatie** om een bestaand account te selecteren of er een te maken. Selecteer vervolgens het project waaronder u de pijp lijn wilt configureren.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Een implementatie groep is een logische set implementatie doel machines die de fysieke omgevingen vertegenwoordigen; bijvoorbeeld ' dev ', ' test ', ' bedoeld ' en ' productie '. U kunt een nieuwe implementatie groep maken of een bestaande implementatie groep selecteren. 
5. Selecteer de build-pijp lijn die het pakket publiceert dat naar de virtuele machine moet worden geïmplementeerd. Houd er rekening mee dat het gepubliceerde pakket een implementatie script moet _implementeren. ps1_ of _Deploy.sh_ in de `deployscripts` map op de hoofdmap van het pakket. Dit implementatie script wordt tijdens runtime uitgevoerd door de Azure DevOps-pijp lijn.
6. Selecteer de implementatie strategie van uw keuze. Selecteer **Canarische**.
7. Voeg een ' Canarische ' tag toe aan de virtuele machines die deel uitmaken van de Canarische implementaties en een Prod-tag naar de virtuele machines die deel uitmaken van implementaties nadat de voltooiing van de distributie is geslaagd. Met tags kunt u doel-Vm's richten die alleen specifieke rollen hebben.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Klik op **OK** in het dialoog venster om de doorlopende bezorgings pijplijn te configureren. U hebt nu een continue leverings pijplijn geconfigureerd om te implementeren op de virtuele machine.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Klik op release pijplijn **bewerken** in azure DevOps om de pijplijn configuratie weer te geven. De pijp lijn bestaat uit drie fasen. De eerste fase is een fase van de implementatie groep en wordt geïmplementeerd op Vm's die als _Canarische_zijn gelabeld. De tweede fase, pauzeert de pijp lijn en wacht op hand matige interventie om de uitvoering te hervatten. Zodra een gebruiker heeft nagegaan dat de distributie van de Canarische machine stabiel is, kan de uitvoering van de pijp lijn hervatten, waarna de derde fase wordt uitgevoerd die wordt geïmplementeerd op Vm's die zijn gelabeld als _Prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Voordat u de pijp lijn uitvoert, moet u ervoor zorgen dat ten minste één virtuele machine is gelabeld als _Prod_. In de derde fase van de pijp lijn wordt de toepassing alleen geïmplementeerd naar de virtuele machines die een _Prod_ -tag hebben.

11. De taak Deploy-script uitvoeren voert standaard het implementatie script _Deploy. ps1_ of _Deploy.sh_ in de map ' deployscripts ' uit in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build pijp lijn dit publiceert in de hoofdmap van het pakket. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Andere implementatie strategieën
- [Strategie voor Rolling implementatie configureren](https://aka.ms/AA7jlh8)
- [Strategie voor de implementatie van blauw en groen configureren](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps-project 
Ga gemakkelijker dan ooit aan de slag met Azure.
 
Met DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren op elke Azure-service: Selecteer een toepassings taal, een runtime en een Azure-service.
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Aanvullende bronnen 
- [Implementeren in azure Virtual Machines met behulp van DevOps-project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Continue implementatie van uw app implementeren in een Schaalset voor virtuele Azure-machines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
