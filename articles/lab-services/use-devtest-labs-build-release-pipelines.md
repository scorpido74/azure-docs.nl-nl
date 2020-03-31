---
title: DevTest Labs in Azure Pipelines gebruiken om pijplijnen te ontwikkelen en uit te brengen
description: Meer informatie over het gebruik van Azure DevTest Labs in Azure Pipelines build- en releasepipelines.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169227"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>DevTest Labs in Azure Pipelines gebruiken om pijplijnen te ontwikkelen en uit te brengen
In dit artikel vindt u informatie over hoe DevTest Labs kan worden gebruikt in azure pipelines voor het maken en vrijgeven van pijplijnen. 

## <a name="overall-flow"></a>Totale stroom
De basisstroom is het hebben van een **buildpijplijn** die de volgende taken uitvoert:

1. Bouw de toepassingscode.
1. Maak de basisomgeving in DevTest Labs.
1. Werk de omgeving bij met aangepaste informatie.
1. De toepassing implementeren in de DevTest Labs-omgeving
1. Test de code. 

Zodra de build is voltooid, gebruikt de **releasepijplijn** de buildartefacten om fasering of productie te implementeren. 

Een van de noodzakelijke uitgangspunten is dat alle informatie die nodig is om het geteste ecosysteem opnieuw te maken beschikbaar is binnen de buildartefacten, inclusief de configuratie van de Azure-bronnen. Omdat Azure-resources kosten maken wanneer ze worden gebruikt, willen bedrijven het gebruik van deze resources beheren of bijhouden. In sommige situaties kunnen Azure Resource Manager-sjablonen die worden gebruikt om de resources te maken en configureren, worden beheerd door een andere afdeling zoals IT. En deze sjablonen kunnen worden opgeslagen in een andere opslagplaats. Het leidt tot een interessante situatie waarin een build wordt gemaakt en getest, en zowel de code als de configuratie moeten worden opgeslagen in de buildartefacten om het systeem in productie correct opnieuw te maken. 

Met Behulp van DevTest Labs tijdens de build/testfase u Azure Resource Manager-sjablonen en ondersteunende bestanden toevoegen aan de buildbronnen, zodat tijdens de releasefase de exacte configuratie wordt geïmplementeerd waarmee wordt getest, wordt geïmplementeerd in productie. Met de taak **Azure DevTest Labs-omgeving maken** met de juiste configuratie worden de Resource Manager-sjablonen in de buildartefacten opgeslagen. In dit voorbeeld gebruikt u de code uit de [zelfstudie: een .NET Core- en SQL Database-webapp in Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)om de web-app in Azure te implementeren en te testen.

![Totale stroom](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure-resources instellen
Er zijn een paar items die vooraf moeten worden gemaakt:

- Twee opslagplaatsen. De eerste met de code uit de zelfstudie en een Resource Manager-sjabloon met twee extra VM's. De tweede bevat de sjabloon Azure Resource Manager (bestaande configuratie) basis.
- Een resourcegroep voor de implementatie van de productiecode en -configuratie.
- Er moet een lab worden ingesteld met een [verbinding met de configuratieopslagplaats](devtest-lab-create-environment-from-arm.md) voor de buildpijplijn. De sjabloon Resourcemanager moet worden ingecheckt in de configuratierepository als azuredeploy.json met de metadata.json, zodat DevTest Labs de sjabloon kunnen herkennen en implementeren.

De buildpipeline maakt een DevTest Labs-omgeving en implementeert de code voor het testen.

## <a name="set-up-a-build-pipeline"></a>Een buildpijplijn instellen
Maak in Azure Pipelines een buildpijplijn met de code uit de [zelfstudie: een .NET Core- en SQL Database-webapp maken in Azure App Service.](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md) Gebruik de **sjabloon ASP.NET Core,** die de benodigde taak vult om de code te bouwen, te testen en te publiceren.

![De ASP.NET sjabloon selecteren](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

U moet drie extra taken toevoegen om de omgeving in DevTest Labs te maken en te implementeren in de omgeving.

![Pijplijn met drie taken](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Omgevingstaak maken
Gebruik de vervolgkeuzelijsten om de volgende waarden te selecteren in de taak omgeving maken van de omgeving **(Azure DevTest Labs Create Create Environment)** de vervolgkeuzelijsten gebruiken om de volgende waarden te selecteren:

- Azure-abonnement
- naam van het lab
- naam van de opslagplaats
- naam van de sjabloon (die de map toont waar de omgeving is opgeslagen). 

We raden u aan vervolgkeuzelijsten op de pagina te gebruiken in plaats van de informatie handmatig in te voeren. Als u de gegevens handmatig invoert, voert u volledig gekwalificeerde Azure Resource Id's in. De taak geeft de vriendelijke namen weer in plaats van resource-id's. 

De omgevingsnaam is de weergegeven naam die wordt weergegeven in DevTest Labs. Het moet een unieke naam voor elke build. Bijvoorbeeld: **TestEnv$(Build.BuildId)**. 

U parametersbestand of parameters opgeven om informatie door te geven aan de sjabloon Resourcebeheer. 

Selecteer de **uitvoervariabelen maken op basis van de** uitvoeroptie omgevingssjabloon en voer een referentienaam in. Voer in dit voorbeeld **BaseEnv** in voor de referentienaam. U gebruikt deze BaseEnv bij het configureren van de volgende taak. 

![Azure DevTest Labs-omgevingtaak maken](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Omgevingstaak invullen
De tweede taak **(Azure DevTest Labs Populate Environment** taak) is het bijwerken van de bestaande DevTest Labs-omgeving. De taak voor omgevingmaken uitvoert **BaseEnv.environmentResourceId** uit die wordt gebruikt om de omgevingsnaam voor deze taak te configureren. De sjabloon Resourcemanager voor dit voorbeeld heeft twee parameters : **adminUserName** en **adminPassword**. 

![Azure DevTest Labs-omgeving taak invullen](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Implementatietaak app-service
De derde taak is de **Azure App Service Deploy-taak.** Het app-type is ingesteld op **Web App** en de naam van de App-service is ingesteld op **$(WebSite).**

![Taak App-service implementeren](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Releasepijplijn instellen
U maakt een releasepijplijn met twee taken: **Azure Deployment: Create or Update Resource Group** en Deploy Azure App **Service**. 

Geef voor de eerste taak de naam en locatie van de resourcegroep op. De sjabloonlocatie is een gekoppeld artefact. Als de sjabloon Resourcebeheer gekoppelde sjablonen bevat, moet een aangepaste implementatie van resourcegroepen worden geïmplementeerd. De sjabloon bevindt zich in het gepubliceerde dropartefact. Sjabloonparameters overschrijven voor de sjabloon Resourcebeheer. U de resterende instellingen met standaardwaarden laten. 

Voor de tweede taak **Deploy Azure App Service**geeft u het Azure-abonnement op, selecteert u Web **App** voor het **app-type**en **$(WebSite)** voor de **naam appservice**. U de resterende instellingen met standaardwaarden laten. 

## <a name="test-run"></a>Testrun
Nu beide pijplijnen zijn ingesteld, handmatig wachtrij een build en zie het werken. De volgende stap is om de juiste trigger voor de build in te stellen en de build aan te sluiten op de releasepijplijn.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Azure DevTest Labs integreren in uw Azure Pipelines-pijplijn en continue integratie- en leveringspijplijn](devtest-lab-integrate-ci-cd-vsts.md)
- [Omgevingen integreren in uw CI/CD-pijplijnen voor Azure Pipelines](integrate-environments-devops-pipeline.md)
