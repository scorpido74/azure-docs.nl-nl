---
title: 'Snelstart: een app voor springboot maken op Azure Service Fabric'
description: In deze snelstart implementeert u een Spring Boot-toepassing voor Azure Service Fabric met behulp van een Spring Boot-voorbeeldtoepassing.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: eb96989b4a2731e78471b848d690b48352408d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121478"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Snelstart: een Java Spring Boot-app implementeren op Azure Service Fabric

In deze quickstart implementeert u een Java Spring Boot-toepassing naar Azure Service Fabric met behulp van bekende command-line-hulpprogramma's op Linux of MacOS. Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers. 

## <a name="prerequisites"></a>Vereisten

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java-omgeving](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) en [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK & Service Fabric Command Line Interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[Macos](#tab/macos)

- [Java-omgeving en Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK & Service Fabric Command Line Interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een terminalvenster de volgende opdracht uit om de voorbeeld-app [Voor het opstarten van](https://github.com/spring-guides/gs-spring-boot) de veeropstart te klonen naar uw lokale machine.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>De Spring Boot-toepassing compileren 
Voer in de *gs-spring-boot/complete* directory de onderstaande opdracht uit om de toepassing te bouwen 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>De Spring Boot-toepassing inpakken 
1. Voer de opdracht uit in de `yo azuresfguest` *gs-spring-boot* directory in je kloon. 

1. Voer de volgende details in voor elke prompt.

    ![Lente Boot Yeoman inzendingen](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Maak in de map *SpringServiceFabric/SpringServiceFabric/SpringGettingStartPkg/code* een bestand met de naam *entryPoint.sh*. Voeg de volgende code toe aan het *entryPoint.sh* bestand. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Voeg de **bron Eindpunten** toe in het *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartPkg/ServiceManifest.xml-bestand*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    De *ServiceManifest.xml* ziet er nu als volgt uit: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

In dit stadium hebt u een Service Fabric-toepassing voor het Spring Boot Aan de slag-voorbeeld gemaakt die u kunt implementeren in Service Fabric.

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

1. Start het lokale cluster op Ubuntu-computers door de volgende opdracht uit te voeren:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Als u een Mac gebruikt, start u het lokale cluster vanuit de Docker-installatiekopie (hierbij wordt verondersteld dat is voldaan aan de [vereisten](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) voor het instellen van het lokale cluster voor Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Het starten van het lokale cluster kan enige tijd duren. Open Service Fabric Explorer op `http://localhost:19080` om te controleren of het cluster volledig naar behoren functioneert. Als de vijf knooppunten in orde zijn, is het lokale cluster actief. 
    
    ![Service Fabric Explorer toont gezonde knooppunten](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Open de *map GS-Spring-Boot/SpringServiceFabric.*
1. Voer de volgende opdracht uit om verbinding te maken met het lokale cluster.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Voer het *install.sh* script uit.

    ```bash
    ./install.sh
    ```

1. Open uw favoriete webbrowser en open de toepassing op `http://localhost:8080`.

    ![Voorbeeld van Spring Boot Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

U hebt nu toegang tot de Spring Boot-toepassing die werd geïmplementeerd in een Service Fabric-cluster.

Zie voor meer informatie het voorbeeld van Spring Boot [Getting Started](https://spring.io/guides/gs/spring-boot/) op de website van de lente.

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er zijn veel manieren waarop u services kunt schalen. U kunt bijvoorbeeld scripts of opdrachten van Service Fabric CLI (sfctl) gebruiken. In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer kan worden uitgevoerd in alle Service Fabric-clusters en is toegankelijk door vanuit een browser te bladeren naar de HTTP-beheerpoort (19080) van het cluster, bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in het cluster - bijvoorbeeld: `http://localhost:19080`.
1. Selecteer de ellips (**...**) naast de **stof:/SpringServiceFabric/SpringGettingStarted-knooppunt** in de treeview en selecteer **Schaalservice**.

    ![Voorbeeld van de schaalservice van Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    U kunt er nu voor kiezen om het aantal exemplaren van de service te schalen.

1. Wijzig het getal in **3** en selecteer **Schaalservice**.

    Een alternatieve manier om de service te schalen met behulp van de opdrachtregel gaat als volgt.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Selecteer het **knooppunt fabric:/SpringServiceFabric/SpringGettingStarted** in de structuurweergave en vouw het partitieknooppunt uit (weergegeven door een GUID).

    ![Service Fabric Explorer-schaalservice voltooid](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    De service heeft drie exemplaren en de structuurweergave laat zien op welke knooppunten de exemplaren worden uitgevoerd.

Met deze eenvoudige beheertaak hebt u het aantal beschikbare resources voor het verwerken van gebruikersbelasting voor de front-endservice verdubbeld. Het is belangrijk te weten dat u niet meerdere exemplaren van een service nodig hebt om ervoor te zorgen dat deze op betrouwbare wijze wordt uitgevoerd. Als de service mislukt, wordt in Service Fabric een nieuw exemplaar van de service uitgevoerd in het cluster.

## <a name="fail-over-services-in-a-cluster"></a>Failoverservices in een cluster

Het opnieuw opstarten van een knooppunt kan worden gesimuleerd met behulp van Service Fabric Explorer om failover van de service te demonstreren. Zorg ervoor dat maar één exemplaar van de service wordt uitgevoerd.

1. Open Service Fabric Explorer in het cluster - bijvoorbeeld: `http://localhost:19080`.
1. Selecteer de ellips (**...**) naast het knooppunt waarop de instantie van uw service wordt uitgevoerd en Start het knooppunt opnieuw op.

    ![Knooppunt servicefabric explorer opnieuw opstarten](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Het service-exemplaar wordt naar een ander knooppunt verplaatst en er treedt geen downtime op voor de toepassing.

    ![Service Fabric Explorer start knooppunt opnieuw op](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u de volgende zaken geleerd:

* Een Spring Boot-toepassing implementeren in Service Fabric
* De toepassing implementeren in het lokale cluster
* De toepassing op meerdere knooppunten uitschalen
* Failover van de service uitvoeren met geen beschikbaarheid

Meer informatie over het werken met Java-apps in Service Fabric vindt u in de zelfstudie voor Java-apps.

> [!div class="nextstepaction"]
> [Een Java-app implementeren](./service-fabric-tutorial-create-java-app.md)
