---
title: Continue integratie & continue implementatie - Azure IoT Edge
description: Continue integratie en continue implementatie instellen - Azure IoT Edge met Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510971"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Continue integratie en continue implementatie naar Azure IoT Edge

U DevOps eenvoudig gebruiken met uw Azure IoT Edge-toepassingen met de ingebouwde Azure IoT Edge-taken in Azure Pipelines. In dit artikel wordt uitgelegd hoe u de functies voor continue integratie en continue implementatie van Azure Pipelines gebruiken om toepassingen snel en efficiënt te bouwen, te testen en te implementeren voor uw Azure IoT Edge.

![Diagram - CI- en CD-branches voor ontwikkeling en productie](./media/how-to-ci-cd/cd.png)

In dit artikel leert u hoe u de ingebouwde Azure IoT Edge-taken voor Azure Pipelines gebruiken om twee pijplijnen voor uw IoT Edge-oplossing te maken. Er zijn vier acties die kunnen worden gebruikt in de Azure IoT Edge-taken.

* **Azure IoT Edge - Build Module-afbeeldingen** nemen uw IoT Edge-oplossingscode en bouwt de containerafbeeldingen.
* **Azure IoT Edge - Push Module-afbeeldingen** pushen moduleafbeeldingen naar het containerregister dat u hebt opgegeven.
* **Azure IoT Edge - Genereer implementatiemanifest** neemt een deployment.template.json-bestand en de variabelen en genereert vervolgens het uiteindelijke IoT Edge-implementatiemanifestbestand.
* **Azure IoT Edge - Deploy to IoT Edge-apparaten** helpt bij het maken van IoT Edge-implementaties op één/meerdere IoT Edge-apparaten.

## <a name="prerequisites"></a>Vereisten

