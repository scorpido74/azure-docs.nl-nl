---
title: CI/CD-pijp lijn met Azure DevOps Projects-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects zorgt ervoor dat u eenvoudig aan de slag kunt met Azure. U kunt in een paar snelle stappen een Azure IoT Edge-app van uw keuze starten.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c7598aafea85e91f28233645d3d71bf85bf45425
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82130979"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Een CI/CD-pijp lijn maken voor IoT Edge met Azure DevOps Projects

Configureer doorlopende integratie (CI) en continue levering (CD) voor uw IoT Edge-toepassing met DevOps Projects. Azure DevOps Projects vereenvoudigt de eerste configuratie van een build- en release-pijplijn in Azure Pipelines.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

DevOps Projects maakt een CI/CD-pijplijn in Azure DevOps. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure-Portal](https://portal.azure.com).

1. Selecteer in het linkerdeelvenster de optie **Een resource selecteren**, en zoek vervolgens naar **DevOps Projects**.  

1. Selecteer **Maken**.

## <a name="create-a-new-application-pipeline"></a>Een nieuwe toepassings pijplijn maken

1. Uw Azure IOT Edge module (s) kunnen worden geschreven in [C#](tutorial-csharp-module.md), [node. js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) en [Java](tutorial-java-module.md). Selecteer uw voorkeurs taal om een nieuwe toepassing te starten: **.net**, **node. js**, **python**, **C**of **Java**. Selecteer **Volgende** om door te gaan.

   ![Taal selecteren voor het maken van een nieuwe toepassing](./media/how-to-devops-project/select-language.png)

2. Selecteer **Simple IOT** als uw toepassings raamwerk en selecteer vervolgens **volgende**.

   ![Selecteer een eenvoudig IoT-Framework](media/how-to-devops-project/select-iot.png)

3. Selecteer **IOT Edge** als de Azure-service waarmee uw toepassing wordt geïmplementeerd en selecteer **volgende**.

   ![IoT Edge service selecteren](media/how-to-devops-project/select-iot-edge.png)

4. Maak gratis een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie.

   1. Geef een naam op voor uw project.

   2. Selecteer uw Azure DevOps-organisatie. Als u geen bestaande organisatie hebt, selecteert u **extra instellingen** om een nieuwe te maken.

   3. Selecteer uw Azure-abonnement.

   4. Gebruik de IoT Hub naam die is gegenereerd door de naam van uw project of geef uw eigen namen op.

   5. Accepteer de standaard locatie of kies een van de besluitingen.

   6. Selecteer **extra instellingen** om de Azure-resources te configureren die namens u DevOps projects gemaakt.

   7. Selecteer **gereed** om het project te maken.

   ![Naam en toepassing maken](media/how-to-devops-project/select-devops.png)

Na enkele minuten wordt het DevOps Projects-dashboard weergegeven in de Azure-portal. Selecteer de naam van uw project om de voortgang weer te geven. Mogelijk moet u de pagina vernieuwen. Een voor beeld-IoT Edge toepassing wordt ingesteld in een opslag plaats in uw Azure DevOps-organisatie, een build wordt uitgevoerd en uw toepassing wordt geïmplementeerd op het IoT Edge apparaat. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.

   ![Toepassing in Azure Portal weer geven](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

DevOps Projects een Git-opslag plaats voor uw project gemaakt in azure opslag plaatsen. In deze sectie bekijkt u de opslag plaats en maakt u code wijzigingen in uw toepassing.

1. Als u wilt navigeren naar de opslag plaats die voor uw project is gemaakt, selecteert u **opslag** plaatsen in het menu van het project dashboard.  

   ![In azure opslag plaatsen gegenereerde opslag plaats weer geven](./media/how-to-devops-project/view-repositories.png)

2. In de volgende stappen wordt uitgelegd hoe u de webbrowser gebruikt om code wijzigingen aan te brengen. Als u uw opslag plaats lokaal wilt klonen, selecteert u **klonen** in de rechter bovenhoek van het venster. Gebruik de meegeleverde URL om uw Git-opslag plaats te klonen in Visual Studio code of uw favoriete ontwikkel programma.

3. De opslag plaats bevat al code voor een module met de naam **FilterModule** op basis van de toepassings taal die u hebt gekozen tijdens het maken van het proces. Open het bestand **modules/FilterModule/module. json** .

   ![Open het bestand module. json in azure opslag plaatsen](./media/how-to-devops-project/open-module-json.png)

4. U ziet dat in dit bestand [Azure DevOps build-variabelen](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) worden gebruikt in de **versie** parameter. Deze configuratie zorgt ervoor dat er een nieuwe versie van de module wordt gemaakt telkens wanneer een nieuwe build wordt uitgevoerd.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige secties Azure DevOps Projects automatisch een volledige CI/CD-pijp lijn voor uw IoT Edge toepassing geconfigureerd. Nu kunt u de pijp lijn verkennen en aanpassen als dat nodig is. Gebruik de volgende stappen om vertrouwd te raken met de Azure DevOps-build-en release-pijp lijnen.

1. Als u de build-pijp lijnen in uw DevOps-project wilt weer geven, selecteert u **pijp lijnen bouwen** in het menu van het project dashboard. Met deze koppeling worden een browsertabblad en de Azure DevOps build-pijplijn van Azure DevOps voor het nieuwe project geopend.

   ![Build-pijp lijnen weer geven in azure-pijp lijnen](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecteer **bewerken**.

    ![Build-pijp lijn bewerken](media/how-to-devops-project/click-edit-button.png)

3. In het deel venster dat wordt geopend, kunt u de taken bekijken die optreden wanneer uw build-pijp lijn wordt uitgevoerd. De build-pijp lijn voert diverse taken uit, zoals het ophalen van bronnen uit de Git-opslag plaats, het bouwen van IoT Edge module-installatie kopieën, het pushen van IoT Edge modules naar een container register en het publiceren van uitvoer die worden gebruikt voor implementaties. Zie [Azure-pijp lijnen configureren voor continue integratie voor](how-to-ci-cd.md#configure-continuous-integration)meer informatie over Azure IOT Edge taken in azure DevOps.

4. Selecteer de koptekst van de **pijp lijn** boven aan de build-pijp lijn om de details van de pijp lijn te openen. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam.

   ![De pijplijn Details bewerken](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecteer **& wachtrij opslaan**en selecteer vervolgens **Opslaan**.

6. Selecteer **Triggers** in het menu Build pijplijn. In DevOps Projects is automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart.  U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

7. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

8. Selecteer **geschiedenis**. Het deel venster Historie bevat een audittrail van recente wijzigingen aan de build. Azure Pipelines houdt alle wijzigingen in de build-pipeline bij en biedt de mogelijkheid om versies te vergelijken.

9. Wanneer u klaar bent met het verkennen van de build-pijp lijn, gaat u naar de bijbehorende release pijplijn. Selecteer **releases** onder **pijp lijnen**en selecteer vervolgens **bewerken** om de details van de pijp lijn weer te geven.

    ![Release pijplijn weer geven](media/how-to-devops-project/release-pipeline.png)

10. Onder **Artefacten** selecteert u **Neerzetten**. De bron die dit artefact controleert, is de uitvoer van de build-pijp lijn die u in de vorige stappen hebt onderzocht.

11. Selecteer naast het pictogram voor **neerzetten** de **continue implementatie trigger** die eruitziet als een bliksem schicht. Deze release pijplijn heeft de trigger ingeschakeld, waarmee een implementatie wordt uitgevoerd telkens wanneer er een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.  

12. Selecteer in het menu voor uw release pijplijn de optie **taken** en kies vervolgens de **ontwikkel** fase in de vervolg keuzelijst. DevOps Projects een release fase hebt gemaakt voor het maken van een IoT-hub, maakt een IoT Edge apparaat in die hub, implementeert de voorbeeld module van de build-pijp lijn en richt een virtuele machine in die moet worden uitgevoerd als uw IoT Edge-apparaat. Zie [Azure-pijp lijnen configureren voor continue implementatie](how-to-ci-cd.md#configure-continuous-deployment)voor meer informatie over Azure IOT Edge taken voor de cd.

    ![Taken voor continue implementatie weer geven](media/how-to-devops-project/dev-release.png)

13. Selecteer aan de rechterkant **Releases weergeven**. In deze weergave wordt een versiesgeschiedenis weergegeven.

14. Selecteer de naam van een release om meer informatie hierover weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de taken voor Azure IoT Edge op Azure DevOps in [continue integratie en continue implementatie naar Azure IOT Edge](how-to-ci-cd.md)
* Inzicht in de implementatie van IoT Edge in [IOT Edge implementaties voor één of op een bepaalde schaal](module-deployment-monitoring.md)
* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
