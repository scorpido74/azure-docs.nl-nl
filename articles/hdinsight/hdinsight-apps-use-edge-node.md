---
title: Lege Edge-knoop punten op Apache Hadoop clusters in HDInsight-Azure gebruiken
description: Een leeg Edge-knoop punt toevoegen aan een HDInsight-cluster dat kan worden gebruikt als een client en vervolgens uw HDInsight-toepassingen testen/hosten.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: a669c6fe0ffd6ff1c4d4613a91074edfe9fa87e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162653"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Lege Edge-knoop punten op Apache Hadoop clusters in HDInsight gebruiken

Meer informatie over het toevoegen van een leeg Edge-knoop punt aan een HDInsight-cluster. Een lege Edge-knoop punt is een virtuele Linux-machine waarop dezelfde client hulpprogramma's zijn geïnstalleerd en geconfigureerd als in de hoofd knooppunten, maar zonder [Apache Hadoop](https://hadoop.apache.org/) services worden uitgevoerd. U kunt het Edge-knoop punt gebruiken om toegang te krijgen tot het cluster, uw client toepassingen te testen en uw client toepassingen te hosten. 

Wanneer u het cluster maakt, kunt u een leeg Edge-knoop punt toevoegen aan een bestaand HDInsight-cluster. Het toevoegen van een leeg Edge-knoop punt geschiedt met behulp van Azure Resource Manager sjabloon.  In het volgende voor beeld ziet u hoe dit wordt gedaan met behulp van een sjabloon:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "{}"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Zoals in het voor beeld wordt weer gegeven, kunt u eventueel een [script actie](hdinsight-hadoop-customize-cluster-linux.md) aanroepen om aanvullende configuratie uit te voeren, zoals het installeren van [Apache-tinten](hdinsight-hadoop-hue-linux.md) in het Edge-knoop punt. De script actie script moet openbaar toegankelijk zijn op het web.  Als het script bijvoorbeeld wordt opgeslagen in azure Storage, gebruikt u open bare containers of open bare blobs.

De grootte van de virtuele machine van het Edge-knoop punt moet voldoen aan de vereisten voor de VM-grootte van het HDInsight-cluster. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)voor de aanbevolen VM-grootten voor werk knooppunten.

Nadat u een Edge-knoop punt hebt gemaakt, kunt u via SSH verbinding maken met het Edge-knoop punt en client hulpprogramma's uitvoeren om toegang te krijgen tot het Hadoop-cluster in HDInsight.

