---
title: 'Quick Start: een Java-app maken in azure Service Fabric'
description: In deze snelstart maakt u een Java-toepassing voor Azure met behulp van een voorbeeldtoepassing van de betrouwbare Service Fabric-services.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 257fd02c2f7ec2aff9d55b91b2cbd54b6eb55431
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464400"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Snelstartgids: een Java-app implementeren in azure Service Fabric op Linux

In deze Quick start ziet u hoe u uw eerste Java-toepassing implementeert in azure Service Fabric met behulp van de eclips IDE op een Linux-ontwikkelaars computer. Wanneer u klaar bent, hebt u een stemtoepassing met een web-front-end in Java die stemresultaten opslaat in een stateful back-endservice in het cluster.

Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers.

![Service Fabric stem voorbeeld van Azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

In deze snelstartgids leert u de volgende zaken:

* Eclipse gebruiken als hulpmiddel voor uw Java-toepassingen in Service Fabric
* De toepassing implementeren in het lokale cluster
* De toepassing op meerdere knooppunten uitschalen

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Service Fabric-SDK en Service Fabric CLI (opdrachtregelinterface) installeren](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git installeren](https://git-scm.com/)
3. [Eclipse installeren](https://www.eclipse.org/downloads/)
4. [Java-omgeving instellen](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), inclusief de optionele stappen voor het installeren van de Eclipse-invoegtoepassing

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de opslagplaats van de voorbeeld-app te klonen op uw lokale computer.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

1. Start het lokale cluster door de volgende opdracht uit te voeren:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Het starten van het lokale cluster kan enige tijd duren. Open Service Fabric Explorer op **http://localhost:19080** om te controleren of het cluster volledig naar behoren functioneert. Als de vijf knooppunten in orde zijn, is het lokale cluster actief.

    ![In azure Service Fabric Explorer worden de juiste knoop punten weer gegeven](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Open Eclipse.
3. Selecteer **bestand** >  > **Gradle** te **importeren** > **bestaand Gradle-project** en volg de wizard.
4. Selecteer **Directory** en kies de `Voting` Directory van de `service-fabric-java-quickstart` map die u hebt gekloond van github. Selecteer **Finish**.

    ![Gradle-project importeren in eclips](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. U hebt nu het project `Voting` in de pakketverkenner voor Eclipse.
6. Klik met de rechter muisknop op het project en selecteer **toepassing publiceren** onder de vervolg keuzelijst **service Fabric** . Kies **PublishProfiles/local. json** als doel profiel en selecteer **publiceren**.

    ![Lokale JSON publiceren met Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Open uw favoriete webbrowser om toegang te krijgen tot de toepassing via `http://localhost:8080`.

    ![Lokale host van Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

U kunt nu een reeks stemmingsopties toevoegen en beginnen met het verzamelen van stemmen. De toepassing wordt uitgevoerd en alle gegevens worden opgeslagen in het Service Fabric-cluster, zonder dat hiervoor een aparte database nodig is.

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er zijn veel manieren waarop u services kunt schalen. U kunt bijvoorbeeld scripts of opdrachten van Service Fabric CLI (sfctl) gebruiken. In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer kan worden uitgevoerd in alle Service Fabric-clusters en is toegankelijk door vanuit een browser te bladeren naar de HTTP-beheerpoort (19080) van het cluster, bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in het cluster - bijvoorbeeld: `https://localhost:19080`.
2. Selecteer het beletsel teken ( **...** ) naast het knoop punt **Fabric:/stem/VotingWeb** in de structuur weergave en selecteer **service schalen**.

    ![Een service schalen in azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    U kunt er nu voor kiezen om het aantal exemplaren van de web-front-endservice te schalen.

3. Wijzig het getal in **2** en selecteer **service schalen**.
4. Selecteer het knoop punt **Fabric:/stem/VotingWeb** in de structuur weergave en vouw het partitie knooppunt uit (vertegenwoordigd door een GUID).

    ![Geschaalde service in azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    U ziet nu dat er twee exemplaren van de service zijn. In de structuurweergave ziet u op welke knooppunten de exemplaren worden uitgevoerd.

Met deze eenvoudige beheertaak hebt u het aantal beschikbare resources voor het verwerken van gebruikersbelasting voor de front-endservice verdubbeld. Het is belangrijk te weten dat u niet meerdere exemplaren van een service nodig hebt om ervoor te zorgen dat deze op betrouwbare wijze wordt uitgevoerd. Als de service mislukt, wordt in Service Fabric een nieuw exemplaar van de service uitgevoerd in het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* Eclipse gebruiken als hulpmiddel voor uw Java-toepassingen in Service Fabric
* Java-toepassingen implementeren in het lokale cluster
* De toepassing op meerdere knooppunten uitschalen

Meer informatie over het werken met Java-apps in Service Fabric vindt u in de zelfstudie voor Java-apps.

> [!div class="nextstepaction"]
> [Een Java-app implementeren](./service-fabric-tutorial-create-java-app.md)
