---
title: Een Apache Spark-taak uitvoeren met Azure Kubernetes service (AKS)
description: Azure Kubernetes service (AKS) gebruiken om een Apache Spark-taak uit te voeren
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 647cb0573922bb53232dbce3f3a7a2557553d47d
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263890"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark taken uitvoeren op AKS

[Apache Spark][apache-spark] is een snelle engine voor grootschalige gegevens verwerking. Vanaf de [Spark 2.3.0-release][spark-latest-release]ondersteunt Apache Spark systeem eigen integratie met Kubernetes-clusters. Azure Kubernetes service (AKS) is een beheerde Kubernetes-omgeving die wordt uitgevoerd in Azure. Dit document bevat informatie over het voorbereiden en uitvoeren van Apache Spark taken op een AKS-cluster (Azure Kubernetes service).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, hebt u het volgende nodig.

* Basis informatie over Kubernetes en [Apache Spark][spark-quickstart].
* [Docker hub][docker-hub] -account of een [Azure container Registry][acr-create].
* Azure CLI is [geïnstalleerd][azure-cli] op uw ontwikkel systeem.
* [Jdk 8][java-install] geïnstalleerd op uw systeem.
* SBT ([scala Build Tool][sbt-install]) dat op uw systeem is geïnstalleerd.
* Git-opdracht regel Programma's die op uw systeem zijn geïnstalleerd.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Spark wordt gebruikt voor grootschalige gegevens verwerking en vereist dat de grootte van de Kubernetes-knoop punten voldoet aan de vereisten van de Spark-resources. We raden een minimale grootte van `Standard_D3_v2` aan voor uw AKS-knoop punten (Azure Kubernetes service).

Als u een AKS-cluster nodig hebt dat voldoet aan deze minimale aanbeveling, voert u de volgende opdrachten uit.

Maak een resource groep voor het cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Maak het AKS-cluster met knoop punten met een grootte `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Maak verbinding met het AKS-cluster.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Als u Azure Container Registry (ACR) gebruikt om container installatie kopieën op te slaan, configureert u verificatie tussen AKS en ACR. Zie de [documentatie voor ACR-verificatie][acr-aks] voor deze stappen.

## <a name="build-the-spark-source"></a>De Spark-bron bouwen

Voordat u Spark-taken uitvoert op een AKS-cluster, moet u de Spark-bron code maken en deze inpakken in een container installatie kopie. De Spark-bron bevat scripts die kunnen worden gebruikt om dit proces te volt ooien.

Kloon de Spark-project opslagplaats naar uw ontwikkel systeem.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Ga naar de map van de gekloonde opslag plaats en sla het pad van de Spark-bron op in een variabele.

```bash
cd spark
sparkdir=$(pwd)
```

Als er meerdere JDK-versies zijn geïnstalleerd, stelt u `JAVA_HOME` in op het gebruik van versie 8 voor de huidige sessie.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Voer de volgende opdracht uit om de Spark-bron code te maken met Kubernetes-ondersteuning.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Met de volgende opdrachten maakt u de Spark-container installatie kopie en pusht u deze naar een container installatie kopie register. Vervang `registry.example.com` door de naam van het container register en `v1` met het label dat u wilt gebruiken. Als docker hub wordt gebruikt, is deze waarde de register naam. Als u Azure Container Registry (ACR) gebruikt, is deze waarde de naam van de ACR-aanmeldings server.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Push de container installatie kopie naar het REGI ster van de container installatie kopie.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Een Spark-taak voorbereiden

Vervolgens moet u een Spark-taak voorbereiden. Een jar-bestand wordt gebruikt om de Spark-taak te bewaren en is nodig om de `spark-submit`-opdracht uit te voeren. Het jar kan toegankelijk worden gemaakt via een open bare URL of vooraf verpakt in een container installatie kopie. In dit voor beeld wordt een jar-voorbeeld gemaakt om de waarde van pi te berekenen. Dit jar wordt vervolgens geüpload naar Azure Storage. Als u een bestaand jar hebt, kunt u vervangen

Maak een map waarin u het project voor een Spark-taak wilt maken.

```bash
mkdir myprojects
cd myprojects
```

Een nieuw scala-project maken op basis van een sjabloon.

```bash
sbt new sbt/scala-seed.g8
```

Wanneer u hierom wordt gevraagd, geeft u `SparkPi` op voor de project naam.

```bash
name [Scala Seed Project]: SparkPi
```

Ga naar de zojuist gemaakte projectmap.

```bash
cd sparkpi
```

Voer de volgende opdrachten uit om een SBT-invoeg toepassing toe te voegen, waarmee het project kan worden ingepakt als jar-bestand.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Voer deze opdrachten uit om de voorbeeld code te kopiëren naar het zojuist gemaakte project en alle benodigde afhankelijkheden toe te voegen.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Als u het project wilt inpakken in een jar, voert u de volgende opdracht uit.

```bash
sbt assembly
```

Na een geslaagde verpakking ziet u uitvoer die lijkt op het volgende.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Taak naar opslag kopiëren

Maak een Azure-opslag account en een container om het jar-bestand te bewaren.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Upload het jar-bestand naar het Azure Storage-account met de volgende opdrachten.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Variabele `jarUrl` bevat nu het openbaar toegankelijke pad naar het jar-bestand.

## <a name="submit-a-spark-job"></a>Een Spark-taak verzenden

Start uitvoeren-proxy in een afzonderlijke opdracht regel met de volgende code.

```bash
kubectl proxy
```

Ga terug naar de hoofdmap van Spark-opslag plaats.

```bash
cd $sparkdir
```

Verzend de taak met behulp van `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Met deze bewerking wordt de Spark-taak gestart, waarmee de taak status naar uw shell-sessie wordt gestreamd. Terwijl de taak wordt uitgevoerd, kunt u de Pod en de uitvoerings tijd van het Spark-stuur programma weer geven met behulp van de opdracht kubectl Get peul. Open een tweede terminal sessie om deze opdrachten uit te voeren.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Terwijl de taak wordt uitgevoerd, kunt u ook toegang krijgen tot de Spark-gebruikers interface. Gebruik in de tweede terminal sessie de opdracht `kubectl port-forward` om toegang te bieden tot Spark-gebruikers interface.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Als u toegang wilt krijgen tot de Spark-gebruikers interface, opent u het adres `127.0.0.1:4040` in een browser.