* Een Azure Repos-opslagplaats. Als u er geen hebt, u [een nieuwe Git repo maken in uw project.](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)
* Een IoT Edge-oplossing die is vastgelegd en naar uw opslagplaats is gepusht. Als u een nieuwe voorbeeldoplossing wilt maken voor het testen van dit artikel, volgt u de stappen in [Modules ontwikkelen en debuggen in Visual Studio Code](how-to-vs-code-develop-module.md) of Develop and [debug C# modules in Visual Studio.](how-to-visual-studio-develop-csharp-module.md)

   Voor dit artikel hebt u alleen de oplossingsmap nodig die is gemaakt door de IoT Edge-sjablonen in Visual Studio Code of Visual Studio. U hoeft deze code niet te bouwen, te pushen, te implementeren of te debuggen voordat u verdergaat. U stelt deze processen in Azure Pipelines in.

   Als u een nieuwe oplossing maakt, kloont u eerst uw repository lokaal. Wanneer u vervolgens de oplossing maakt, u ervoor kiezen deze rechtstreeks in de archiefmap te maken. U eenvoudig vastleggen en duwen de nieuwe bestanden vanaf daar.

* Een containerregister waar u moduleafbeeldingen pushen. U [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) of een derdenregister gebruiken.
* Een actieve [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) met ten minste IoT Edge-apparaten voor het testen van de afzonderlijke test- en productieimplementatiefasen. U de quickstart-artikelen volgen om een IoT Edge-apparaat op [Linux](quickstart-linux.md) of [Windows](quickstart.md) te maken

Zie Uw code delen met [Visual Studio en Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) voor meer informatie over het gebruik van Azure Repos.

## <a name="configure-continuous-integration"></a>Continue integratie configureren

In deze sectie maakt u een nieuwe buildpijplijn. Configureer de pijplijn automatisch wanneer u wijzigingen in de voorbeeldoplossing voor IoT Edge inschakelt en buildlogs publiceert.

>[!NOTE]
>In dit artikel wordt de visual designer van Azure DevOps gebruikt. Schakel de voorbeeldfunctie voor de nieuwe YAML-ervaring voor het maken van yaml-pijplijnen uit voordat u de stappen in deze sectie volgt.
>
>1. Selecteer in Azure DevOps het profielpictogram en selecteer **Voorbeeldfuncties**.
>2. **Nieuwe YAML-ervaring voor het maken van pijplijnen** uitschakelen.
>
>Zie [Een buildpijplijn maken](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)voor meer informatie.

1. Meld u aan bij uw Azure DevOps-organisatie (**\/https: /dev.azure.com/{your organization}/**) en open het project dat uw IoT Edge-oplossingsopslagplaats bevat.

   Voor dit artikel hebben we een repository gemaakt genaamd **IoTEdgeRepo.** Die repository bevat **IoTEdgeSolution** die de code heeft voor een module met de naam **filtermodule.**

   ![Open uw DevOps-project](./media/how-to-ci-cd/init-project.png)

2. Navigeer naar Azure Pipelines in uw project. Open het tabblad **Builds** en selecteer **Nieuwe pijplijn**. Als u al pijplijnen hebt gebouwd, selecteert u de knop **Nieuw.** Kies vervolgens **Nieuwe buildpijplijn**.

    ![Een nieuwe build-pipeline maken](./media/how-to-ci-cd/add-new-build.png)

3. Volg de aanwijzingen om uw pijplijn te maken.

   1. Geef de brongegevens op voor uw nieuwe buildpijplijn. Selecteer **Azure Repos Git** als bron en selecteer vervolgens het project, de opslagplaats en de vertakking waar uw IoT Edge-oplossingscode zich bevindt. Selecteer vervolgens **Doorgaan**.

      ![Selecteer de pijplijnbron](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecteer **Lege taak** in plaats van een sjabloon.

      ![Begin met een leeg proces](./media/how-to-ci-cd/start-with-empty.png)

4. Zodra uw pijplijn is gemaakt, wordt u naar de pijplijneditor geleid. Kies in de beschrijving van uw pijplijn de juiste agentgroep op basis van uw doelplatform:

   * Als u uw modules wilt bouwen in platform amd64 voor Linux-containers, kiest u **Hosted Ubuntu 1604**

   * Als u uw modules wilt bouwen in platform amd64 voor Windows 1809-containers, moet u [zelf gehoste agent instellen op Windows.](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)

   * Als u uw modules wilt bouwen in platform arm32v7 of arm64 voor Linux containers, moet u [zelf gehoste agent op Linux instellen.](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)

     ![Groep buildagent configureren](./media/how-to-ci-cd/configure-env.png)

5. Uw pijplijn wordt vooraf geconfigureerd met een taak genaamd **Agent-taak 1**. **+** Selecteer het plusteken ( ) om drie taken twee keer aan de taak toe te voegen: Azure **IoT Edge** twee maal, Bestanden één keer **kopiëren** en Artefacten één keer **publiceren.** (Plaats de plaats boven de naam van elke taak om de knop **Toevoegen** te bekijken.)

   ![Azure IoT Edge-taak toevoegen](./media/how-to-ci-cd/add-iot-edge-task.png)

   Wanneer alle vier de taken worden toegevoegd, ziet uw agenttaak eruit als het volgende voorbeeld:

   ![Drie taken in de buildpijplijn](./media/how-to-ci-cd/add-tasks.png)

6. Selecteer de eerste **Azure IoT Edge-taak** om deze te bewerken. Deze taak bouwt alle modules in de oplossing met het doelplatform dat u opgeeft.

   * **Weergavenaam**: Accepteer de **standaardAzure IoT Edge - Moduleafbeeldingen bouwen**.
   * **Actie:** Accepteer de standaardafbeeldingen van de **buildmodule.**
   * **.template.json-bestand**: Selecteer de ellips (**...**) en navigeer naar het bestand **deployment.template.json** in de opslagplaats met uw IoT Edge-oplossing.
   * **Standaardplatform:** Selecteer het juiste platform voor uw modules op basis van uw doel-IoT Edge-apparaat.
   * **Uitvoervariabelen:** de uitvoervariabelen bevatten een referentienaam die u gebruiken om het bestandspad te configureren waar uw deployment.json-bestand wordt gegenereerd. Stel de referentienaam in op iets gedenkwaardigs als **edge.**

7. Selecteer de tweede **Azure IoT Edge-taak** om deze te bewerken. Met deze taak worden alle moduleafbeeldingen naar het containerregister dat u selecteert.

   * **Weergavenaam:** de weergavenaam wordt automatisch bijgewerkt wanneer het actieveld verandert.
   * **Actie:** Gebruik de vervolgkeuzelijst om **moduleafbeeldingen te**selecteren.
   * **Type containerregister:** selecteer het type containerregister dat u gebruikt om uw moduleafbeeldingen op te slaan. Afhankelijk van welk registertype u kiest, wordt het formulier gewijzigd. Als u **Azure Container Registry**kiest, gebruikt u de vervolgkeuzelijsten om het Azure-abonnement en de naam van uw containerregister te selecteren. Als u **Algemeen containerregister**kiest, selecteert u **Nieuw** om een registerserviceverbinding te maken.
   * **.template.json-bestand**: Selecteer de ellips (**...**) en navigeer naar het bestand **deployment.template.json** in de opslagplaats met uw IoT Edge-oplossing.
   * **Standaardplatform**: Selecteer hetzelfde platform als de ingebouwde moduleafbeeldingen.

   Als u meerdere containerregisters hebt om uw moduleafbeeldingen te hosten, moet u deze taak dupliceren, verschillende containerregisterselecteren en **Bypass-module(s)** gebruiken in de geavanceerde instellingen om de afbeeldingen te omzeilen die niet voor dit specifieke register zijn.

8. Selecteer de taak **Bestanden kopiëren** om deze te bewerken. Gebruik deze taak om bestanden naar de map met artefact-fasering te kopiëren.

   * **Weergavenaam:** Bestanden kopiëren naar: map neerzetten.
   * **Inhoud**: Plaats twee regels `deployment.template.json` `**/module.json`in deze sectie en . Deze twee typen bestanden zijn de ingangen om IoT Edge-implementatiemanifest te genereren. Moet worden gekopieerd naar de artefact staging map en gepubliceerd voor release pijplijn.
   * **Doelmap:** Zet `$(Build.ArtifactStagingDirectory)`de variabele . Zie [Variabelen maken](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) voor meer informatie over de beschrijving.

9. Selecteer de taak **Artefacten publiceren om** deze te bewerken. Geef het mappad voor artefacten naar de taak zodat het pad kan worden gepubliceerd om pijplijn vrij te geven.

   * **Weergavenaam:** Artefact publiceren: neerzetten.
   * **Pad om te**publiceren `$(Build.ArtifactStagingDirectory)`: Zet de variabele . Zie [Variabelen maken](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) voor meer informatie over de beschrijving.
   * **Artefact naam:** drop.
   * **Artefact-publicatielocatie:** Azure Pipelines.

10. Open het tabblad **Triggers** en schakel het selectievakje in om **continue integratie in te schakelen**. Zorg ervoor dat de vertakking met uw code is opgenomen.

    ![Continue integratietrigger inschakelen](./media/how-to-ci-cd/configure-trigger.png)

11. Sla de nieuwe buildpijplijn op met de knop **Opslaan.**

Deze pijplijn is nu geconfigureerd om automatisch uit te voeren wanneer u nieuwe code naar uw repo pusht. De laatste taak, het publiceren van de pijplijnartefacten, activeert een releasepijplijn. Ga verder naar het volgende gedeelte om de releasepijplijn te bouwen.

## <a name="configure-continuous-deployment"></a>Continue implementatie inschakelen

In deze sectie maakt u een releasepijplijn die is geconfigureerd om automatisch uit te voeren wanneer artefacten van de buildpijplijn worden uitgevoerd en implementatielogboeken in Azure Pipelines worden weergegeven.

Een nieuwe pijplijn maken en een nieuwe fase toevoegen

1. Kies op het tabblad **Releases** de optie **+ Nieuwe pijplijn**. Of als u al pijplijnen voor het vrijgeven hebt, kiest u de knop **+ Nieuw** en selecteert u + **Nieuwe releasepijplijn**.  

    ![Releasepijplijn toevoegen](./media/how-to-ci-cd/add-release-pipeline.png)

2. Wanneer u wordt gevraagd een sjabloon te selecteren, kiest u ervoor om te beginnen met een **lege taak**.

    ![Begin met een lege taak](./media/how-to-ci-cd/start-with-empty-job.png)

3. De nieuwe releasepijplijn wordt geïnitialiseert met één fase, **genaamd Fase 1.** Hernoem Fase 1 **in dev** en behandel het als een testomgeving. Meestal hebben continue implementatiepijplijnen meerdere fasen, waaronder **dev,** **staging** en **prod**. U meer maken op basis van uw DevOps-praktijk. Sluit het venster met podiumdetails zodra de naam is gewijzigd.

4. Koppel de release aan de buildartefacten die worden gepubliceerd door de buildpijplijn. Klik **op Artefacten** gebied toevoegen.

   ![Artefacten toevoegen](./media/how-to-ci-cd/add-artifacts.png)  

5. Selecteer brontype **Build**in **Een artefact-pagina toevoegen**. Selecteer vervolgens het project en de pijplijn die u hebt gemaakt. Selecteer vervolgens **Toevoegen**.

   ![Een build-artefact toevoegen](./media/how-to-ci-cd/add-an-artifact.png)

6. Open de artefacttriggers en selecteer de schakelknop om de trigger voor continue implementatie in te schakelen. Nu zal er een nieuwe release worden gemaakt elke keer dat er een nieuwe build beschikbaar is.

   ![Implementatietrigger voor continue implementatie configureren](./media/how-to-ci-cd/add-a-trigger.png)

7. De **dev-fase** is vooraf geconfigureerd met één taak en nul taken. Selecteer **taken** in het pijplijnmenu en kies vervolgens de **dev-fase.**  Selecteer de taak en het taakaantal om de taken in deze fase te configureren.

    ![Dev-taken configureren](./media/how-to-ci-cd/view-stage-tasks.png)

8. In **de dev-fase** ziet u een **standaardagenttaak**. U details over de agenttaak configureren, maar de implementatietaak is platformongevoelig, zodat u **hosted VS2017** of **Hosted Ubuntu 1604** gebruiken in de **agentgroep** (of een andere agent die door uzelf wordt beheerd).

9. Selecteer het plusteken (**+**) om twee taken toe te voegen. Zoek en voeg **Azure IoT Edge** twee keer toe.

    ![Taken voor dev toevoegen](./media/how-to-ci-cd/add-task-qa.png)

10. Selecteer de eerste **Azure IoT Edge-taak** en configureer deze met de volgende waarden:

    * **Weergavenaam:** de weergavenaam wordt automatisch bijgewerkt wanneer het actieveld verandert.
    * **Actie:** gebruik de vervolgkeuzelijst om **implementatiemanifest genereren**te selecteren . Als u de actiewaarde wijzigt, wordt ook de naam van de taakweergave bijgewerkt.
    * **.template.json-bestand**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Zet het pad . Het pad wordt gepubliceerd vanaf build pipeline.
    * **Standaardplatform**: Kies dezelfde waarde bij het bouwen van de moduleafbeeldingen.
    * **Uitvoerpad**: Zet `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`het pad . Dit pad is het laatste IoT Edge-implementatiemanifestbestand.

    Met deze configuraties u de `deployment.template.json` URL's van de moduleafbeelding in het bestand vervangen. Het **manifest Implementatie genereren** helpt ook de variabelen te vervangen `deployment.template.json` door de exacte waarde die u in het bestand hebt gedefinieerd. In VS/VS-code geeft u de werkelijke `.env` waarde in een bestand op. In Azure Pipelines stelt u de waarde in op het tabblad Regelvariabelen voor vrijgevenpijplijn. Ga naar het tabblad Variabelen en configureer de naam en waarde als volgt.

    * **ACR_ADDRESS:** uw Azure Container Registry-adres.
    * **ACR_PASSWORD:** uw azure-containerregisterwachtwoord.
    * **ACR_USER:** uw azure-containerregistergebruikersnaam.

    Als u andere variabelen in uw project hebt, u de naam en waarde op dit tabblad opgeven. Het **manifest Voor implementatie genereren** kan `${VARIABLE}` alleen de variabelen herkennen die `*.template.json` op smaak zijn, zorg ervoor dat u dit in uw bestanden gebruikt.

    ![Variabelen configureren voor releasepijplijn](./media/how-to-ci-cd/configure-variables.png)

11. Selecteer de tweede **Azure IoT Edge-taak** en configureer deze met de volgende waarden:

    * **Weergavenaam:** de weergavenaam wordt automatisch bijgewerkt wanneer het actieveld verandert.
    * **Actie:** gebruik de vervolgkeuzelijst om **Implementeren naar IoT Edge-apparaten te**selecteren. Als u de actiewaarde wijzigt, wordt ook de naam van de taakweergave bijgewerkt.
    * **Azure-abonnement**: selecteer het abonnement dat uw IoT Hub bevat.
    * **IoT Hub-naam:** Selecteer uw IoT-hub.
    * **Kies één/meerdere apparaten:** kies of u de releasepijplijn op één apparaat of meerdere apparaten wilt implementeren.
      * Als u op één apparaat implementeert, voert u de **IE-id van IoT Edge in.**
      * Als u op meerdere apparaten implementeert, geeft u de **doelvoorwaarde**van het apparaat op. De doelvoorwaarde is een filter die overeenkomt met een set IoT Edge-apparaten in IoT Hub. Als u apparaattags als voorwaarde wilt gebruiken, moet u de bijbehorende apparaattags bijwerken met de dubbele IoT Hub-apparaat. Werk de prioriteit van de **IoT Edge-implementatie-id** en **de implementatie van IoT Edge bij** in de geavanceerde instellingen. Zie [IoT Edge-automatische implementaties begrijpen](module-deployment-monitoring.md)voor meer informatie over het maken van een implementatie voor meerdere apparaten.
    * Geavanceerde instellingen uitvouwen, **IoT Edge-implementatie-id**selecteren, de variabele plaatsen `$(System.TeamProject)-$(Release.EnvironmentName)`. Hiermee worden de project- en releasenaam in kaart gebracht met uw IoT Edge-implementatie-id.

12. Selecteer **Opslaan** om de wijzigingen op te slaan in de nieuwe releasepijplijn. Ga terug naar de pijplijnweergave door **Pijplijn** in het menu te selecteren.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD verifiëren met de build- en releasepijplijnen

Als u een build-taak wilt activeren, u een commit naar de broncoderepository pushen of handmatig activeren. In deze sectie activeert u handmatig de CI/CD-pijplijn om te testen of deze werkt. Controleer vervolgens of de implementatie is geslaagd.

1. Navigeer naar de buildpijplijn die u aan het begin van dit artikel hebt gemaakt.

2. U een buildtaak in uw buildpijplijn activeren door de knop **Wachtrij** te selecteren als in de volgende schermafbeelding.

    ![Handmatige trigger](./media/how-to-ci-cd/manual-trigger.png)

3. Selecteer de buildtaak om de voortgang ervan te bekijken. Als de buildpijplijn is voltooid, wordt een release naar **dev-fase** geactiveerd.

    ![Logboeken bouwen](./media/how-to-ci-cd/build-logs.png)

4. Met de succesvolle **dev-release** wordt IoT Edge-implementatie mogelijk om IoT Edge-apparaten te targeten.

    ![Release naar dev](./media/how-to-ci-cd/pending-approval.png)

5. Klik **op dev-fase** om de releaselogs te bekijken.

    ![Release-logboeken](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Volgende stappen

* Voorbeeld van best practices voor IoT Edge DevOps in [Azure DevOps Project voor IoT Edge](how-to-devops-project.md)
* Inzicht in de IoT Edge-implementatie in [IoT Edge-implementaties begrijpen voor afzonderlijke apparaten of op schaal](module-deployment-monitoring.md)
* Loop door de stappen om een implementatie te maken, bij te werken of te verwijderen in [IoT Edge-modules implementeren en bewaken op schaal.](how-to-deploy-monitor.md)