> [!WARNING]   
> Aangepaste onderdelen die zijn geïnstalleerd op het Edge-knoop punt ontvangen commercieel redelijke ondersteuning van micro soft. Dit kan leiden tot problemen oplossen die u tegen komt. Het is ook mogelijk dat u resources van de community voor verdere ondersteuning moet worden genoemd. Hier volgen enkele van de meest actieve sites om hulp van de community te verkrijgen:
>
> * [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Als u een Apache-technologie gebruikt, kunt u mogelijk ondersteuning vinden via de Apache-project sites op [https://apache.org](https://apache.org), zoals de [Apache Hadoop](https://hadoop.apache.org/) -site.

> [!IMPORTANT]
> Er worden binnen drie maanden na publicatie een Ubuntu-installatie kopie beschikbaar voor het maken van nieuwe HDInsight-clusters. Vanaf januari 2019 worden clusters **zonder** automatische patches uitgevoerd (inclusief Edge-knoop punten). Klanten moeten script acties of andere mechanismen gebruiken om een actief cluster te patchen.  Zie [patches voor het besturings systeem voor HDInsight](./hdinsight-os-patching.md)voor meer informatie.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Een Edge-knoop punt toevoegen aan een bestaand cluster
In deze sectie gebruikt u een resource manager-sjabloon om een Edge-knoop punt toe te voegen aan een bestaand HDInsight-cluster.  De Resource Manager-sjabloon vindt u in [github](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). De Resource Manager-sjabloon roept een script actie op die zich op https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh bevindt. Het script voert geen acties uit.  Het is de demonstratie van het aanroepen van een script actie vanuit een resource manager-sjabloon te demonstreren.

**Een leeg Edge-knoop punt toevoegen aan een bestaand cluster**

1. Klik op de volgende afbeelding om u aan te melden bij Azure en open de Azure Resource Manager sjabloon in de Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Configureer de volgende eigenschappen:
   
   * **Abonnement**: Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.
   * **Resource groep**: Selecteer de resource groep die wordt gebruikt voor het bestaande HDInsight-cluster.
   * **Locatie**: Selecteer de locatie van het bestaande HDInsight-cluster.
   * **Cluster naam**: Voer de naam in van een bestaand HDInsight-cluster.
   * **Grootte van rand knooppunt**: Selecteer een van de VM-grootten. De VM-grootte moet voldoen aan de VM-grootte vereisten voor het worker-knoop punt. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)voor de aanbevolen VM-grootten voor werk knooppunten.
   * **Voor voegsel van Edge-knoop punt**: de standaard waarde is **Nieuw**.  Met de standaard waarde is de naam van het Edge **-knoop punt New-de**.  U kunt het voor voegsel aanpassen vanuit de portal. U kunt ook de volledige naam van de sjabloon aanpassen.

4. Ga **akkoord met de bovenstaande voor waarden**en klik vervolgens op **aanschaffen** om het Edge-knoop punt te maken.

>[!IMPORTANT]  
> Zorg ervoor dat u de Azure-resource groep voor het bestaande HDInsight-cluster selecteert.  Anders wordt het volgende fout bericht weer gegeven: kan de aangevraagde bewerking niet uitvoeren op een geneste bron. De bovenliggende resource&lt;clustername > is niet gevonden.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Een Edge-knoop punt toevoegen bij het maken van een cluster
In deze sectie gebruikt u een resource manager-sjabloon om een HDInsight-cluster te maken met een Edge-knoop punt.  De Resource Manager-sjabloon vindt u in de [Galerie met sjablonen van Azure Quick](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)start. De Resource Manager-sjabloon roept een script actie op die zich op https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh bevindt. Het script voert geen acties uit.  Het is de demonstratie van het aanroepen van een script actie vanuit een resource manager-sjabloon te demonstreren.

**Een HDInsight-cluster maken met een Edge-knoop punt**

1. Maak een HDInsight-cluster als u er nog geen hebt.  Zie [aan de slag met Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Klik op de volgende afbeelding om u aan te melden bij Azure en open de Azure Resource Manager sjabloon in de Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Configureer de volgende eigenschappen:
   
   * **Abonnement**: Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.
   * **Resource groep**: Maak een nieuwe resource groep die wordt gebruikt voor het cluster.
   * **Locatie**: selecteer een locatie voor de resourcegroep.
   * **Cluster naam**: Voer een naam in voor het nieuwe cluster dat u wilt maken.
   * **Gebruikers naam**voor het aanmelden bij een cluster: Voer de HADOOP http-gebruikers naam in.  De standaardnaam is **admin**.
   * **Wacht woord voor cluster aanmelding**: Voer het Hadoop http-gebruikers wachtwoord in.
   * **SSH-gebruikers naam**: Voer de SSH-gebruikers naam in. De standaard naam is **sshuser**.
   * **SSH-wacht woord**: Voer het SSH-gebruikers wachtwoord in.
   * **Script actie installeren**: behoud de standaard waarde voor dit artikel.
     
     Sommige eigenschappen zijn vastgelegd in de sjabloon: cluster type, aantal cluster worker-knoop punten, grootte van Edge-knoop punt en rand knooppunt naam.
4. Ga **akkoord met de bovenstaande voor waarden**en klik vervolgens op **aanschaffen** om het cluster met het Edge-knoop punt te maken.

## <a name="add-multiple-edge-nodes"></a>Meerdere Edge-knoop punten toevoegen

U kunt meerdere Edge-knoop punten toevoegen aan een HDInsight-cluster.  De configuratie van knoop punten met meerdere randen kan alleen worden uitgevoerd met Azure Resource Manager sjablonen.  Zie het sjabloon voorbeeld aan het begin van dit artikel.  U moet de **targetInstanceCount** bijwerken zodat deze overeenkomt met het aantal Edge-knoop punten dat u wilt maken.

## <a name="access-an-edge-node"></a>Toegang tot een Edge-knoop punt
Het SSH-eind punt van het Edge-knoop punt is &lt;EdgeNodeName >.&lt;clustername >-ssh.azurehdinsight.net:22.  Bijvoorbeeld new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Het Edge-knoop punt wordt weer gegeven als een toepassing op het Azure Portal.  De portal geeft u de informatie over de toegang tot het Edge-knoop punt via SSH.

**Het SSH-eind punt van het Edge-knoop punt controleren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een Edge-knoop punt.
3. Klik op **toepassingen**. Het Edge-knoop punt wordt weer geven.  De standaard naam is **New-de**.
4. Klik op het Edge-knoop punt. Het SSH-eind punt wordt weer geven.

**Hive op het Edge-knoop punt gebruiken**

1. Gebruik SSH om verbinding te maken met het edge-knooppunt. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Nadat u met SSH verbinding hebt gemaakt met het Edge-knoop punt, gebruikt u de volgende opdracht om de Hive-console te openen:
   
        hive
3. Voer de volgende opdracht uit om Hive-tabellen in het cluster weer te geven:
   
        show tables;

## <a name="delete-an-edge-node"></a>Een Edge-knoop punt verwijderen
U kunt een Edge-knoop punt verwijderen uit het Azure Portal.

**Toegang krijgen tot een Edge-knoop punt**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een Edge-knoop punt.
3. Klik op **toepassingen**. Er wordt een lijst met Edge-knoop punten weer geven.  
4. Klik met de rechter muisknop op het Edge-knoop punt dat u wilt verwijderen en klik vervolgens op **verwijderen**.
5. Klik op **Ja** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u een Edge-knoop punt toevoegt en hoe u toegang krijgt tot het Edge-knoop punt. Raadpleeg de volgende artikelen voor meer informatie:

* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md): informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [Aangepaste hdinsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): meer informatie over het implementeren van een niet-gepubliceerde hdinsight-toepassing op hdinsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [MSDN: een HDInsight-toepassing installeren](https://msdn.microsoft.com/library/mt706515.aspx): informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Op Linux gebaseerde Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informatie over het aanroepen van Resource Manager-sjablonen voor het maken van HDInsight-clusters.

