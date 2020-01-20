---
title: Integreer bestaande MongoDB-toepassing met Azure Cosmos DB-API voor MongoDB en open Service Broker voor Azure (OSBA)
description: In dit artikel leert u hoe u een bestaande Java-en MongoDB-toepassing kunt integreren met de Azure Cosmos DB-API voor MongoDB met behulp van Open Service Broker voor Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Open Service Broker Cosmos DB, open Service Broker voor Azure
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277691"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integreer bestaande MongoDB-toepassing met Azure Cosmos DB-API voor MongoDB en open Service Broker voor Azure (OSBA)

Azure Cosmos DB is een wereldwijd gedistribueerde multimodeldatabaseservice. Het biedt ook wire-protocol compatibiliteit met verschillende NoSQL-Api's, waaronder voor MongoDB. Met de Cosmos DB-API voor MongoDB kunt u Cosmos DB gebruiken met uw bestaande MongoDB-toepassing zonder dat u de database Stuur Programma's of implementatie van uw toepassing hoeft te wijzigen. U kunt ook een Cosmos DB-Service inrichten met behulp van Open Service Broker voor Azure.

In dit artikel maakt u een bestaande Java-toepassing die gebruikmaakt van een MongoDB-data base en deze bijwerkt om een Cosmos DB-Data Base te gebruiken met behulp van Open Service Broker voor Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, moet u:
    
