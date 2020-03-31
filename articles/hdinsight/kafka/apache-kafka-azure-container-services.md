---
title: Azure Kubernetes-service gebruiken met Kafka op HDInsight
description: Meer informatie over het gebruik van Kafka op HDInsight van containerafbeeldingen die worden gehost in Azure Kubernetes Service (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 6abb4f632535f1bda7e9f337f111ba372a624f2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239621"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Azure Kubernetes-service gebruiken met Apache Kafka op HDInsight

Meer informatie over het gebruik van Azure Kubernetes Service (AKS) met [Apache Kafka](https://kafka.apache.org/) op het HDInsight-cluster. De stappen in dit document gebruiken een Node.js-toepassing die wordt gehost in AKS om de verbinding met Kafka te verifiëren. Deze applicatie maakt gebruik van het [kafka-node](https://www.npmjs.com/package/kafka-node) pakket om te communiceren met Kafka. Het maakt gebruik [van Socket.io](https://socket.io/) voor event driven messaging tussen de browserclient en de back-end gehost in AKS.

[Apache Kafka](https://kafka.apache.org) is een open-source gedistribueerd streamingplatform dat kan worden gebruikt voor het bouwen van pijplijnen en toepassingen voor realtime streaming van gegevens. Azure Kubernetes Service beheert uw gehoste Kubernetes-omgeving en maakt het snel en eenvoudig om gecontaineriseerde toepassingen te implementeren. Met behulp van een Azure Virtual Network u de twee services verbinden.

> [!NOTE]  
> De focus van dit document ligt op de stappen die nodig zijn om Azure Kubernetes Service in staat te stellen met Kafka te communiceren op HDInsight. Het voorbeeld zelf is slechts een basis-Kafka-client om aan te tonen dat de configuratie werkt.

## <a name="prerequisites"></a>Vereisten

* [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Een Azure-abonnement

In dit document wordt ervan uitgegaan dat u bekend bent met het maken en gebruiken van de volgende Azure-services:

* Kafka op HDInsight
* Azure Kubernetes Service
* Virtuele netwerken van Azure

In dit document wordt er ook van uitgegaan dat u de [zelfstudie van](../../aks/tutorial-kubernetes-prepare-app.md)azure Kubernetes Service hebt doorlopen. In dit artikel wordt een containerservice maakt, wordt een `kubectl` Kubernetes-cluster, een containerregister en wordt het hulpprogramma geconfigureerd.

## <a name="architecture"></a>Architectuur

### <a name="network-topology"></a>Netwerktopologie

Zowel HDInsight als AKS gebruiken een Azure Virtual Network als container voor rekenbronnen. Om communicatie tussen HDInsight en AKS mogelijk te maken, moet u communicatie tussen hun netwerken mogelijk maken. De stappen in dit document maken gebruik van Virtual Network Peering naar de netwerken. Andere verbindingen, zoals VPN, moeten ook werken. Zie het [document Voor virtuele netwerkpeering](../../virtual-network/virtual-network-peering-overview.md) voor meer informatie over peering.

In het volgende diagram wordt de netwerktopologie weergegeven die in dit document wordt gebruikt:

![HDInsight in het ene virtuele netwerk, AKS in een ander, met behulp van peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Naamomzetting is niet ingeschakeld tussen de peered-netwerken, dus IP-adressering wordt gebruikt. Kafka op HDInsight is standaard geconfigureerd om hostnamen terug te sturen in plaats van IP-adressen wanneer clients verbinding maken. De stappen in dit document wijzigen Kafka om in plaats daarvan IP-advertenties te gebruiken.

## <a name="create-an-azure-kubernetes-service-aks"></a>Een Azure Kubernetes-service (AKS) maken

Als u nog geen AKS-cluster hebt, gebruikt u een van de volgende documenten om te leren hoe u er een maakt:

* [Een AKS-cluster (Azure Kubernetes Service) implementeren - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Een AKS-cluster (Azure Kubernetes Service) implementeren - CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS maakt een virtueel netwerk tijdens de installatie in een **extra** resourcegroep. De aanvullende resourcegroep volgt de naamgevingsconventie van **MC_resourceGroup_AKSclusterName_location**.  
> Dit netwerk is gekeken naar het netwerk dat is gemaakt voor HDInsight in de volgende sectie.

## <a name="configure-virtual-network-peering"></a>Virtueel netwerkpeering configureren

### <a name="identify-preliminary-information"></a>Voorlopige informatie identificeren

1. Zoek in de [Azure-portal](https://portal.azure.com)de extra **resourcegroep** die het virtuele netwerk voor uw AKS-cluster bevat.

2. Selecteer in de brongroep de __virtuele netwerkbron.__ Noteer deze naam voor later gebruik.

3. Selecteer **onder Instellingen** __adresruimte__. Let op de vermelde adresruimte.

### <a name="create-virtual-network"></a>Virtueel netwerk maken

1. Als u een virtueel netwerk voor HDInsight wilt maken, navigeert u naar + __Een virtueel netwerknetwerk__ > __Networking__ > voor bronnen__maken.__

1. Maak het netwerk met behulp van de volgende richtlijnen voor bepaalde eigenschappen:

    |Eigenschap | Waarde |
    |---|---|
    |Adresruimte|U moet een adresruimte gebruiken die de adresruimte die door het AKS-clusternetwerk wordt gebruikt, niet overlapt.|
    |Locatie|Gebruik dezelfde __locatie__ voor het virtuele netwerk dat u voor het AKS-cluster hebt gebruikt.|

1. Wacht tot het virtuele netwerk is gemaakt voordat u naar de volgende stap gaat.

### <a name="configure-peering"></a>Peering configureren

1. Als u het peering wilt configureren tussen het HDInsight-netwerk en het AKS-clusternetwerk, selecteert u het virtuele netwerk en selecteert u __Peerings__.

1. Selecteer __+ Voeg toe__ en gebruik de volgende waarden om het formulier in te vullen:

    |Eigenschap |Waarde |
    |---|---|
    |Naam van het \<peering van deze VN-> naar extern virtueel netwerk|Voer een unieke naam in voor deze peeringconfiguratie.|
    |Virtueel netwerk|selecteer het virtuele netwerk voor het **AKS-cluster**.|
    |Naam van de \<peering van \<AKS VN> naar deze VN->|Voer een unieke naam in.|

    Laat alle andere velden achter de standaardwaarde en selecteer __OK__ om de peering te configureren.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Apache Kafka-cluster maken op HDInsight

Wanneer u het kafka-cluster op HDInsight maakt, moet u lid worden van het virtuele netwerk dat eerder is gemaakt voor HDInsight. Zie het [clusterdocument Een Cluster document Maken van een Apache Kafka](apache-kafka-get-started.md) voor meer informatie over het maken van een Kafka-cluster.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP-reclame configureren

Gebruik de volgende stappen om Kafka te configureren om IP-adressen te adverteren in plaats van domeinnamen:

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang CLUSTERNAME door de naam van het cluster Kafka op HDInsight.

    Gebruik de https-gebruikersnaam en -wachtwoord voor het cluster wanneer u daarom wordt gevraagd. De Ambari Web UI voor het cluster wordt weergegeven.

2. Als u informatie over Kafka wilt weergeven, selecteert u __Kafka__ in de lijst aan de linkerkant.

    ![Servicelijst met Kafka gemarkeerd](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Als u de Kafka-configuratie wilt bekijken, selecteert u __Configs__ in het bovenste midden.

    ![Configuratie apache Ambari-services](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Als u de __kafka-env-configuratie__ wilt vinden, voert u `kafka-env` het veld __Filter__ rechtsboven in.

    ![Kafka-configuratie, voor kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Als u Kafka wilt configureren om IP-adressen te adverteren, voegt u de volgende tekst toe aan de onderkant van het veld __kafka-env-sjabloon:__

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Als u de interface wilt configureren `listeners` waarop Kafka luistert, voert u het veld __Filter__ rechtsboven in.

7. Als u Kafka wilt configureren om te luisteren __listeners__ op `PLAINTEXT://0.0.0.0:9092`alle netwerkinterfaces, wijzigt u de waarde in het veld listeners in .

8. Als u de configuratiewijzigingen wilt opslaan, gebruikt u de knop __Opslaan.__ Voer een sms-bericht in waarin de wijzigingen worden beschreven. Selecteer __OK__ zodra de wijzigingen zijn opgeslagen.

    ![Apache Ambari opslaan configuratie](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Als u fouten wilt voorkomen bij het opnieuw starten van Kafka, gebruikt u de knop __Serviceacties__ en selecteert u __Onderhoudsmodus inschakelen__. Selecteer OK om deze bewerking te voltooien.

    ![Serviceacties, waarbij het onderhoud is gemarkeerd](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Als u Kafka opnieuw wilt starten, gebruikt u de knop __Opnieuw starten__ en selecteert u Alle getroffen __opnieuw starten__. Bevestig het opnieuw opstarten en gebruik de knop __OK__ nadat de bewerking is voltooid.

    ![Knop Opnieuw starten met alle getroffen gemarkeerde gemarkeerde gemarkeerde opnieuw starten](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Als u de onderhoudsmodus wilt uitschakelen, gebruikt u de knop __Serviceacties__ en selecteert u __Onderhoudsmodus uitschakelen__. Selecteer **OK** om deze bewerking te voltooien.

## <a name="test-the-configuration"></a>De configuratie testen

Op dit moment zijn Kafka en Azure Kubernetes Service in communicatie via de peered virtuele netwerken. Als u deze verbinding wilt testen, gebruikt u de volgende stappen:

1. Maak een Kafka-onderwerp dat wordt gebruikt door de testtoepassing. Zie het [clusterdocument Een Apache Kafka maken](apache-kafka-get-started.md) voor informatie over het maken van Kafka-onderwerpen.

2. Download de voorbeeldtoepassing van [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test).

3. Bewerk `index.js` het bestand en wijzig de volgende regels:

    * `var topic = 'mytopic'`: `mytopic` Vervang door de naam van het Kafka-onderwerp dat door deze toepassing wordt gebruikt.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Vervang door het interne IP-adres van een van de brokerhosts voor uw cluster.

        Zie het [API-document Apache Ambari REST](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) als u het interne IP-adres van de brokerhosts (workernodes) in het cluster wilt vinden. Kies IP-adres van een van de vermeldingen `wn`waar de domeinnaam begint met .

4. Installeer vanuit een `src` opdrachtregel in de map afhankelijkheden en gebruik Docker om een afbeelding te maken voor implementatie:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pakketten die nodig zijn door deze toepassing worden ingecheckt in `npm` de repository, dus u hoeft het hulpprogramma niet te gebruiken om ze te installeren.

5. Meld u aan bij uw Azure Container Registry (ACR) en zoek de naam loginServer:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Als u de naam van uw Azure Container Registry niet kent of niet bekend bent met het gebruik van de Azure CLI om met de Azure Kubernetes-service te werken, raadpleegt u de [AKS-zelfstudies](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tag de `kafka-aks-test` lokale afbeelding met de loginServer van uw ACR. Voeg `:v1` ook toe aan het einde om de afbeeldingsversie aan te geven:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Duw de afbeelding naar het register:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Deze bewerking duurt enkele minuten.

8. Bewerk het Kubernetes`kafka-aks-test.yaml`manifest-bestand `microsoft` ( ) en vervang de ACR loginServer-naam die in stap 4 is opgehaald.

9. Gebruik de volgende opdracht om de toepassingsinstellingen uit het manifest te implementeren:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Gebruik de volgende opdracht `EXTERNAL-IP` om de toepassing van toepassing te bekijken:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Zodra een extern IP-adres is toegewezen, gebruikt u __CTRL + C__ om het horloge af te sluiten

11. Open een webbrowser en voer het externe IP-adres voor de service in. Je komt op een pagina die lijkt op de volgende afbeelding:

    ![Apache Kafka test webpagina afbeelding](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Voer tekst in het veld in en selecteer de knop __Verzenden.__ De gegevens worden naar Kafka verzonden. Dan leest de kafka-consument in de applicatie het bericht en voegt het toe aan de sectie __Berichten van Kafka.__

    > [!WARNING]  
    > U meerdere exemplaren van een bericht ontvangen. Dit probleem treedt meestal op wanneer u uw browser vernieuwt na het aansluiten of meerdere browserverbindingen met de toepassing opent.

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende koppelingen voor meer informatie over het gebruik van Apache Kafka in HDInsight:

* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)

* [Gebruik MirrorMaker om een replica van Apache Kafka te maken op HDInsight](apache-kafka-mirroring.md)

* [Gebruik Apache Storm met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Verbinding maken met Apache Kafka via een Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
