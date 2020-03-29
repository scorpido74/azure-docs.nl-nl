---
title: CI/CD-pijplijn met Azure DevOps-projecten - Azure IoT Edge | Microsoft Documenten
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. Het helpt u in enkele snelle stappen een Azure IoT Edge-app naar keuze te starten.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510308"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Een CI/CD-pijplijn maken voor IoT Edge met Azure DevOps-projecten

Configureer continue integratie (CI) en continue levering (CD) voor uw IoT Edge-toepassing met DevOps Projects. Azure DevOps Projects vereenvoudigt de eerste configuratie van een build- en release-pijplijn in Azure Pipelines.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Projects maakt een CI/CD-pijplijn in Azure DevOps. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com).

1. Selecteer in het linkerdeelvenster de optie **Een resource selecteren**, en zoek vervolgens naar **DevOps Projects**.  

1. Selecteer **Maken**.

## <a name="create-a-new-application-pipeline"></a>Een nieuwe toepassingspijplijn maken

1. Uw Azure IoT Edge-module(s) kan worden geschreven in [C#](tutorial-csharp-module.md), [Node.js,](tutorial-node-module.md) [Python,](tutorial-python-module.md) [C](tutorial-c-module.md) en [Java](tutorial-java-module.md). Selecteer uw voorkeurstaal om een nieuwe toepassing te starten: **.NET**, **Node.js**, **Python,** **C**of **Java**. Selecteer **Volgende** om door te gaan.

   ![Taal selecteren om een nieuwe toepassing te maken](./media/how-to-devops-project/select-language.png)

2. Selecteer **Eenvoudig IoT** als toepassingskader en selecteer **Volgende**.

   ![Eenvoudig IoT-framework selecteren](media/how-to-devops-project/select-iot.png)

3. Selecteer **IoT Edge** als de Azure-service die uw toepassing implementeert en selecteer **Volgende**.

   ![IoT Edge-service selecteren](media/how-to-devops-project/select-iot-edge.png)

4. Maak gratis een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie.

   1. Geef een naam op voor uw project.

   2. Selecteer uw Azure DevOps-organisatie. Als u geen bestaande organisatie hebt, selecteert u **Aanvullende instellingen** om een nieuwe organisatie te maken.

   3. Selecteer uw Azure-abonnement.

   4. Gebruik de naam Van de IoT-hub die is gegenereerd door uw projectnaam of geef uw eigen naam op.

   5. Accepteer de standaardlocatie of kies er een dicht bij u in de buurt.

   6. Selecteer **Extra instellingen** om de Azure-resources te configureren die DevOps Projects namens u maakt.

   7. Selecteer **Gereed** om het maken van uw project af te ronden.

   ![Toepassing benoemen en maken](media/how-to-devops-project/select-devops.png)

Na enkele minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Selecteer uw projectnaam om de voortgang te zien. Mogelijk moet u de pagina vernieuwen. Een voorbeeld van IoT Edge-toepassing is ingesteld in een opslagplaats in uw Azure DevOps-organisatie, een build wordt uitgevoerd en uw toepassing wordt geïmplementeerd op het IoT Edge-apparaat. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.

   ![Toepassing weergeven in Azure-portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

DevOps Projects heeft een Git-repository gemaakt voor uw project in Azure Repos. In deze sectie bekijkt u de repository en brengt u codewijzigingen aan in uw toepassing.

1. Als u wilt navigeren naar de repo die voor uw project is gemaakt, selecteert u **Repositories** in het menu van uw projectdashboard.  

   ![Repository weergeven die is gegenereerd in Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. De volgende stappen doorlopen het gebruik van de webbrowser om codewijzigingen aan te brengen. Als u uw opslagplaats lokaal wilt klonen, selecteert u **Kloon** rechtsboven in het venster. Gebruik de opgegeven URL om je Git-repository te klonen in Visual Studio Code of je favoriete ontwikkeltool.

3. De repository bevat al code voor een module genaamd **FilterModule** op basis van de toepassingstaal die u hebt gekozen in het creatieproces. Open het **bestand modules/FilterModule/module.json.**

   ![Module.json-bestand openen in Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Dit bestand maakt gebruik van [Azure DevOps buildvariabelen](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) in de **parameter version.** Deze configuratie zorgt ervoor dat een nieuwe versie van de module wordt gemaakt elke keer dat een nieuwe build wordt uitgevoerd.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige secties configureerde Azure DevOps Projects automatisch een volledige CI/CD-pijplijn voor uw IoT Edge-toepassing. Verken en pas de pijplijn naar behoefte aan. Gebruik de volgende stappen om vertrouwd te raken met de Azure DevOps-build- en releasepijplijnen.

1. Als u de buildpijplijnen in uw Ontwikkelaarsproject wilt weergeven, selecteert u **Pijplijnen bouwen** in het menu van uw projectdashboard. Met deze koppeling worden een browsertabblad en de Azure DevOps build-pijplijn van Azure DevOps voor het nieuwe project geopend.

   ![Buildpipelines weergeven in Azure-pijplijnen](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecteer **Bewerken**.

    ![Buildpipeline bewerken](media/how-to-devops-project/click-edit-button.png)

3. In het deelvenster dat wordt geopend, u de taken onderzoeken die plaatsvinden wanneer uw pijplijn wordt uitgevoerd. De buildpijplijn voert verschillende taken uit, zoals het ophalen van bronnen uit de Git-opslagplaats, het bouwen van IoT Edge-moduleafbeeldingen, het pushen van IoT Edge-modules naar een containerregister en het publiceren van uitvoer die worden gebruikt voor implementaties. Zie [Azure Pipelines configureren voor continue integratie voor](how-to-ci-cd.md#configure-continuous-integration)meer informatie over Azure IoT Edge-taken in Azure DevOps.

4. Selecteer de **koptekst Pijplijn** boven aan de buildpijplijn om de pijplijndetails te openen. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam.

   ![De pijplijndetails bewerken](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecteer **& wachtrij opslaan**en selecteer **Opslaan**.

6. Selecteer **Triggers** in het menu pijplijn bouwen. In DevOps Projects is automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart.  U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

7. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

8. Selecteer **Geschiedenis**. Het geschiedenispaneel bevat een controlespoor van recente wijzigingen in de build. Azure Pipelines houdt alle wijzigingen in de build-pipeline bij en biedt de mogelijkheid om versies te vergelijken.

9. Wanneer u klaar bent met het verkennen van de buildpijplijn, navigeert u naar de bijbehorende releasepijplijn. Selecteer **Releases** onder **Pijplijnen**en selecteer **Bewerken** om de pijplijndetails weer te geven.

    ![Releasepijplijn weergeven](media/how-to-devops-project/release-pipeline.png)

10. Onder **Artefacten** selecteert u **Neerzetten**. De bron die dit artefact bekijkt is de uitvoer van de buildpijplijn die u in de vorige stappen hebt onderzocht.

11. Selecteer naast het pictogram **Drop** de **trigger voor continue implementatie** die eruitziet als een bliksemschicht. Deze release pijplijn heeft ingeschakeld de trigger, die een implementatie draait elke keer dat er een nieuwe build artefact beschikbaar. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.  

12. Selecteer **taken** in het menu voor uw releasepijplijn en kies vervolgens de **dev-fase** in de vervolgkeuzelijst. DevOps Projects heeft een releasefase voor u gemaakt die een IoT-hub maakt, een IoT Edge-apparaat in die hub maakt, de voorbeeldmodule uit de buildpijplijn implementeert en een virtuele machine instelt die als uw IoT Edge-apparaat wordt uitgevoerd. Zie [Azure Pipelines configureren voor continue implementatie voor](how-to-ci-cd.md#configure-continuous-deployment)meer informatie over Azure IoT Edge-taken voor cd.

    ![Continue implementatietaken weergeven](media/how-to-devops-project/dev-release.png)

13. Selecteer aan de rechterkant **Releases weergeven**. In deze weergave wordt een versiesgeschiedenis weergegeven.

14. Selecteer de naam van een release om meer informatie over een release weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de taken voor Azure IoT Edge op Azure DevOps in [continue integratie en continue implementatie naar Azure IoT Edge](how-to-ci-cd.md)
* Inzicht in de IoT Edge-implementatie in [IoT Edge-implementaties begrijpen voor afzonderlijke apparaten of op schaal](module-deployment-monitoring.md)
* Loop door de stappen om een implementatie te maken, bij te werken of te verwijderen in [IoT Edge-modules implementeren en bewaken op schaal.](how-to-deploy-monitor.md)
