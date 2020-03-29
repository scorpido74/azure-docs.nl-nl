---
title: Azure-servicefabric Java-clientAPI's
description: Api's van Service Fabric Java-client genereren en gebruiken met behulp van REST API-specificatie van Service Fabric-client
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451672"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure-servicefabric Java-clientAPI's

Api's van Service Fabric-client maken het mogelijk om op microservices gebaseerde toepassingen en containers te implementeren en te beheren in een Service Fabric-cluster op Azure, on-premises, op lokale ontwikkelingsmachine of in andere cloud. In dit artikel wordt beschreven hoe u API's van Service Fabric Java-client genereren en gebruiken bovenop de REST-API's van de Service Fabric-client

## <a name="generate-the-client-code-using-autorest"></a>De clientcode genereren met AutoRest

[AutoRest](https://github.com/Azure/autorest) is een tool die clientbibliotheken genereert voor toegang tot RESTful-webservices. Invoer naar AutoRest is een specificatie die de REST-API beschrijft met behulp van de OpenAPI-specificatie-indeling. [Service Fabric client REST API's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) volgen deze specificatie .

Volg de onderstaande stappen om Service Fabric Java-clientcode te genereren met behulp van de AutoRest-tool.

1. nodejs en NPM installeren op uw computer

    Als u linux gebruikt dan:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Als u Mac OS X gebruikt, gaat het dan als nog niet het:
    ```bash
    brew install node
    ```

2. Installeer AutoRest met Behulp van NPM.
    ```bash
    npm install -g autorest
    ```

3. Fork en kloon [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) repository in uw lokale machine en ga naar de gekloonde locatie vanaf de terminal van uw machine.


4. Ga naar de locatie hieronder in uw gekloonde repo.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Als uw clusterversie geen 6.0.* is, gaat u naar de juiste map in de stabiele map.
    >   

5. Voer de volgende opdracht autorest uit om de java-clientcode te genereren.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Hieronder is een voorbeeld dat het gebruik van autorest aantoont.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Met de ``servicefabric.json`` volgende opdracht wordt specificatiebestand als ``java-rest-api-     code`` invoer opgenomen en ``servicefabricrest`` wordt java-clientcode in de map gegenereerd en wordt de code in naamruimte ingesloten. Na deze stap vindt u ``models`` ``implementation`` twee mappen ``ServiceFabricClientAPIs.java`` ``package-info.java`` en twee ``java-rest-api-code`` bestanden en gegenereerd in de map.


## <a name="include-and-use-the-generated-client-in-your-project"></a>De gegenereerde client opnemen en gebruiken in uw project

1. Voeg de gegenereerde code op de juiste manier toe aan uw project. We raden u aan een bibliotheek te maken met de gegenereerde code en deze bibliotheek op te nemen in uw project.
2. Als u een bibliotheek maakt, neemt u de volgende afhankelijkheid op in het project van uw bibliotheek. Als u een andere aanpak volgt, moet u de afhankelijkheid op de juiste manier opnemen.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Als u bijvoorbeeld een Maven-buildsysteem gebruikt, ``pom.xml`` neemt u het volgende in uw bestand op:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Een RestClient maken met de volgende code:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Gebruik het clientobject en maak de juiste gesprekken indien nodig. Hier volgen enkele voorbeelden die het gebruik van clientobject aantonen. We gaan ervan uit dat het toepassingspakket is ingebouwd en geüpload naar de image store voordat u de onderstaande API's gebruikt.
    * Een aanvraag indienen
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Een app maken

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Inzicht in de gegenereerde code
Voor elke API vindt u vier overbelasting van de implementatie. Als er optionele parameters dan zou je nog vier variaties, waaronder die optionele parameters te vinden. Denk bijvoorbeeld aan ``removeReplica``de API .
 1. **openbare nietig verwijderenReplica(String nodeName, UUID partitionId, String replicaId, Booleaanse forceRemove, Long timeout)**
    * Dit is de synchrone variant van de removeReplica API-aanroep
 2. **openbare ServiceFuture\<Void> verwijderenReplicaAsync(String nodeName, UUID partitionId, String replicaId, Booleaanse\<forceRemove, Long timeout, final ServiceCallback Void> serviceCallback)**
    * Deze variant van API-aanroep kan worden gebruikt als u op de toekomst gebaseerde asynchrone programmering wilt gebruiken en callbacks wilt gebruiken
 3. **openbare waarneembare\<leegte> replicaasync(String nodeName, UUID partitionId, String replicaId)**
    * Deze variant van API-aanroep kan worden gebruikt als u reactieve asynchrone programmering wilt gebruiken
 4. **public Observable\<\<ServiceResponse Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * Deze variant van API-aanroep kan worden gebruikt als u reactieve asynchrone programmering wilt gebruiken en raw-restrespons wilt verwerken

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [API's voor servicefabricrest](https://docs.microsoft.com/rest/api/servicefabric/)

