---
title: Bestaande MongoDB-toepassing integreren met Azure Cosmos DB API voor MongoDB en Open Service Broker voor Azure (OSBA)
description: In dit artikel leert u hoe u een bestaande Java- en MongoDB-toepassing integreert met de Azure Cosmos DB API voor MongoDB met Open Service Broker voor Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker voor Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247925"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Bestaande MongoDB-toepassing integreren met Azure Cosmos DB API voor MongoDB en Open Service Broker voor Azure (OSBA)

Azure Cosmos DB is een wereldwijd gedistribueerde, multi-model databaseservice. Het biedt ook draadprotocol compatibiliteit met verschillende NoSQL API's, waaronder voor MongoDB. Met de Cosmos DB API voor MongoDB u Cosmos DB gebruiken met uw bestaande MongoDB-toepassing zonder dat u de databasestuurprogramma's of implementatie van uw toepassing hoeft te wijzigen. U ook een Cosmos DB-service inrichten met Open Service Broker voor Azure.

In dit artikel neemt u een bestaande Java-toepassing die een MongoDB-database gebruikt en deze bijwerkt om een Cosmos DB-database te gebruiken met Open Service Broker voor Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, moet u:
    
* Laat een [Azure Kubernetes Service-cluster](kubernetes-walkthrough.md) maken.
* [Open Service Broker voor Azure laten installeren en configureren op uw AKS-cluster.](integrate-azure.md) 
* Laat de [SERVICECatalogus CLI](https://svc-cat.io/docs/install/) installeren `svcat` en configureren om opdrachten uit te voeren.
* Hebben een bestaande [MongoDB](https://www.mongodb.com/) database. U bijvoorbeeld MongoDB laten draaien op uw [ontwikkelingsmachine](https://docs.mongodb.com/manual/administration/install-community/) of in een [Azure VM.](../virtual-machines/linux/install-mongodb.md)
* Heb een manier om verbinding te maken met en query's van de MongoDB database, zoals de [mongo shell](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Toepassingscode ophalen
    
In dit artikel gebruikt u de [bronmuzieksampletoepassing van Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) om een toepassing aan te tonen die een MongoDB-database gebruikt.
    
Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>De toepassing voorbereiden om uw MongoDB-database te gebruiken

De bronmuziek sample applicatie biedt veel opties voor datasources. In dit artikel configureert u het om een bestaande MongoDB-database te gebruiken. 

Voeg de YAML volgende aan het einde van *src / main/resources/application.yml*. Deze toevoeging maakt een profiel genaamd *mongodb* en configureert een URI en database naam. Vervang de URI door de verbindingsgegevens van uw bestaande MongoDB-database. Het toevoegen van de URI, die een gebruikersnaam en wachtwoord bevat, rechtstreeks aan dit bestand is alleen voor **ontwikkelingsgebruik** en **mag nooit worden toegevoegd aan versiebeheer.**

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Wanneer u uw toepassing start en vertelt dat het *mongodb-profiel* moet worden gebruikt, maakt deze verbinding met uw MongoDB-database en gebruikt deze om de gegevens van de toepassing op te slaan.

Ga als het gaat om het bouwen van uw toepassing:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Start uw aanvraag en vertel het om het *mongodb* profiel te gebruiken:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigeer `http://localhost:8080` naar in uw browser.

![Spring Music-app met standaardgegevens](media/music-app.png)

Let op dat de toepassing is gevuld met een aantal [standaardgegevens](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Communiceer ermee door een paar bestaande albums te verwijderen en een paar nieuwe albums te maken.

U controleren of uw toepassing uw MongoDB-database gebruikt door er verbinding mee te maken en de *musicdb-database* op te vragen:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

In het voorgaande voorbeeld wordt de [mongo-shell](https://docs.mongodb.com/manual/mongo/) gebruikt om verbinding te maken met de MongoDB-database en deze op te vragen. U ook controleren of uw wijzigingen worden gehandhaafd door uw toepassing te stoppen, opnieuw op te starten en terug te navigeren naar de toepassing in uw browser. Let op de wijzigingen die u hebt aangebracht zijn er nog steeds.


## <a name="create-a-cosmos-db-database"></a>Een Cosmos DB-database maken

Als u een Cosmos DB-database in Azure `svcat provision` wilt maken met Open Service Broker, gebruikt u de opdracht:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

De vorige opdracht bevat een Cosmos DB-database in Azure in de resourcegroep *MyResourceGroup* in de *eastus-regio.* Meer informatie over *resourceGroep,* *locatie*en andere Azure-specifieke JSON-parameters is beschikbaar in de referentiedocumentatie van de [Cosmos DB-module](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Als u wilt controleren of het inrichten van de database is voltooid, gebruikt u de opdracht `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Uw database is gereed wanneer u *Gereed* onder *STATUS ziet.*

Zodra uw database is voltooid provisioning, moet u de metadata te binden aan een [Kubernetes geheim](https://kubernetes.io/docs/concepts/configuration/secret/). Andere toepassingen kunnen dan toegang krijgen tot die gegevens nadat deze zijn gebonden aan een geheim. Als u de metagegevens van uw `svcat bind` database aan een geheim wilt binden, gebruikt u de opdracht:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>De Cosmos DB-database gebruiken met uw toepassing

Als u de Cosmos DB-database met uw toepassing wilt gebruiken, moet u de URI kennen om er verbinding mee te maken. Gebruik de `kubectl get secret` opdracht om deze informatie te krijgen:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

De vorige opdracht krijgt de *musicdb* geheim en geeft alleen de URI. Geheimen worden opgeslagen in base64-formaat, zodat de vorige opdracht ook decodeert.

Met behulp van de URI van de Cosmos DB-database, update *src / main/resources/application.yml* om het te gebruiken:

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

Het bijwerken van de URI, die een gebruikersnaam en wachtwoord bevat, rechtstreeks naar dit bestand is alleen voor **ontwikkelingsgebruik** en **mag nooit worden toegevoegd aan versiebeheer.**

Herbouwen en starten van uw toepassing om te beginnen met het gebruik van de Cosmos DB-database:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Let op uw toepassing maakt nog steeds gebruik van de *mongodb* profiel en een URI die begint met *mongodb://* om verbinding te maken met de Cosmos DB database. De [Azure Cosmos DB API voor MongoDB](../cosmos-db/mongodb-introduction.md) biedt deze compatibiliteit. Het stelt uw toepassing in staat om te blijven werken alsof het een MongoDB-database gebruikt, maar het gebruikt eigenlijk Cosmos DB.

Navigeer `http://localhost:8080` naar in uw browser. Let op dat de standaardgegevens zijn hersteld. Communiceer ermee door een paar bestaande albums te verwijderen en een paar nieuwe albums te maken. U controleren of uw wijzigingen worden gehandhaafd door uw toepassing te stoppen, opnieuw op te starten en terug te navigeren naar de toepassing in uw browser. Let op de wijzigingen die u hebt aangebracht zijn er nog steeds. De wijzigingen blijven bestaan in de Cosmos DB die u hebt gemaakt met Open Service Broker voor Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Uw toepassing uitvoeren op uw AKS-cluster

U [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) gebruiken om de toepassing te implementeren in uw AKS-cluster. Azure Dev Spaces helpt u bij het genereren van artefacten, zoals Dockerfiles en Helm-diagrammen, en een toepassing implementeren en uitvoeren in AKS.

Ga als eerste voor het inschakelen van Azure Dev Spaces in uw AKS-cluster:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Gebruik de hulpprogramma's Azure Dev Spaces om uw toepassing voor te bereiden op het uitvoeren in AKS:

```cmd
azds prep --public
```

Deze opdracht genereert verschillende artefacten, waaronder een *grafieken/* map, dat is uw Helm grafiek, aan de wortel van het project. Met deze opdracht kan geen *Dockerfile* voor dit specifieke project worden gegenereerd, dus u moet het maken.

Maak een bestand aan de hoofdmap van uw project met de naam *Dockerfile* met deze inhoud:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Bovendien moet u de *eigenschap configurations.develop.build* in *azds.yaml* bijwerken naar *false:*
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

U moet ook het kenmerk *containerPort* bijwerken naar *8080* in *grafieken/lentemuziek/sjablonen/deployment.yaml:*

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

Ga als een te meer met de toepassing naar AKS:

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

Navigeer naar de URL die in de logboeken wordt weergegeven. In het voorgaande voorbeeld *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* gebruikt u . 

Controleer of u de toepassing samen met uw wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een bestaande toepassing bijwerken van het gebruik van MongoDB tot het gebruik van Cosmos DB API voor MongoDB. In dit artikel wordt ook gedeald over het inrichten van een Cosmos DB-service met Open Service Broker voor Azure en het implementeren van die toepassing naar AKS met Azure Dev Spaces.

Zie voor meer informatie over Cosmos DB, Open Service Broker voor Azure en Azure Dev Spaces:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker voor Azure](https://osba.sh)
* [Ontwikkelen met Dev Spaces](../dev-spaces/azure-dev-spaces.md)