![Spark-gebruikers interface](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Taak resultaten en logboeken ophalen

Nadat de taak is voltooid, is het stuur programma pod de status voltooid. Haal de naam van de pod op met de volgende opdracht.

```bash
kubectl get pods --show-all
```

Uitvoer:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Gebruik de `kubectl logs`-opdracht om logboeken op te halen uit het Spark-stuur programma pod. Vervang de naam van de pod door de naam van de pod van uw stuur programma.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

In deze logboeken kunt u het resultaat van de Spark-taak zien. Dit is de waarde van pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pakket jar met container installatie kopie

In het bovenstaande voor beeld is het Spark jar-bestand geüpload naar Azure Storage. Een andere mogelijkheid is om het jar-bestand te verpakken in aangepaste docker-installatie kopieën.

Ga hiervoor naar de `dockerfile` voor de Spark-installatie kopie die zich bevindt in de map `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Voeg de instructie am `ADD` toe voor de Spark-taak `jar` ergens tussen `WORKDIR`-en `ENTRYPOINT`-declaraties.

Werk het jar-pad bij naar de locatie van het bestand `SparkPi-assembly-0.1.0-SNAPSHOT.jar` in het ontwikkel systeem. U kunt ook uw eigen aangepaste JAR-bestand gebruiken.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Bouw en push de installatie kopie met de meegeleverde Spark-scripts.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Bij het uitvoeren van de taak, in plaats van een externe Jar-URL aan te duiden, kan het `local://`-schema worden gebruikt met het pad naar het jar-bestand in de docker-installatie kopie.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> In Spark- [documentatie][spark-docs]: ' de Kubernetes scheduler is momenteel experimenteel. In toekomstige versies zijn er mogelijk gedrags wijzigingen in de configuratie, container installatie kopieën en entrypoints ".

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de Spark-documentatie voor meer informatie.

> [!div class="nextstepaction"]
> [Documentatie voor Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
