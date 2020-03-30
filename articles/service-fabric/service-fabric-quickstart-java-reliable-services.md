---
title: 'Snelstart: een Java-app maken op Azure Service Fabric'
description: In deze snelstart maakt u een Java-toepassing voor Azure met behulp van een voorbeeldtoepassing van de betrouwbare Service Fabric-services.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: fc615149b092aebfdde767fb3b716fb897bfd551
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121501"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Snelstart: Een Java-app implementeren voor Azure Service Fabric op Linux

In deze quickstart implementeert u een Java-toepassing op Azure Service Fabric met behulp van de Eclipse IDE op een Linux-ontwikkelaarsmachine. Wanneer u klaar bent, hebt u een stemtoepassing met een web-front-end in Java die stemresultaten opslaat in een stateful back-endservice in het cluster.

Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers.

## <a name="prerequisites"></a>Vereisten

- [Java-omgeving](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) en [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) en [Eclipse plug-in voor Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK en Command Line Interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
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

    ![Azure Service Fabric Explorer toont gezonde knooppunten](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Open Eclipse.
3. Selecteer **Bestaand** > **Import** > **Gradle-project** **Gradle** > voor bestand importeren en volg de wizard.
4. Selecteer **Directory** en kies de **stemmap** in de **service-fabric-java-quickstart-map** die u hebt gekloond van GitHub. Selecteer **Finish**.

    ![Import Gradle project in Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. U hebt nu het project `Voting` in de pakketverkenner voor Eclipse.
6. Klik met de rechtermuisknop op het project en selecteer **Toepassing publiceren** onder de vervolgkeuzelijst **ServiceFabric.** Kies **PublishProfiles/Local.json** als doelprofiel en selecteer **Publiceren**.

    ![Azure Service Fabric publiceert lokale JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Open uw favoriete webbrowser en open de toepassing op `http://localhost:8080`.

    ![Lokale host Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

U kunt nu een reeks stemmingsopties toevoegen en beginnen met het verzamelen van stemmen. De toepassing wordt uitgevoerd en alle gegevens worden opgeslagen in het Service Fabric-cluster, zonder dat hiervoor een aparte database nodig is.

![Voorbeeld van azure-servicefabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er zijn vele manieren om uw services te schalen. U bijvoorbeeld scripts of opdrachten van Service`sfctl`Fabric CLI (). In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer wordt uitgevoerd in alle Service Fabric-clusters en is toegankelijk vanuit een browser door te bladeren naar de HTTP-beheerpoort van het cluster (19080). Bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in uw cluster. Bijvoorbeeld `https://localhost:19080`.
2. Selecteer de ellips (**...**) naast de **stof:/Voting/VotingWeb-knooppunt** in de treeview en selecteer **Schaalservice**.

    ![Een service schalen in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    U kunt er nu voor kiezen om het aantal exemplaren van de web-front-endservice te schalen.

3. Wijzig het getal in **2** en selecteer **Schaalservice**.
4. Selecteer de **stof:/Voting/VotingWeb-knooppunt** in de structuurweergave en vouw het partitieknooppunt uit (weergegeven door een GUID).

    ![Geschaalde service in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

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
