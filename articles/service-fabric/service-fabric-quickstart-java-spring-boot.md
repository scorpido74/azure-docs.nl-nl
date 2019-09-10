---
title: 'Quickstart: Een Spring boot-app maken in azure Service Fabric'
description: In deze snelstart implementeert u een Spring Boot-toepassing voor Azure Service Fabric met behulp van een Spring Boot-voorbeeldtoepassing.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 2aa5879ee3960bd5d26855ac7e7c3e12994ee54e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861343"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Quickstart: Een Java Spring boot-app implementeren op Azure Service Fabric

In deze Quick start ziet u hoe u een Java Spring boot-toepassing implementeert in azure Service Fabric. Azure Service Fabric is een platform voor gedistribueerde systemen voor het implementeren en distribueren van microservices en containers. 

Deze snelstart gebruikt het [Aan de slag](https://spring.io/guides/gs/spring-boot/)-voorbeeld van de Spring-website. Met gebruik van vertrouwde opdrachtregelprogramma’s wordt u in deze snelstart stapsgewijs begeleid bij het implementeren van het Spring Boot-voorbeeld als een Service Fabric-toepassing. Als u klaar bent, werkt het Spring Boot Aan de slag-voorbeeld in Service Fabric.

![Schermafbeelding van de toepassing](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

In deze snelstart leert u de volgende zaken:

* Een Spring Boot-toepassing implementeren in Service Fabric
* De toepassing implementeren in het lokale cluster
* De toepassing uitschalen over meerdere knooppunten
* Failover van de service uitvoeren met geen beschikbaarheid

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. Service Fabric-SDK en Service Fabric CLI (opdrachtregelinterface) installeren

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Git installeren](https://git-scm.com/)
1. Yeoman installeren

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Java-omgeving instellen

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een terminalvenster de volgende opdracht uit om het Spring Boot Aan de slag-voorbeeld te klonen op de lokale computer.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>De Spring Boot-toepassing compileren 
1. Voer in de directory `gs-spring-boot/complete` de onderstaande opdracht uit om de toepassing te compileren 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>De Spring Boot-toepassing inpakken 
1. Voer in de `gs-spring-boot`-map van uw kloon de opdracht `yo azuresfguest` uit. 

1. Voer de volgende details in voor elke prompt.

    ![Yeoman-vermeldingen](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. Maak in de map `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` een bestand met de naam `entryPoint.sh`. Voeg het volgende toe aan het bestand `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. De resource voor **eind punten** toevoegen aan het bestand *GS-Spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest. XML*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    De **ServiceManifest.xml** ziet er nu als volgt uit: 

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

    Het starten van het lokale cluster kan enige tijd duren. Open Service Fabric Explorer op **http://localhost:19080** om te controleren of het cluster volledig naar behoren functioneert. Als de vijf knooppunten in orde zijn, is het lokale cluster actief. 
    
    ![Lokaal cluster is in orde](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Open de map `gs-spring-boot/SpringServiceFabric`.
1. Voer de volgende opdracht uit om verbinding te maken met het lokale cluster.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Voer het `install.sh`-script uit.

    ```bash
    ./install.sh
    ```

1. Open uw favoriete webbrowser om `http://localhost:8080`toegang te krijgen tot de toepassing.

    ![Front-end van de toepassing voor Local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

U hebt nu toegang tot de Spring Boot-toepassing die werd geïmplementeerd in een Service Fabric-cluster.

## <a name="scale-applications-and-services-in-a-cluster"></a>Toepassingen en services voor schalen in een cluster

Services kunnen eenvoudig worden geschaald in een cluster om een wijziging in de belasting voor de services aan te kunnen. U schaalt een service door het aantal exemplaren te wijzigen dat wordt uitgevoerd in het cluster. Er zijn veel manieren waarop u services kunt schalen. U kunt bijvoorbeeld scripts of opdrachten van Service Fabric CLI (sfctl) gebruiken. In de volgende stappen wordt Service Fabric Explorer gebruikt.

Service Fabric Explorer kan worden uitgevoerd in alle Service Fabric-clusters en is toegankelijk door vanuit een browser te bladeren naar de HTTP-beheerpoort (19080) van het cluster, bijvoorbeeld `http://localhost:19080`.

Voer de volgende stappen uit om de web-front-endservice te schalen:

1. Open Service Fabric Explorer in het cluster - bijvoorbeeld: `http://localhost:19080`.
1. Selecteer het beletsel teken ( **...** ) naast het knoop punt **Fabric:/SpringServiceFabric/springgettingstartedt** in de structuur weergave en selecteer **service schalen**.

    ![De Service Fabric Explorer-service schalen](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    U kunt er nu voor kiezen om het aantal exemplaren van de service te schalen.

1. Wijzig het aantal in **3** en selecteer **service schalen**.

    Een alternatieve manier om de service te schalen met behulp van de opdrachtregel gaat als volgt.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Selecteer het knoop punt **Fabric:/SpringServiceFabric/springgettingstartedt** in de structuur weergave en vouw het partitie knooppunt uit (vertegenwoordigd door een GUID).

    ![Schalen van Service Fabric Explorer-service is voltooid](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    De service heeft drie exemplaren en de structuurweergave laat zien op welke knooppunten de exemplaren worden uitgevoerd.

Met deze eenvoudige beheertaak hebt u het aantal beschikbare resources voor het verwerken van gebruikersbelasting voor de front-endservice verdubbeld. Het is belangrijk te weten dat u niet meerdere exemplaren van een service nodig hebt om ervoor te zorgen dat deze op betrouwbare wijze wordt uitgevoerd. Als de service mislukt, wordt in Service Fabric een nieuw exemplaar van de service uitgevoerd in het cluster.

## <a name="fail-over-services-in-a-cluster"></a>Failoverservices in een cluster

Het opnieuw opstarten van een knooppunt kan worden gesimuleerd met behulp van Service Fabric Explorer om failover van de service te demonstreren. Zorg ervoor dat maar één exemplaar van de service wordt uitgevoerd.

1. Open Service Fabric Explorer in het cluster - bijvoorbeeld: `http://localhost:19080`.
1. Selecteer het beletsel teken ( **...** ) naast het knoop punt waarop het exemplaar van uw service wordt uitgevoerd en start het knoop punt opnieuw op.

    ![Knooppunt opnieuw starten in Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. Het service-exemplaar wordt naar een ander knooppunt verplaatst en er treedt geen downtime op voor de toepassing.

    ![Knooppunt opnieuw starten in Service Fabric Explorer is voltooid](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

* Een Spring Boot-toepassing implementeren in Service Fabric
* De toepassing implementeren in het lokale cluster
* De toepassing uitschalen over meerdere knooppunten
* Failover van de service uitvoeren met geen beschikbaarheid

Meer informatie over het werken met Java-apps in Service Fabric vindt u in de zelfstudie voor Java-apps.

> [!div class="nextstepaction"]
> [Een Java-app implementeren](./service-fabric-tutorial-create-java-app.md)
