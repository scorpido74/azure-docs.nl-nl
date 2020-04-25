---
title: Continue integratie & doorlopende implementatie-Azure IoT Edge
description: Continue integratie en doorlopende implementatie instellen-Azure IoT Edge met Azure DevOps, Azure-pijp lijnen
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a653d13137a3067bfaf51c64c09454a08783e31
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131416"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Continue integratie en continue implementatie naar Azure IoT Edge

U kunt eenvoudig DevOps met uw Azure IoT Edge-toepassingen met de ingebouwde Azure IoT Edge taken in azure-pijp lijnen. In dit artikel wordt beschreven hoe u de functies continue integratie en continue implementatie van Azure-pijp lijnen kunt gebruiken om toepassingen snel en efficiënt te bouwen, te testen en te implementeren op uw Azure IoT Edge.

![Diagram-CI-en CD-vertakkingen voor ontwikkeling en productie](./media/how-to-ci-cd/cd.png)

In dit artikel leert u hoe u de ingebouwde Azure IoT Edge taken voor Azure-pijp lijnen kunt gebruiken om twee pijp lijnen te maken voor uw IoT Edge oplossing. Er kunnen vier acties worden gebruikt in de Azure IoT Edge taken.

* **Azure IOT Edge-build-installatie kopieën van de module maken** uw IOT Edge oplossings code en bouwt de container installatie kopieën.
* Met **Azure IOT Edge-push installatie kopieën** worden module-installatie kopieën gepusht naar het container register dat u hebt opgegeven.
* **Azure IOT Edge-implementatie manifest maken** neemt een implementatie. sjabloon. JSON-bestand en de variabelen op, waarna het definitieve manifest bestand voor IOT Edge implementatie wordt gegenereerd.
* **Azure IOT Edge-implementeren op IOT edge-apparaten** helpt bij het maken van IOT Edge-implementaties op één of meerdere IOT edge apparaten.

## <a name="prerequisites"></a>Vereisten

