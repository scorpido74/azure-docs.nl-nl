---
title: Caffe gebruiken in Azure HDInsight Spark voor gedistribueerde diep gaande lessen
description: Gebruik Caffe op Apache Spark voor gedistribueerd diepe learning in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: e5988bf1955502d89cc31bcc30672de983a399ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083347"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Caffe gebruiken in Azure HDInsight Spark voor gedistribueerde diep gaande lessen

## <a name="introduction"></a>Inleiding

Grondige kennis is van invloed op alles van de gezondheids zorg tot het Trans Port naar productie, en nog veel meer. Bedrijven gaan dieper leren om harde problemen op te lossen, zoals [afbeeldings classificatie](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [spraak herkenning](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), object herkenning en automatische vertaling.

Er zijn [veel populaire Frameworks](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), waaronder [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [tensor flow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, etc. [Caffe](https://caffe.berkeleyvision.org/) is een van de meest beroemde niet-symbolische (dwingende) Neural-netwerk frameworks en veel gebruikt op veel gebieden, waaronder computer vision. Daarnaast combineert [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark) Caffe met Apache Spark, in welk geval een diep gaande leer gemak eenvoudig kan worden gebruikt op een bestaand Hadoop-cluster. U kunt dieper leren gebruiken in combi natie met Spark ETL-pijp lijnen, de systeem complexiteit verminderen en latentie voor het leren van de volledige oplossing.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) is een Cloud Apache Hadoop aanbieding die geoptimaliseerde open-source analyse clusters biedt voor Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka en ml Services. HDInsight wordt ondersteund door een SLA van 99,9%. Al deze big data technologieën en ISV-toepassingen zijn eenvoudig te implementeren als beheerde clusters met beveiliging en bewaking voor ondernemingen.

In dit artikel wordt beschreven hoe u [Caffe op Spark](https://github.com/yahoo/CaffeOnSpark) installeert voor een HDInsight-cluster. In dit artikel wordt ook gebruikgemaakt van de ingebouwde MNIST-demo om te laten zien hoe u gedistribueerde diep gaande leren gebruiken met HDInsight Spark in Cpu's.

Er zijn vier stappen om de taak uit te voeren:

1. De vereiste afhankelijkheden op alle knoop punten installeren
2. Caffe bouwen op Spark for HDInsight op het hoofd knooppunt
3. De vereiste bibliotheken naar alle worker-knoop punten distribueren
4. Stel een Caffe-model op en voer dit op een gedistribueerde manier uit.

Aangezien HDInsight een PaaS-oplossing is, biedt het fantastische platform functies, zodat het eenvoudig is om sommige taken uit te voeren. Een van de functies die in dit blog bericht worden gebruikt, wordt [script actie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)genoemd, waarmee u shell opdrachten kunt uitvoeren om cluster knooppunten (hoofd knooppunt, Worker-knoop punt of Edge-knoop punt) aan te passen.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Stap 1: de vereiste afhankelijkheden installeren op alle knoop punten

Om aan de slag te gaan, moet u de afhankelijkheden installeren. De Caffe-site en de [CaffeOnSpark-site](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) biedt een nuttige wiki voor het installeren van de afhankelijkheden voor Spark in de garen modus. HDInsight gebruikt ook Spark in de garen modus. U moet echter nog enkele afhankelijkheden toevoegen voor het HDInsight-platform. Hiervoor gebruikt u een script actie en voert u deze uit op alle hoofd knooppunten en worker-knoop punten. Deze script actie duurt ongeveer 20 minuten, omdat deze afhankelijkheden ook afhankelijk zijn van andere pakketten. U moet deze op een locatie die toegankelijk is voor uw HDInsight-cluster plaatsen, zoals een GitHub-locatie of het standaard-BLOB Storage-account.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"

Er zijn twee stappen in de script actie. De eerste stap bestaat uit het installeren van alle vereiste bibliotheken. Deze bibliotheken bevatten de benodigde bibliotheken voor zowel het compileren van Caffe (zoals gflags, glog) als het uitvoeren van Caffe (zoals numpy). u gebruikt libatlas voor CPU-optimalisatie, maar u kunt altijd de CaffeOnSpark-wiki volgen op het installeren van andere optimalisatie Bibliotheken, zoals MKL of CUDA (voor GPU).

De tweede stap is het downloaden, compileren en installeren van protobuf 2.5.0 voor Caffe tijdens runtime. Protobuf 2.5.0 [is vereist](https://github.com/yahoo/CaffeOnSpark/issues/87), maar deze versie is niet beschikbaar als een pakket op Ubuntu 16. Daarom moet u het compileren vanuit de bron code. Er zijn ook enkele bronnen op internet over hoe u deze kunt compileren. Zie [hier](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html)voor meer informatie.

Om aan de slag te gaan, kunt u deze script actie uitvoeren op uw cluster voor alle worker-knoop punten en hoofd knooppunten (voor HDInsight 3,5). U kunt de script acties uitvoeren op een bestaand cluster of script acties gebruiken tijdens het maken van het cluster. Zie [de documentatie voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)meer informatie over de script acties.

![Script acties voor het installeren van afhankelijkheden](./media/apache-spark-deep-learning-caffe/submit-script-action.png)

## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Stap 2: Caffe bouwen op Apache Spark voor HDInsight op het hoofd knooppunt

De tweede stap bestaat uit het bouwen van Caffe op de hoofd knooppunt en vervolgens de gecompileerde bibliotheken distribueren naar alle worker-knoop punten. In deze stap moet u [ssh in uw hoofd knooppunt](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Daarna moet u het [CaffeOnSpark-bouw proces](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)volgen. Hieronder ziet u het script dat u kunt gebruiken om CaffeOnSpark te maken met een paar extra stappen.

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Mogelijk moet u meer doen dan wat de documentatie van CaffeOnSpark vertelt. De wijzigingen zijn:
- Wijzig alleen de CPU en gebruik libatlas voor dit specifieke doel.
- Plaats de gegevens sets in de BLOB-opslag. Dit is een gedeelde locatie die toegankelijk is voor alle worker-knoop punten voor later gebruik.
- Plaats de gecompileerde Caffe-bibliotheken in BLOB Storage en later kopieert u deze bibliotheken naar alle knoop punten met behulp van script acties om extra compilatie tijd te voor komen.

### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Problemen oplossen: er is een ant-BuildException opgetreden: exec heeft geretourneerd: 2

Wanneer u voor het eerst probeert CaffeOnSpark te maken, wordt er soms

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Reinig de code opslagplaats door schone maken en voer vervolgens build maken uit om dit probleem op te lossen, zolang u de juiste afhankelijkheden hebt.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Problemen oplossen: time-out voor verbinding met maven-opslag plaats

Soms bevat maven een time-outfout voor de verbinding, vergelijkbaar met het volgende code fragment:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

U moet het over enkele minuten opnieuw proberen.

### <a name="troubleshooting-test-failure-for-caffe"></a>Problemen oplossen: test fouten voor Caffe

Er wordt waarschijnlijk een test fout weer geven bij het uitvoeren van de laatste controle op CaffeOnSpark. Dit is waarschijnlijk gerelateerd aan UTF-8-code ring, maar mag niet van invloed zijn op het gebruik van Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Stap 3: de vereiste bibliotheken naar alle worker-knoop punten distribueren

De volgende stap is het distribueren van de bibliotheken (in principe de bibliotheken in CaffeOnSpark/Caffe-Public/Distribute/lib/en CaffeOnSpark/Caffe-distri/Distribute/lib/) naar alle knoop punten. In stap 2 plaatst u deze bibliotheken in BLOB Storage. in deze stap gebruikt u script acties om het naar alle hoofd knooppunten en worker-knoop punten te kopiëren.

U doet dit door een script actie uit te voeren, zoals wordt weer gegeven in het volgende code fragment:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Zorg ervoor dat u moet verwijzen naar de juiste locatie die specifiek is voor uw cluster.)

Omdat u in stap 2 de BLOB-opslag hebt geplaatst, die toegankelijk is voor alle knoop punten, kopieert u deze alleen naar alle knoop punten.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Stap 4: een Caffe model opstellen en dit op een gedistribueerde manier uitvoeren

Caffe wordt geïnstalleerd nadat u de voor gaande stappen hebt uitgevoerd. De volgende stap is het schrijven van een Caffe-model. 

Caffe maakt gebruik van een ' expresse-architectuur ', waarbij u voor het opstellen van een model alleen een configuratie bestand hoeft te definiëren en zonder te hoeven coderen (in de meeste gevallen). Laten we dit eens bekijken. 

Het model dat u traint, is een voorbeeld model voor MNIST training. De MNIST-data base met handgeschreven cijfers bevat een Trainingsset met 60.000-voor beelden en een testset met 10.000-voor beelden. Het is een subset van een grotere set die beschikbaar is via het NIST. De grootte van de cijfers is genormaliseerd en worden gecentreerd weergegeven in een afbeelding met een vaste grootte. CaffeOnSpark heeft een aantal scripts om de gegevensset te downloaden en deze te converteren naar de juiste indeling.

CaffeOnSpark biedt een aantal netwerk topologieën voor MNIST-training. Het is een goed ontwerp van het splitsen van de netwerk architectuur (de topologie van het netwerk) en Optima Lise ring. In dit geval zijn er twee bestanden vereist:

het bestand ' Oplosser ' ($ {CAFFE_ON_SPARK}/data/lenet_memory_solver. prototxt) wordt gebruikt voor het overzien van de optimalisatie en het genereren van parameter updates. Hiermee definieert u bijvoorbeeld of CPU of GPU wordt gebruikt, wat de impuls, het aantal iteraties, enzovoort. Ook wordt gedefinieerd welke neuron-netwerk topologie het programma moet gebruiken (dit is het tweede bestand dat u nodig hebt). Zie [Caffe-documentatie](https://caffe.berkeleyvision.org/tutorial/solver.html)voor meer informatie over Oplosser.

Aangezien u in dit voor beeld CPU gebruikt in plaats van GPU, moet u de laatste regel wijzigen in:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Voor beeld van HDInsight Caffe-configuratie](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

U kunt indien nodig andere regels wijzigen.

In het tweede bestand ($ {CAFFE_ON_SPARK}/data/lenet_memory_train_test. prototxt) wordt gedefinieerd hoe het neuron-netwerk eruitziet en het relevante invoer-en uitvoer bestand. u moet het bestand ook bijwerken om de locatie van de trainings gegevens weer te geven. Wijzig het volgende deel in lenet_memory_train_test. prototxt (u moet verwijzen naar de juiste locatie die specifiek is voor uw cluster):

- Wijzig de "File:/users/mridul/bigml/demodl/mnist_train_lmdb" in "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- Wijzig "File:/users/mridul/bigml/demodl/mnist_test_lmdb/" in "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Voor beeld van HDInsight Caffe-configuratie](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

Raadpleeg de [Caffe-documentatie over MNIST-gegevensset](https://caffe.berkeleyvision.org/gathered/examples/mnist.html) voor meer informatie over het definiëren van het netwerk.

Voor het doel van dit artikel gebruikt u dit MNIST-voor beeld. Voer de volgende opdrachten uit vanaf het hoofd knooppunt:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Met de voor gaande opdracht worden de vereiste bestanden (lenet_memory_solver. prototxt en lenet_memory_train_test. prototxt) gedistribueerd naar elke garen container. Met de opdracht wordt ook het relevante pad van elke Spark-stuur programma/uitvoerder ingesteld op LD_LIBRARY_PATH. LD_LIBRARY_PATH is gedefinieerd in het vorige code fragment en verwijst naar de locatie met CaffeOnSpark-bibliotheken. 

## <a name="monitoring-and-troubleshooting"></a>Bewaking en probleem oplossing

Omdat u gebruikmaakt van de cluster modus garen, in welk geval het Spark-stuur programma wordt gepland voor een wille keurige container (en een wille keurig worker-knoop punt), zou u alleen moeten zien in de-console, zoals:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Als u wilt weten wat er is gebeurd, moet u meestal het logboek van het Spark-stuur programma ophalen. Dit bevat meer informatie. In dit geval moet u naar de gebruikers interface van de GARENs gaan om de relevante GARENs logboeken te vinden. U kunt de GARENs-UI ophalen met behulp van deze URL:

    https://yourclustername.azurehdinsight.net/yarnui

![browser weergave voor Apache garen scheduler](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

U kunt bekijken hoeveel resources zijn toegewezen voor deze specifieke toepassing. U kunt op de koppeling ' Planner ' klikken en u ziet dat er voor deze toepassing negen containers worden uitgevoerd. u vraagt garen om acht uitvoerendeers te leveren en een andere container is voor het proces van een stuur programma. 

![HDI Apache-scheduler-weer gave](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Mogelijk wilt u de logboeken of container logboeken controleren als er fouten zijn opgetreden. Voor de logboeken van Stuur Programma's kunt u op de toepassings-ID in de gebruikers interface van garen klikken en vervolgens op de knop Logboeken klikken. De logboeken van de Stuur Programma's worden naar stderr geschreven.

![Apache-venster in de weer gave browser](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Zo ziet u mogelijk een aantal van de onderstaande fout in de stuur programma-Logboeken, wat aangeeft dat u te veel uitvoerders toewijst.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Soms kan het probleem zich voordoen in plaats van Stuur Programma's. In dit geval moet u de container logboeken controleren. U kunt de container Logboeken altijd ophalen en vervolgens de mislukte container ophalen. U kunt deze fout bijvoorbeeld beantwoorden wanneer u Caffe uitvoert.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

In dit geval moet u de mislukte container-ID ophalen (in het bovenstaande geval is deze container_1485916338528_0008_05_000005). Vervolgens moet u uitvoeren 

    yarn logs -containerId container_1485916338528_0008_03_000005

vanuit de hoofd knooppunt. Nadat de container is mislukt, wordt deze veroorzaakt door het gebruik van de GPU-modus (waarbij u in plaats daarvan de CPU-modus moet gebruiken) in lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.

## <a name="getting-results"></a>Resultaten ophalen

Aangezien u 8 voor uitvoerers toewijst en de netwerk topologie eenvoudig is, duurt het Maxi maal 30 minuten om het resultaat uit te voeren. Vanaf de opdracht regel kunt u zien dat u het model op wasb:///mnist.model plaatst en de resultaten plaatst in een map met de naam wasb:///mnist_features_result.

U kunt de resultaten opvragen door uit te voeren

    hadoop fs -cat hdfs:///mnist_features_result/*

en het resultaat ziet er als volgt uit:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

De SampleID vertegenwoordigt de ID in de MNIST-gegevensset en het label is het nummer dat door het model wordt geïdentificeerd.

## <a name="conclusion"></a>Conclusie

In deze documentatie hebt u geprobeerd CaffeOnSpark te installeren met een eenvoudig voor beeld uit te voeren. HDInsight is een volledig beheerd reken platform voor gedistribueerde Clouds en is de beste plaats voor het uitvoeren van machine learning en geavanceerde analytische workloads voor grote gegevensset, en voor gedistribueerd diep gaande lessen kunt u Caffe in HDInsight Spark gebruiken om dieper te leren werken stappen.

## <a name="seealso"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
