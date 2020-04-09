---
title: Een Apache Spark-taak uitvoeren met Azure Kubernetes Service (AKS)
description: Gebruik Azure Kubernetes Service (AKS) om een Apache Spark-taak te maken en uit te voeren voor grootschalige gegevensverwerking.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 2e399c1a7b0f9bbc2aac375fe8af969a2b9e0e48
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877624"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark-taken uitvoeren op AKS

[Apache Spark][apache-spark] is een snelle motor voor grootschalige gegevensverwerking. Vanaf de [Spark 2.3.0-release][spark-latest-release]ondersteunt Apache Spark native integratie met Kubernetes-clusters. Azure Kubernetes Service (AKS) is een beheerde Kubernetes-omgeving die wordt uitgevoerd in Azure. In dit document wordt informatie gegeven over het voorbereiden en uitvoeren van Apache Spark-taken op een AKS-cluster (Azure Kubernetes Service).

## <a name="prerequisites"></a>Vereisten

Om de stappen in dit artikel te voltooien, heb je het volgende nodig.

* Basisbegrip van Kubernetes en [Apache Spark][spark-quickstart].
* [Docker Hub-account][docker-hub] of een [Azure Container Registry][acr-create].
* Azure CLI [geïnstalleerd][azure-cli] op uw ontwikkelingssysteem.
* [JDK 8][java-install] geïnstalleerd op uw systeem.
* SBT ([Scala Build Tool][sbt-install]) geïnstalleerd op uw systeem.
* Git command-line tools geïnstalleerd op uw systeem.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Spark wordt gebruikt voor grootschalige gegevensverwerking en vereist dat Kubernetes-knooppunten zijn aangepast aan de vereisten van spark-bronnen. We raden een `Standard_D3_v2` minimale grootte van voor uw Azure Kubernetes Service (AKS) knooppunten.

Als u een AKS-cluster nodig hebt dat aan deze minimumaanbeveling voldoet, voert u de volgende opdrachten uit.

Maak een resourcegroep voor het cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Maak een serviceprincipal voor het cluster. Nadat deze is gemaakt, hebt u de Service Principal-appId en het wachtwoord nodig voor de volgende opdracht.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Maak het AKS-cluster met `Standard_D3_v2`knooppunten van grootte en waarden van appId en wachtwoord die worden doorgegeven als service-principal en client-secret parameters.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Maak verbinding met het AKS-cluster.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Als u Azure Container Registry (ACR) gebruikt om containerafbeeldingen op te slaan, configureert u verificatie tussen AKS en ACR. Zie de [ACR-verificatiedocumentatie][acr-aks] voor deze stappen.

## <a name="build-the-spark-source"></a>De Spark-bron bouwen

Voordat u Spark-taken uitvoert op een AKS-cluster, moet u de Spark-broncode inbouwen en verpakken in een containerafbeelding. De Spark-bron bevat scripts die kunnen worden gebruikt om dit proces te voltooien.

Kloon de Spark project repository naar uw ontwikkelingssysteem.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Wijzig in de map van de gekloonde opslagplaats en sla het pad van de Spark-bron op in een variabele.

```bash
cd spark
sparkdir=$(pwd)
```

Als u meerdere JDK-versies `JAVA_HOME` hebt geïnstalleerd, stelt u in om versie 8 te gebruiken voor de huidige sessie.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Voer de volgende opdracht uit om de Spark-broncode met Kubernetes-ondersteuning te bouwen.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Met de volgende opdrachten wordt de spark-containerafbeelding gemaakt en wordt deze naar een containerafbeeldingsregister gepusht. Vervang `registry.example.com` door de naam van `v1` uw containerregister en door de tag die u het liefst gebruikt. Als u Docker Hub gebruikt, is deze waarde de registernaam. Als u Azure Container Registry (ACR) gebruikt, is deze waarde de naam van de ACR-inlogserver.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Duw de containerafbeelding naar het containerimageregister.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Een Spark-taak voorbereiden

Bereid vervolgens een Spark-klus voor. Een potbestand wordt gebruikt om de Spark-taak `spark-submit` vast te houden en is nodig bij het uitvoeren van de opdracht. De pot kan toegankelijk worden gemaakt via een openbare URL of voorverpakt in een containerafbeelding. In dit voorbeeld wordt een voorbeeldpot gemaakt om de waarde van Pi te berekenen. Deze pot wordt vervolgens geüpload naar Azure-opslag. Als u een bestaande pot, voel je vrij om te vervangen

