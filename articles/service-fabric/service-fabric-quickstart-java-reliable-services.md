---
title: 'Quick Start: een Java-app maken in azure Service Fabric'
description: In deze snelstart maakt u een Java-toepassing voor Azure met behulp van een voorbeeldtoepassing van de betrouwbare Service Fabric-services.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: fc615149b092aebfdde767fb3b716fb897bfd551
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77121501"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Snelstartgids: een Java-app implementeren in azure Service Fabric op Linux

In deze Quick Start implementeert u een Java-toepassing naar Azure Service Fabric met behulp van de eclips IDE op een Linux-ontwikkelaars computer. Wanneer u klaar bent, hebt u een stemtoepassing met een web-front-end in Java die stemresultaten opslaat in een stateful back-endservice in het cluster.

Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers.

## <a name="prerequisites"></a>Vereisten

- [Java-omgeving](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) en [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclips (4.6) +](https://www.eclipse.org/downloads/packages/) en [eclips-invoeg toepassing voor service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK en de opdracht regel interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de opslagplaats van de voorbeeld-app te klonen op uw lokale computer.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

1. Start het lokale cluster door de volgende opdracht uit te voeren:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Het starten van het lokale cluster kan enige tijd duren. Open Service Fabric Explorer op `http://localhost:19080` om te controleren of het cluster volledig naar behoren functioneert. Als de vijf knooppunten in orde zijn, is het lokale cluster actief.

    ![In azure Service Fabric Explorer worden de juiste knoop punten weer gegeven](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Open Eclipse.
3. Selecteer **bestand** > **importeren** > **Gradle**Gradle > **bestaand Gradle-project** en volg de wizard.
4. Selecteer **map** en kies de map **stemmen** in de map **service-Fabric-Java-Quick** start die u hebt gekloond van github. Selecteer **Finish**.

    ![Gradle-project importeren in eclips](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. U hebt nu het project `Voting` in de pakketverkenner voor Eclipse.
6. Klik met de rechter muisknop op het project en selecteer **toepassing publiceren** onder de vervolg keuzelijst **service Fabric** . Kies **PublishProfiles/local. json** als doel profiel en selecteer **publiceren**.

    ![Lokale JSON publiceren met Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Open uw favoriete webbrowser en open de toepassing op `http://localhost:8080`.

    ![Lokale host van Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

U kunt nu een reeks stemmingsopties toevoegen en beginnen met het verzamelen van stemmen. De toepassing wordt uitgevoerd en alle gegevens worden opgeslagen in het Service Fabric-cluster, zonder dat hiervoor een aparte database nodig is.

![Service Fabric stem voorbeeld van Azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er zijn veel manieren om uw services te schalen. U kunt bijvoorbeeld scripts of opdrachten van Service Fabric CLI (`sfctl`) gebruiken. In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer wordt uitgevoerd in alle Service Fabric-clusters en is toegankelijk vanuit een browser door te bladeren naar de HTTP-beheer poort van het cluster (19080). Bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in uw cluster. Bijvoorbeeld `https://localhost:19080`.
2. Selecteer het beletsel teken (**...**) naast het knoop punt **Fabric:/stem/VotingWeb** in de structuur weergave en selecteer **service schalen**.

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