* Een Azure opslag plaatsen-opslag plaats. Als u er nog geen hebt, kunt u [een nieuwe Git-opslag plaats in uw project maken](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Een IoT Edge oplossing die is doorgevoerd en gepusht naar uw opslag plaats. Als u een nieuwe voorbeeld oplossing wilt maken voor het testen van dit artikel, volgt u de stappen in [modules voor ontwikkel-en fout opsporing in Visual Studio code](how-to-vs-code-develop-module.md) of [ontwikkel en debug C#-modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   Voor dit artikel hoeft u alleen de map Solution te maken die is gemaakt door de IoT Edge sjablonen in Visual Studio code of Visual Studio. U hoeft deze code niet te bouwen, te pushen, te implementeren of te debuggen voordat u doorgaat. U stelt deze processen in op Azure-pijp lijnen.

   Als u een nieuwe oplossing wilt maken, moet u uw opslag plaats lokaal eerst klonen. Wanneer u de oplossing maakt, kunt u ervoor kiezen om deze rechtstreeks in de map opslag plaats te maken. U kunt de nieuwe bestanden eenvoudig van daaruit door voeren en pushen.

* Een container register waar u module installatie kopieën kunt pushen. U kunt [Azure container Registry](https://docs.microsoft.com/azure/container-registry/) of een REGI ster van derden gebruiken.
* Een actieve [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) met ten minste IOT edge apparaten voor het testen van de afzonderlijke test-en productie-implementatie fasen. U kunt de Quick Start-artikelen volgen om een IoT Edge apparaat te maken in [Linux](quickstart-linux.md) of [Windows](quickstart.md)

Zie [uw code delen met Visual Studio en Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) voor meer informatie over het gebruik van Azure opslag plaatsen

## <a name="configure-continuous-integration"></a>Continue integratie configureren

In deze sectie maakt u een nieuwe build-pijp lijn. Configureer de pijp lijn zo dat deze automatisch wordt uitgevoerd wanneer u wijzigingen aanbrengt in de voor beeld-IoT Edge oplossing en publiceer build-Logboeken.

>[!NOTE]
>In dit artikel wordt gebruikgemaakt van de Visual Designer van Azure DevOps. Voordat u de stappen in deze sectie volgt, schakelt u de preview-functie uit voor het maken van de nieuwe YAML pipeline-ervaring.
>
>1. Selecteer in azure DevOps uw profiel pictogram en selecteer vervolgens **Preview-functies**.
>2. Schakel de functie voor het **maken van nieuwe YAML-pijp lijnen** uit.
>
>Zie [een build-pijp lijn maken](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)voor meer informatie.

1. Meld u aan bij uw Azure DevOps-organisatie (**https:\//dev.Azure.com/{your organization}/**) en open het project met uw IOT Edge Solution-opslag plaats.

   Voor dit artikel hebt u een opslag plaats gemaakt met de naam **IoTEdgeRepo**. Deze opslag plaats bevat **IoTEdgeSolution** die de code voor een module met de naam **filtermodule**heeft.

   ![Open uw DevOps-project](./media/how-to-ci-cd/init-project.png)

2. Navigeer naar Azure-pijp lijnen in uw project. Open het tabblad **builds** en selecteer **nieuwe pijp lijn**. Als u al een pijp lijn hebt gemaakt, selecteert u de knop **Nieuw** . Kies vervolgens **nieuwe build-pijp lijn**.

    ![Een nieuwe build-pipeline maken](./media/how-to-ci-cd/add-new-build.png)

3. Volg de aanwijzingen om uw pijp lijn te maken.

   1. Geef de bron gegevens voor de nieuwe build-pijp lijn op. Selecteer **Azure opslag plaatsen Git** als bron en selecteer vervolgens het project, de opslag plaats en de vertakking waar uw IOT Edge oplossings code zich bevindt. Selecteer vervolgens **door gaan**.

      ![De pijplijn bron selecteren](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecteer **lege taak** in plaats van een sjabloon.

      ![Beginnen met een leeg proces](./media/how-to-ci-cd/start-with-empty.png)

4. Zodra de pijp lijn is gemaakt, wordt u naar de pijplijn editor geleid. Kies in de pijplijn beschrijving de juiste agent pool op basis van uw doel platform:

   * Als u uw modules in platform amd64 voor Linux-containers wilt maken, kiest u **gehoste Ubuntu 1604**

   * Als u uw modules in platform amd64 voor Windows 1809-containers wilt maken, moet u [zelf-hostende agent instellen in Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Als u uw modules in platform arm32v7 of arm64 voor Linux-containers wilt maken, moet u [zelf-hostende agent instellen in Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Groep bouwen agent configureren](./media/how-to-ci-cd/configure-env.png)

5. Uw pijp lijn wordt vooraf geconfigureerd met een taak genaamd **Agent taak 1**. Selecteer het plus teken (**+**) om drie taken toe te voegen aan de taak: **Azure IOT Edge** twee maal, **Kopieer bestanden** één keer en **Publiceer de build-artefacten** eenmaal. (Beweeg de muis aanwijzer over de naam van elke taak om de knop **toevoegen** te zien.)

   ![Azure IoT Edge taak toevoegen](./media/how-to-ci-cd/add-iot-edge-task.png)

   Wanneer alle vier de taken zijn toegevoegd, ziet uw agent-taak eruit als in het volgende voor beeld:

   ![Drie taken in de build-pijp lijn](./media/how-to-ci-cd/add-tasks.png)

6. Selecteer de eerste **Azure IOT Edge** taak om deze te bewerken. Met deze taak worden alle modules in de oplossing gebaseerd op het doel platform dat u opgeeft.

   * **Weergave naam**: accepteer de standaard instellingen voor de **module installatie van Azure IOT Edge-build**.
   * **Actie**: accepteer de standaard **installatie kopieën**voor de build-module.
   * **. sjabloon. JSON-bestand**: Selecteer het weglatings teken (**...**) en navigeer naar het bestand **Deployment. Temp late. json** in de opslag plaats met uw IOT EDGE-oplossing.
   * **Standaard platform**: Selecteer het juiste platform voor uw modules op basis van uw doel IOT edge apparaat.
   * **Uitvoer variabelen**: de uitvoer variabelen bevatten een referentie naam die u kunt gebruiken om het bestandspad te configureren waar uw implementatie. JSON-bestand wordt gegenereerd. Stel de naam van de verwijzing in op een onthouden soort, net als een **rand**.

7. Selecteer de tweede **Azure IOT Edge** taak om deze te bewerken. Met deze taak worden alle module-installatie kopieën gepusht naar het container register dat u selecteert.

   * **Weergave naam**: de weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd.
   * **Actie**: gebruik de vervolg keuzelijst om **installatie kopieën van push module**te selecteren.
   * **Container register type**: Selecteer het type container register dat u gebruikt om de module installatie kopieën op te slaan. Afhankelijk van het register type dat u kiest, verandert het formulier. Als u **Azure container Registry**selecteert, gebruikt u de vervolg keuzelijst om het Azure-abonnement en de naam van uw container register te selecteren. Als u **algemene container Registry**kiest, selecteert u **Nieuw** om een register service verbinding te maken.
   * **. sjabloon. JSON-bestand**: Selecteer het weglatings teken (**...**) en navigeer naar het bestand **Deployment. Temp late. json** in de opslag plaats met uw IOT EDGE-oplossing.
   * **Standaard platform**: Selecteer hetzelfde platform als uw ingebouwde module-installatie kopieën.

   Als u meerdere container registers hebt om uw module installatie kopieën te hosten, moet u deze taak dupliceren, een ander container register selecteren en **module (s) overs Laan** gebruiken in de geavanceerde instellingen om de installatie kopieën die niet voor dit specifieke REGI ster zijn, over te slaan.

8. Selecteer de taak **bestanden kopiëren** om deze te bewerken. Gebruik deze taak om bestanden te kopiëren naar de map voor het voorbereiden van artefacten.

   * **Weergave naam**: bestanden kopiëren naar: map verwijderen.
   * **Inhoud**: plaats twee regels in deze sectie `deployment.template.json` en. `**/module.json` Deze twee typen bestanden zijn de invoer voor het genereren van IoT Edge implementatie manifest. Moet worden gekopieerd naar de map voor het voorbereiden van een artefact en gepubliceerd voor release pijplijn.
   * **Doelmap**: plaats de variabele `$(Build.ArtifactStagingDirectory)`. Zie [Build Varia bles](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) voor meer informatie over de beschrijving.

9. Selecteer de taak **Build-artefacten publiceren** om deze te bewerken. Geef het pad naar de map voor het klaarzetten van artefacten naar de taak zodat het pad naar de release pijplijn kan worden gepubliceerd.

   * **Weergave naam**: publicatie-artefact: neerzetten.
   * **Pad voor publiceren**: plaats de variabele `$(Build.ArtifactStagingDirectory)`. Zie [Build Varia bles](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) voor meer informatie over de beschrijving.
   * **Artefact naam**: neerzetten.
   * **Publicatie locatie artefacten**: Azure-pijp lijnen.

10. Open het tabblad **Triggers** en schakel het selectie vakje in om **continue integratie in te scha kelen**. Zorg ervoor dat de vertakking met uw code is opgenomen.

    ![Trigger voor continue integratie inschakelen](./media/how-to-ci-cd/configure-trigger.png)

11. Sla de nieuwe build-pijp lijn op met de knop **Opslaan** .

Deze pijp lijn is nu geconfigureerd om automatisch te worden uitgevoerd wanneer u nieuwe code naar uw opslag plaats pusht. De laatste taak, die pijp lijn artefacten publiceert, een release pijplijn wordt geactiveerd. Ga door naar de volgende sectie om de release pijplijn te bouwen.

## <a name="configure-continuous-deployment"></a>Continue implementatie inschakelen

In deze sectie maakt u een release pijplijn die is geconfigureerd om automatisch te worden uitgevoerd wanneer uw build-pijp lijn artefacten overneemt en de implementatie Logboeken in azure-pijp lijnen worden weer gegeven.

Een nieuwe pijp lijn maken en een nieuw stadium toevoegen

1. Klik op het tabblad **releases** op **+ nieuwe pijp lijn**. Als u al release pijplijnen hebt, kiest u de knop **+ Nieuw** en selecteert u **+ nieuwe release pijplijn**.  

    ![Release pijplijn toevoegen](./media/how-to-ci-cd/add-release-pipeline.png)

2. Wanneer u wordt gevraagd om een sjabloon te selecteren, kiest u om te beginnen met een **lege taak**.

    ![Beginnen met een lege taak](./media/how-to-ci-cd/start-with-empty-job.png)

3. Uw nieuwe release pijplijn wordt geïnitialiseerd met één fase, de naam **fase 1**. Wijzig de naam fase 1 in **dev** en behandel deze als een test omgeving. Doorgaans hebben doorlopende implementatie pijplijnen meerdere fasen, waaronder **ontwikkeling**, **fase ring** en **productie**. U kunt meer maken op basis van uw DevOps-oefening. Sluit het venster met fase details zodra de naam ervan is gewijzigd.

4. Koppel de release aan de constructie-artefacten die worden gepubliceerd door de build-pijp lijn. Klik op **toevoegen** in het gebied artefacten.

   ![Artefacten toevoegen](./media/how-to-ci-cd/add-artifacts.png)  

5. Selecteer op **de pagina een artefact toevoegen de**optie bron type **Build**. Selecteer vervolgens het project en de build-pijp lijn die u hebt gemaakt. Selecteer vervolgens **Toevoegen**.

   ![Een build-artefact toevoegen](./media/how-to-ci-cd/add-an-artifact.png)

6. Open de artefact triggers en selecteer de schakel optie om de continue implementatie trigger in te scha kelen. Er wordt nu een nieuwe release gemaakt wanneer een nieuwe build beschikbaar is.

   ![Trigger voor continue implementatie configureren](./media/how-to-ci-cd/add-a-trigger.png)

7. De **ontwikkelings** fase is vooraf geconfigureerd met één taak en nul taken. Selecteer in het menu pijp lijn **taken** en kies vervolgens de **ontwikkelings** fase.  Selecteer de taak en het aantal taken voor het configureren van de taken in deze fase.

    ![Dev-taken configureren](./media/how-to-ci-cd/view-stage-tasks.png)

8. In de **ontwikkel** fase ziet u een standaard **Agent taak**. U kunt details over de agent taak configureren, maar de implementatie taak is niet van het platform, zodat u een **gehoste VS2017** of **gehoste Ubuntu 1604** in de **agent groep** (of een andere door uzelf beheerde agent) kunt gebruiken.

9. Selecteer het plus teken (**+**) om twee taken toe te voegen. Zoek en Voeg twee keer **Azure IOT Edge** toe.

    ![Taken toevoegen voor dev](./media/how-to-ci-cd/add-task-qa.png)

10. Selecteer de eerste **Azure IOT Edge** taak en configureer deze met de volgende waarden:

    * **Weergave naam**: de weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd.
    * **Actie**: gebruik de vervolg keuzelijst om **implementatie manifest genereren**te selecteren. Als u de actie waarde wijzigt, wordt ook de weergave naam van de taak bijgewerkt zodat deze overeenkomt.
    * **. sjabloon. JSON-bestand**: plaats het `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`pad. Het pad wordt gepubliceerd vanuit een build-pijp lijn.
    * **Standaard platform**: Kies dezelfde waarde bij het bouwen van de module installatie kopieën.
    * **Uitvoerpad**: plaats het pad `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Dit pad is het uiteindelijke manifest bestand voor de implementatie van IoT Edge.

    Deze configuraties helpt bij het vervangen van de installatie kopie `deployment.template.json` -url's van de module in het bestand. Het **genereren van het implementatie manifest** helpt ook de variabelen te vervangen door de exacte waarde die `deployment.template.json` u in het bestand hebt gedefinieerd. In VS/VS-code geeft u de daad werkelijke waarde op in `.env` een bestand. In azure-pijp lijnen stelt u de waarde in op het tabblad release pijplijn variabelen. Ga naar het tabblad variabelen en configureer de naam en waarde als volgt.

    * **ACR_ADDRESS**: uw Azure container Registry adres.
    * **ACR_PASSWORD**: uw Azure container Registry wacht woord.
    * **ACR_USER**: de gebruikers naam van uw Azure container Registry.

    Als u andere variabelen in uw project hebt, kunt u de naam en waarde op dit tabblad opgeven. Het **manifest** voor het genereren van `${VARIABLE}` implementaties kan alleen de variabelen herkennen. Zorg ervoor dat u deze in uw `*.template.json` bestanden gebruikt.

    ![Variabelen voor release pijplijn configureren](./media/how-to-ci-cd/configure-variables.png)

11. Selecteer de tweede **Azure IOT Edge** taak en configureer deze met de volgende waarden:

    * **Weergave naam**: de weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd.
    * **Actie**: gebruik de vervolg keuzelijst om **implementeren naar IOT edge-apparaten**te selecteren. Als u de actie waarde wijzigt, wordt ook de weergave naam van de taak bijgewerkt zodat deze overeenkomt.
    * **Azure-abonnement**: Selecteer het abonnement dat uw IOT hub bevat.
    * **IOT hub naam**: Selecteer uw IOT-hub.
    * **Kies één of meerdere**apparaten: Kies of u wilt dat de release pijplijn op één apparaat of op meerdere apparaten wordt geïmplementeerd.
      * Als u op één apparaat implementeert, voert u de **IOT edge apparaat-id**in.
      * Als u implementeert op meerdere apparaten, geeft u de **voor waarde voor**het apparaat doel op. De doel voorwaarde is een filter dat overeenkomt met een set IoT Edge apparaten in IoT Hub. Als u Device Tags als voor waarde wilt gebruiken, moet u de labels van de bijbehorende apparaten bijwerken met IoT Hub apparaat twee. Werk de **IOT Edge implementatie-id** en **IOT Edge implementatie prioriteit** bij in de geavanceerde instellingen. Zie [inzicht IOT Edge automatische implementaties](module-deployment-monitoring.md)voor meer informatie over het maken van een implementatie voor meerdere apparaten.
    * Vouw geavanceerde instellingen uit, selecteer **IOT Edge implementatie-id**, plaats `$(System.TeamProject)-$(Release.EnvironmentName)`de variabele. Hiermee wordt de naam van het project en de release toegewezen aan uw IoT Edge-implementatie-ID.

12. Selecteer **Opslaan** om uw wijzigingen op te slaan in de nieuwe release pijplijn. Ga terug naar de pijplijn weergave door **pijp lijn** te selecteren in het menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD controleren met pijp lijnen voor Build en release

Als u een build-taak wilt activeren, kunt u een door voeren naar de opslag plaats van de bron code pushen of hand matig activeren. In deze sectie kunt u hand matig de CI/CD-pijp lijn activeren om te testen dat deze werkt. Controleer vervolgens of de implementatie slaagt.

1. Ga naar de build-pijp lijn die u aan het begin van dit artikel hebt gemaakt.

2. U kunt een build-taak in uw build-pijp lijn activeren door de knop **Queue** te selecteren als de volgende scherm afbeelding.

    ![Hand matige trigger](./media/how-to-ci-cd/manual-trigger.png)

3. Selecteer de taak maken om de voortgang te bekijken. Als de build-pijp lijn met succes is voltooid, wordt een release-naar- **ontwikkelings** fase geactiveerd.

    ![Logboeken maken](./media/how-to-ci-cd/build-logs.png)

4. Met de geslaagde **dev** -versie wordt IOT Edge-implementatie gemaakt voor doel apparaten van IOT Edge.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Klik op **ontwikkel** fase om release logboeken te bekijken.

    ![Release-logboeken](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Volgende stappen

* IoT Edge DevOps best practices-voor beeld in het [Azure DevOps-project voor IOT Edge](how-to-devops-project.md)
* Inzicht in de implementatie van IoT Edge in [IOT Edge implementaties voor één of op een bepaalde schaal](module-deployment-monitoring.md)
* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