Maak een map waarin u het project wilt maken voor een Spark-taak.

```bash
mkdir myprojects
cd myprojects
```

Maak een nieuw Scala-project op basis van een sjabloon.

```bash
sbt new sbt/scala-seed.g8
```

Voer desgevraagd `SparkPi` de projectnaam in.

```bash
name [Scala Seed Project]: SparkPi
```

Navigeer naar de nieuw gemaakte projectmap.

```bash
cd sparkpi
```

Voer de volgende opdrachten uit om een SBT-plug-in toe te voegen, waarmee het project als een potbestand kan worden verpakt.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Voer deze opdrachten uit om de voorbeeldcode naar het nieuw gemaakte project te kopiëren en alle benodigde afhankelijkheden toe te voegen.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Voer de volgende opdracht uit om het project in een pot te verpakken.

```bash
sbt assembly
```

Na een succesvolle verpakking, moet u de output vergelijkbaar met de volgende te zien.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Taak kopiëren naar opslag

Maak een Azure-opslagaccount en -container om het jar-bestand vast te houden.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Upload het jar-bestand naar het Azure-opslagaccount met de volgende opdrachten.

```azurecli
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

Variable `jarUrl` bevat nu het openbaar toegankelijke pad naar het jar-bestand.

## <a name="submit-a-spark-job"></a>Een Spark-taak indienen

Start kube-proxy in een aparte opdrachtregel met de volgende code.

```bash
kubectl proxy
```

Navigeer terug naar de hoofdmap van Spark-opslagplaats.

```bash
cd $sparkdir
```

Maak een serviceaccount met voldoende machtigingen voor het uitvoeren van een taak.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Stuur de `spark-submit`taak in via .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Met deze bewerking wordt de Spark-taak gestart, waarbij de taakstatus wordt doorgestroomd naar uw shell-sessie. Terwijl de taak wordt uitgevoerd, u spark-stuurprogrammapod en executorpods zien met de opdracht kubectl get pods. Open een tweede terminalsessie om deze opdrachten uit te voeren.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Terwijl de taak wordt uitgevoerd, hebt u ook toegang tot de Spark-gebruikersinterface. Gebruik in de tweede `kubectl port-forward` terminalsessie de opdracht Om toegang te geven tot de Spark-gebruikersinterface.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Als u toegang wilt krijgen `127.0.0.1:4040` tot spark-gebruikersinterface, opent u het adres in een browser.

![Gebruikersinterface stimuleren](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Jobresultaten en logboeken verzamelen

Nadat de taak is voltooid, bevindt de chauffeurspod zich in de status 'Voltooid'. Download de naam van de pod met de volgende opdracht.

```bash
kubectl get pods --show-all
```

Uitvoer:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Gebruik `kubectl logs` de opdracht om logboeken van de spark driver pod te krijgen. Vervang de naam van de pod door de naam van uw stuurprogrammapod.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Binnen deze logboeken ziet u het resultaat van de spark-taak, de waarde van Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>De kruik van het pakket met containerbeeld

In het bovenstaande voorbeeld is het Spark-jar-bestand geüpload naar Azure-opslag. Een andere optie is om het jar-bestand te verpakken in op maat gemaakte Docker-afbeeldingen.

Zoek hiervoor de `dockerfile` voor de Spark-afbeelding in `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` de map. Voeg `ADD` een instructie toe `jar` voor `WORKDIR` `ENTRYPOINT` de Spark-taak ergens tussen en declaratie.

Werk het jarpad bij `SparkPi-assembly-0.1.0-SNAPSHOT.jar` naar de locatie van het bestand op uw ontwikkelingssysteem. U ook gebruik maken van uw eigen aangepaste pot bestand.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Bouw en duw de afbeelding met de meegeleverde Spark-scripts.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Bij het uitvoeren van de taak, in `local://` plaats van een externe jar URL aan te geven, kan het schema worden gebruikt met het pad naar het jar-bestand in de Docker-afbeelding.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Van Spark [documentatie][spark-docs]: "De Kubernetes scheduler is momenteel experimenteel. In toekomstige versies kunnen er gedragswijzigingen zijn rond configuratie, containerafbeeldingen en entrypoints".

## <a name="next-steps"></a>Volgende stappen

Bekijk Spark-documentatie voor meer details.

> [!div class="nextstepaction"]
> [Spark-documentatie][spark-docs]

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
