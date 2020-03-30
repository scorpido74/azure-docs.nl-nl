---
title: Maak een container voor Apache Tomcat op Linux
description: Maak Linux-container om een toepassing te blootstellen die wordt uitgevoerd op de Apache Tomcat-server op Azure Service Fabric. Bouw een Docker-afbeelding met uw toepassing en Apache Tomcat-server, duw de afbeelding naar een containerregister, bouw en implementeer een Service Fabric-containertoepassing.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614414"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Service Fabric-container met Apache Tomcat-server op Linux
Apache Tomcat is een populaire, open-source implementatie van de Java Servlet en Java Server technologieën. In dit artikel ziet u hoe u een container bouwt met Apache Tomcat en een eenvoudige webtoepassing, de container implementeert op een Service Fabric-cluster met Linux en verbinding maakt met de webtoepassing.  

Zie de [Apache Tomcat-homepage voor](https://tomcat.apache.org/)meer informatie over Apache Tomcat. 

## <a name="prerequisites"></a>Vereisten
* Een ontwikkelcomputer waarop wordt uitgevoerd:
  * [Service Fabric SDK en hulpprogramma's](service-fabric-get-started-linux.md).
  * [Docker CE voor Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric-CLI](service-fabric-cli.md)

* Een containerregister in Azure Container Registry. U een containerregister maken in uw Azure-abonnement met behulp van [de Azure-portal](../container-registry/container-registry-get-started-portal.md) of [de Azure CLI.](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry) 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Een Tomcat-afbeelding maken en lokaal uitvoeren
Volg de stappen in deze sectie om een Docker-afbeelding te maken op basis van een Apache Tomcat-afbeelding en een eenvoudige web-app en voer deze vervolgens uit in een container op uw lokale systeem. 
 
1. Clone the [Service Fabric aan de slag met Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) samples repository op je ontwikkelcomputer.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Mappen wijzigen in de Apache Tomcat-serversampledirectory *(service-fabric-java-getting-started/container-apache-tomcat-web-server-sample):*

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Maak een Docker-bestand op basis van de officiële [Tomcat-afbeelding](https://hub.docker.com/_/tomcat/) op Docker Hub en het voorbeeld van de Tomcat-server. Maak in de *map service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* een bestand met de naam *Dockerfile* (zonder bestandsextensie). Voeg het volgende toe aan het bestand *Dockerfile* en sla de wijzigingen op:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zie de [dockerfile-referentie](https://docs.docker.com/engine/reference/builder/) voor meer informatie.


4. Voer `docker build` de opdracht uit om de afbeelding te maken waarop uw webtoepassing wordt uitgevoerd:

   ```bash
   docker build . -t tomcattest
   ```

   Met deze opdracht wordt de nieuwe afbeelding gemaakt met behulp van de `tomcattest`instructies in het Dockerfile, waarbij de afbeelding wordt vernoemen (-t-tagging). Om een containerafbeelding te maken, wordt de basisafbeelding eerst gedownload van Docker Hub en wordt de toepassing eraan toegevoegd. 

   Nadat de buildopdracht is voltooid, voert u de opdracht `docker images` uit om de gegevens van de nieuwe installatiekopie te bekijken:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Controleer of uw containertoepassing lokaal wordt uitgevoerd voordat u het containerregister invoert:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`noemt de container, zodat u er naar verwijzen met een vriendelijke naam in plaats van de ID.
   * `-p`hiermee wordt de poorttoewijzing tussen de container en het hostbesturingssysteem opgegeven. 

   > [!Note]
   > De poort die `-p` u met de parameter opent, moet de poort zijn waarop uw Tomcat-toepassing naar aanvragen luistert. In het huidige voorbeeld is er een connector geconfigureerd in het *bestand ApacheTomcat/conf/server.xml* om te luisteren op poort 8080 voor HTTP-aanvragen. Deze poort is toegewezen aan poort 8080 op de host. 

   Zie de [docker-rundocumentatie](https://docs.docker.com/engine/reference/commandline/run/)voor meer informatie over andere parameters.

1. Als u uw container wilt testen, opent u een browser en voert u een van de volgende URL's in. U ziet een variant van de "Hello World!" welkomstscherm voor elke URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hallo wereld /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Stop de container en verwijder deze van uw ontwikkelingscomputer:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>De Afbeelding Van Tomcat naar uw containerregister duwen
Nu u hebt geverifieerd dat de Tomcat-afbeelding in een container op uw ontwikkelingscomputer wordt uitgevoerd, duwt u deze naar een opslagplaats in een containerregister. In dit artikel wordt Azure Container Registry gebruikt om de afbeelding op te slaan, maar met enige wijziging van stappen u elk containerregister gebruiken dat u kiest. In dit artikel wordt aangenomen dat de registernaam *myregistry* is en de volledige registernaam myregistry.azurecr.io. Wijzig deze op de juiste manier voor uw scenario. 

1. U `docker login` zich aanmelden bij uw containerregister met uw [registerreferenties.](../container-registry/container-registry-authentication.md)

   In het volgende voorbeeld worden de id en het wachtwoord van een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) van Azure Active Directory doorgegeven. U hebt bijvoorbeeld een service-principal aan uw register toegewezen voor een automatiseringsscenario. U zich ook aanmelden met uw registergebruikersnaam en wachtwoord.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Met de volgende opdracht maakt u een tag (of alias) van de installatiekopie met een volledig gekwalificeerd pad naar uw register. In dit voorbeeld wordt de installatiekopie in de naamruimte `samples` geplaatst om overbodige items in de hoofdmap van het register te voorkomen.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. De installatiekopie naar het containerregister pushen:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>De containertoepassing Service Fabric bouwen en implementeren
Nu u de Tomcat-afbeelding naar een containerregister hebt gepusht, u een Containertoepassing ServiceFabric maken en implementeren die de Tomcat-afbeelding uit uw register haalt en deze uitvoert als een containerservice in uw cluster. 

1. Maak een nieuwe map buiten uw lokale kloon (buiten de *service-fabric-java-getting-started* directory tree). Schakel ernaar en gebruik Yeoman om een steiger te maken voor een containertoepassing: 

   ```bash
   yo azuresfcontainer 
   ```
   Voer de volgende waarden in wanneer daarom wordt gevraagd:

   * Uw toepassing een naam geven: ServiceFabricTomcat
   * Naam van de applicatieservice: TomcatService
   * Voer de naam van de afbeelding in: geef de URL op voor de containerafbeelding in uw containerregister; myregistry.azurecr.io/samples/tomcattest bijvoorbeeld.
   * Opdrachten: Laat dit leeg. Omdat voor deze installatiekopie een workloadinvoerpunt is gedefinieerd, hoeft u niet expliciet invoeropdrachten op te geven (opdrachten worden uitgevoerd in de container, zodat de container na het opstarten actief blijft).
   * Aantal exemplaren van de toepassing van gastcontainers: 1

   ![Service Fabric Yeoman-generator voor containers](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Voeg in het servicemanifest *(ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml)* de volgende XML toe onder de **hoofdtag van ServiceManfest** om de poort te openen waarop uw toepassing naar aanvragen luistert. De **tag Eindpunt** declareert het protocol en de poort voor het eindpunt. Voor dit artikel luistert de containerservice op poort 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Voeg in het toepassingsmanifest *(ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml)* onder de **tag ServiceManifestImport** de volgende XML toe. Vervang de **accountnaam** en **het wachtwoord** in de **tag RepositoryCredentials** door de naam van uw containerregister en het wachtwoord dat nodig is om u aan te melden.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   De tag **ContainerHostPolicies** geeft beleid op voor het activeren van containerhosts.
    
   * Met de tag PortBinding configureert u het toewijzingsbeleid voor containerpoort-naar-host.The **PortBinding** tag configures the container port-to-host port mapping policy. Het kenmerk **ContainerPort** is ingesteld op 8080 omdat de container poort 8080 blootlegt, zoals opgegeven in het Dockerfile. Het kenmerk **EndpointRef** is ingesteld op 'endpointTest', het eindpunt dat in het servicemanifest in de vorige stap is gedefinieerd. Zo worden inkomende aanvragen voor de service op poort 8080 toegewezen aan poort 8080 op de container. 
   * De **tag RepositoryCredentials** geeft de referenties op die de container moet verifiëren met de (privé)opslagplaats waar de afbeelding vandaan wordt gehaald. U hebt dit beleid niet nodig als de afbeelding uit een openbare opslagplaats wordt gehaald.
    

12. Maak in de map *ServiceFabricTomcat* verbinding met uw cluster van servicefabric. 

   * Voer het als nodig op om verbinding te maken met het lokale cluster Service Fabric:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Als u verbinding wilt maken met een beveiligd Azure-cluster, controleert u of het clientcertificaat aanwezig is als een .pem-bestand in de *serviceFabricTomcat-map* en voert u het uitvoeren uit: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Vervang in de vorige `your-certificate.pem` opdracht de naam van uw clientcertificaatbestand. In ontwikkel- en testomgevingen wordt het clustercertificaat vaak gebruikt als clientcertificaat. Als uw certificaat niet zelf is `-no-verify` ondertekend, laat u de parameter weg. 
       
     Clustercertificaten worden meestal lokaal gedownload als .pfx-bestanden. Als u uw certificaat nog niet in PEM-indeling hebt, u de volgende opdracht uitvoeren om een .pem-bestand te maken vanuit een .pfx-bestand:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Als uw .pfx-bestand niet `-passin pass:` met een wachtwoord is beveiligd, gebruikt u de laatste parameter.


13. Voer het installatiescript uit dat in de sjabloon is opgenomen om de toepassing naar uw cluster te implementeren. Het script kopieert het toepassingspakket naar het afbeeldingsarchief van het cluster, registreert het toepassingstype en maakt een instantie van de toepassing.

     ```bash
     ./install.sh
     ```

   Nadat u het installatiescript hebt uitgevoerd, opent u een browser en navigeert u naar Service Fabric Explorer:
    
   * Gebruik op een `http://localhost:19080/Explorer` lokaal cluster (vervang *localhost* door het privé-IP van de VM als u Vagrant op Mac OS X gebruikt).
   * Gebruik op `https://PublicIPorFQDN:19080/Explorer`een beveiligd Azure-cluster . 
    
   Vouw het knooppunt **Toepassingen** uit en houd er rekening mee dat er nu een vermelding is voor uw toepassingstype, **ServiceFabricTomcatType**en een andere voor de eerste instantie van dat type. Het kan enkele minuten duren voordat de toepassing volledig is geïmplementeerd, dus wees geduldig.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Als u toegang wilt krijgen tot de toepassing op de Tomcat-server, opent u een browservenster en voert u een van de volgende URL's in. Als u bent geïmplementeerd in het lokale cluster, gebruikt u *localhost* voor *PublicIPorFQDN*. U ziet een variant van de "Hello World!" welkomstscherm voor elke URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Opruimen
Gebruik het script voor het verwijderen in de sjabloon om de toepassingsinstantie uit uw cluster te verwijderen en het toepassingstype uit te schrijven.

```bash
./uninstall.sh
```

Nadat u de installatiekopie naar het containerregister hebt gepusht, kunt u de lokale installatiekopie op de ontwikkelcomputer verwijderen:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Volgende stappen
* Lees [Uw eerste Service Fabric-containertoepassing maken op Linux](service-fabric-get-started-containers-linux.md)voor snelle stappen met extra Linux-containerfuncties.
* Voor meer gedetailleerde stappen over Linux-containers, lees de zelfstudie van de [zelfstudie van de Linux-container-app](service-fabric-tutorial-create-container-images.md) maken.
* Meer informatie over het uitvoeren van [containers in Service Fabric](service-fabric-containers-overview.md).