* Er is een [Azure Kubernetes-service cluster](kubernetes-walkthrough.md) gemaakt.
* [Open Service Broker voor Azure geïnstalleerd en geconfigureerd op uw AKS-cluster](integrate-azure.md). 
* Laat de [service CATALOG cli](https://svc-cat.io/docs/install/) geïnstalleerd en geconfigureerd om `svcat`-opdrachten uit te voeren.
* Een bestaande [MongoDb](https://www.mongodb.com/) -data base hebben. U kunt bijvoorbeeld MongoDB uitvoeren op uw [ontwikkel machine](https://docs.mongodb.com/manual/administration/install-community/) of in een [Azure-VM](../virtual-machines/linux/install-mongodb.md).
* Een manier om verbinding te maken met de MongoDB-data base, zoals de [Mongo-shell](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Toepassingscode ophalen
    
In dit artikel gebruikt u de voor [beeld-voorbeeld toepassing van](https://github.com/cloudfoundry-samples/spring-music) de voor grond van Cloud Foundry om een toepassing te demonstreren die gebruikmaakt van een MongoDb-data base.
    
Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>De toepassing voorbereiden voor gebruik van uw MongoDB-data base

De voor beeld-toepassing voor de lente-muziek biedt veel opties voor gegevens bronnen. In dit artikel configureert u dit voor het gebruik van een bestaande MongoDB-data base. 

Voeg de YAML toe die volgt op het einde van *src/main/resources/Application. yml*. Met deze toevoeging maakt u een profiel met de naam *MongoDb* en configureert u een URI en database naam. Vervang de URI door de verbindings gegevens naar uw bestaande MongoDB-data base. Het toevoegen van de URI, die een gebruikers naam en wacht woord bevat, is **alleen voor ontwikkelings** doeleinden en **moet nooit worden toegevoegd aan versie beheer**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Wanneer u uw toepassing start en laat zien dat het *MongoDb* -profiel wordt gebruikt, maakt het verbinding met uw MongoDb-data base en gebruikt deze om de gegevens van de toepassing op te slaan.

Uw toepassing bouwen:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Start uw toepassing en vertel het gebruik van het *MongoDb* -profiel:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigeer naar `http://localhost:8080` in uw browser.

![Spring Music-app met standaardgegevens](media/music-app.png)

U ziet dat de toepassing is gevuld met een aantal [standaard gegevens](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). U kunt hiermee communiceren door enkele bestaande albums te verwijderen en er een paar nieuwe te maken.

U kunt controleren of uw toepassing gebruikmaakt van uw MongoDB-data base door verbinding te maken en de *musicdb* -data base te doorzoeken:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

In het vorige voor beeld wordt de [Mongo-shell](https://docs.mongodb.com/manual/mongo/) gebruikt om verbinding te maken met de MongoDb-data base. U kunt ook controleren of uw wijzigingen blijven bestaan door de toepassing te stoppen, opnieuw op te starten en terug te gaan naar deze in uw browser. U ziet dat de wijzigingen die u hebt aangebracht nog steeds aanwezig zijn.


## <a name="create-a-cosmos-db-database"></a>Een Cosmos DB-database maken

Als u een Cosmos DB-data base in azure wilt maken met behulp van Open Service Broker, gebruikt u de `svcat provision` opdracht:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Met de voor gaande opdracht wordt een Cosmos DB data base in azure ingericht in de resource groep *MyResourceGroup* in de regio *eastus* . Meer informatie over *resourceGroup*, *locatie*en andere Azure-specifieke JSON-para meters is beschikbaar in de [referentie documentatie van de Cosmos db module](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Als u wilt controleren of het inrichten van de database is voltooid, gebruikt u de opdracht `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Uw data base is gereed wanneer u *klaar bent* onder *status*.

Wanneer de inrichting van uw data base is voltooid, moet u de meta gegevens ervan aan een [Kubernetes-geheim](https://kubernetes.io/docs/concepts/configuration/secret/)binden. Andere toepassingen hebben vervolgens toegang tot die gegevens nadat deze zijn gebonden aan een geheim. Gebruik de opdracht `svcat bind` om de meta gegevens van uw data base aan een geheim te koppelen:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>De Cosmos DB-Data Base gebruiken voor uw toepassing

Als u de Cosmos DB Data Base met uw toepassing wilt gebruiken, moet u de URI weten om er verbinding mee te maken. Als u deze informatie wilt ophalen, gebruikt u de opdracht `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Met de voor gaande opdracht wordt het *musicdb* -geheim opgehaald en wordt alleen de URI weer gegeven. Geheimen worden opgeslagen in Base64-indeling, dus de voor gaande opdracht wordt ook gedecodeerd.

Gebruik de URI van de Cosmos DB-Data Base, werk *src/main/resources/Application. yml* uit:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Het bijwerken van de URI, die een gebruikers naam en wacht woord bevat, rechtstreeks naar dit bestand is **alleen voor ontwikkelings** doeleinden en **moet nooit worden toegevoegd aan versie beheer**.

Bouw en start uw toepassing opnieuw om te beginnen met het gebruik van de Cosmos DB Data Base:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

U ziet dat uw toepassing nog steeds gebruikmaakt van het *MongoDb* -profiel en een URI die begint met *MongoDb://* om verbinding te maken met de Cosmos DB-Data Base. De [Azure Cosmos DB-API voor MongoDb](../cosmos-db/mongodb-introduction.md) biedt deze compatibiliteit. Hiermee kan uw toepassing blijven werken alsof deze gebruikmaakt van een MongoDB-data base, maar in feite Cosmos DB.

Navigeer naar `http://localhost:8080` in uw browser. U ziet dat de standaard gegevens zijn hersteld. U kunt hiermee communiceren door enkele bestaande albums te verwijderen en er een paar nieuwe te maken. U kunt controleren of uw wijzigingen blijven bestaan door de toepassing te stoppen, opnieuw op te starten en terug te gaan naar deze in uw browser. U ziet dat de wijzigingen die u hebt aangebracht nog steeds aanwezig zijn. De wijzigingen worden opgeslagen in de Cosmos DB die u hebt gemaakt met behulp van Open Service Broker voor Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Uw toepassing uitvoeren op uw AKS-cluster

U kunt [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) gebruiken om de toepassing te implementeren in uw AKS-cluster. Met Azure dev Spaces kunt u artefacten genereren, zoals Dockerfiles-en helm-grafieken, en een toepassing implementeren en uitvoeren in AKS.

Azure dev-ruimten in uw AKS-cluster inschakelen:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Gebruik de Azure dev Spaces-hulp middelen om uw toepassing voor te bereiden voor uitvoering in AKS:

```cmd
azds prep --public
```

Met deze opdracht worden verschillende artefacten gegenereerd, waaronder een grafiek */* map, die uw helm-diagram is, in de hoofdmap van het project. Met deze opdracht kan geen *Dockerfile* voor dit specifieke project worden gegenereerd, zodat u deze kunt maken.

Maak een bestand in de hoofdmap van het project met de naam *Dockerfile* met deze inhoud:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Bovendien moet u de eigenschap *configurations. develope. build* bijwerken in *azds. yaml* op *False*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

U moet ook het kenmerk *containerPort* bijwerken naar *8080* in *Charts/Spring-Music/templates/Deployment. yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Uw toepassing implementeren naar AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navigeer naar de URL die wordt weer gegeven in de logboeken. In het voor gaande voor beeld gebruikt u *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Controleer of de toepassing samen met uw wijzigingen wordt weer geven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een bestaande toepassing kunt bijwerken met behulp van Cosmos DB MongoDB-API voor MongoDB. In dit artikel wordt ook beschreven hoe u een Cosmos DB-Service inricht met behulp van Open Service Broker voor Azure en die toepassing implementeert voor AKS met Azure dev Spaces.

Voor meer informatie over Cosmos DB, opent u Service Broker voor Azure en Azure dev Spaces, zie:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Service Broker openen voor Azure](https://osba.sh)
* [Ontwikkelen met ontwikkel ruimten](../dev-spaces/azure-dev-spaces.md)
