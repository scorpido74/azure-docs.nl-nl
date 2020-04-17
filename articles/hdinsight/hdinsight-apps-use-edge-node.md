---
title: Lege randknooppunten gebruiken op Apache Hadoop-clusters in Azure HDInsight
description: Een leeg randknooppunt toevoegen aan een HDInsight-cluster. Gebruikt als client en test of host vervolgens uw HDInsight-toepassingen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537794"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Lege randknooppunten gebruiken op Apache Hadoop-clusters in HDInsight

Meer informatie over het toevoegen van een leeg randknooppunt aan een HDInsight-cluster. Een leeg randknooppunt is een Virtuele Linux-machine met dezelfde clienttools die zijn geïnstalleerd en geconfigureerd als in de hoofdnoden. Maar zonder [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) diensten draaien. U het randknooppunt gebruiken voor toegang tot het cluster, het testen van uw clienttoepassingen en het hosten van uw clienttoepassingen.

U een leeg randknooppunt toevoegen aan een bestaand HDInsight-cluster aan een nieuw cluster wanneer u het cluster maakt. Het toevoegen van een leeg randknooppunt gebeurt met azure resource manager-sjabloon.  In het volgende voorbeeld wordt uitgelegd hoe het wordt gedaan met behulp van een sjabloon:

```json
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
```

Zoals in het voorbeeld wordt weergegeven, u optioneel een [scriptactie](hdinsight-hadoop-customize-cluster-linux.md) aanroepen om extra configuratie uit te voeren. Zoals het installeren van [Apache Hue](hdinsight-hadoop-hue-linux.md) in het randknooppunt. Het scriptactiescript moet openbaar toegankelijk zijn op het web.  Als het script bijvoorbeeld is opgeslagen in Azure Storage, gebruikt u openbare containers of openbare blobs.

De grootte van de virtuele machine met randknooppunt moet voldoen aan de vereisten voor vm-grootte van de HDInsight-clusterworker. Zie [Apache Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)voor de aanbevolen vm-formaten voor werknemersknooppunt.

Nadat u een randknooppunt hebt gemaakt, u verbinding maken met het randknooppunt met SSH en clienthulpprogramma's uitvoeren om toegang te krijgen tot het Hadoop-cluster in HDInsight.

> [!WARNING]
> Aangepaste onderdelen die op het randknooppunt zijn geïnstalleerd, krijgen commercieel redelijke ondersteuning van Microsoft. Dit kan leiden tot het oplossen van problemen die u tegenkomt. Of u worden verwezen naar de gemeenschap middelen voor verdere hulp. De volgende zijn enkele van de meest actieve sites voor het krijgen van hulp van de gemeenschap:
>
> * [MSDN forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Als u een Apache-technologie gebruikt, u mogelijk hulp [https://apache.org](https://apache.org)vinden via de Apache-projectsites op, zoals de [Apache Hadoop-site.](https://hadoop.apache.org/)

> [!IMPORTANT]
> Ubuntu-afbeeldingen worden binnen 3 maanden na publicatie beschikbaar voor het maken van nieuwe HDInsight-cluster. Vanaf januari 2019 worden lopende clusters (inclusief randknooppunten) **niet** automatisch gepatcht. Klanten moeten scriptacties of andere mechanismen gebruiken om een lopend cluster te patchen.  Zie [OS patchen voor HDInsight voor](./hdinsight-os-patching.md)meer informatie.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Een randknooppunt toevoegen aan een bestaand cluster

In deze sectie gebruikt u een resourcemanagersjabloon om een randknooppunt toe te voegen aan een bestaand HDInsight-cluster.  De sjabloon Resourcemanager is te vinden in [GitHub.](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/) De sjabloon Resourcemanager roept een https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shscriptactie aan die zich op . Het script doet geen acties.  Het is om aanroepenscriptactie aan te tonen vanuit een Resource Manager-sjabloon.

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de sjabloon Azure Resource Manager in de Azure-portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configureer de volgende eigenschappen:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.|
    |Resourcegroep|Selecteer de resourcegroep die wordt gebruikt voor het bestaande HDInsight-cluster.|
    |Locatie|Selecteer de locatie van het bestaande HDInsight-cluster.|
    |Clusternaam|Voer de naam in van een bestaand HDInsight-cluster.|

1. Controleer **of ik akkoord ga met de hierboven vermelde algemene voorwaarden**en selecteer Vervolgens **Kopen** om het randknooppunt te maken.

> [!IMPORTANT]  
> Selecteer de Azure-brongroep voor het bestaande HDInsight-cluster.  Anders krijgt u het foutbericht 'Kan de gevraagde bewerking niet uitvoeren op geneste resources. Bovenliggende&lt;bron ' ClusterNaam>' niet gevonden."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Een randknooppunt toevoegen bij het maken van een cluster

In deze sectie gebruikt u een resourcemanagersjabloon om een HDInsight-cluster met een randknooppunt te maken.  De sjabloon Resourcebeheer is te vinden in de [azure quickstart-sjablonengalerie](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). De sjabloon Resourcemanager roept een https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shscriptactie aan die zich op . Het script doet geen acties.  Het is om aanroepenscriptactie aan te tonen vanuit een Resource Manager-sjabloon.

1. Maak een HDInsight-cluster als u er nog geen hebt.  Zie [Aan de slag met Hadoop in HDInsight.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de sjabloon Azure Resource Manager in de Azure-portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configureer de volgende eigenschappen:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.|
    |Resourcegroep|Maak een nieuwe resourcegroep die voor het cluster wordt gebruikt.|
    |Locatie|Selecteer een locatie voor de resourcegroep.|
    |Clusternaam|Voer een naam in voor het nieuwe cluster dat u wilt maken.|
    |Gebruikersnaam voor clusteraanmelding|Voer de HTTP-gebruikersnaam van Hadoop in.  De standaardnaam is **admin**.|
    |Wachtwoord voor clusteraanmelding|Voer het HTTP-gebruikerswachtwoord van Hadoop in.|
    |Ssh-gebruikersnaam|Voer de SSH-gebruikersnaam in. De standaardnaam is **sshuser**.|
    |Ssh-wachtwoord|Voer het SSH-gebruikerswachtwoord in.|
    |Scriptactie installeren|Houd de standaardwaarde voor het doorlopen van dit artikel.|

    Sommige eigenschappen zijn hardcoded in de sjabloon: clustertype, clusterwerkknooppuntaantal, grootte van het randknooppunt en naam van het randknooppunt.

1. Controleer **of ik akkoord ga met de hierboven vermelde algemene voorwaarden**en selecteer Vervolgens **Kopen** om het cluster met het randknooppunt te maken.

## <a name="add-multiple-edge-nodes"></a>Meerdere randknooppunten toevoegen

U meerdere randknooppunten toevoegen aan een HDInsight-cluster.  De configuratie van meerdere randknooppunten kan alleen worden uitgevoerd met Azure Resource Manager-sjablonen.  Zie het sjabloonvoorbeeld aan het begin van dit artikel.  Werk de **targetInstanceCount** bij om het aantal randknooppunten weer te geven dat u wilt maken.

## <a name="access-an-edge-node"></a>Toegang tot een randknooppunt

Het ssh-eindpunt voor &lt;randknooppunt is EdgeNodeName>. &lt;ClusterName>-ssh.azurehdinsight.net:22.  Bijvoorbeeld new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Het randknooppunt wordt weergegeven als een toepassing op de Azure-portal.  De portal geeft u de informatie om toegang te krijgen tot het randknooppunt met behulp van SSH.

**Het SSH-eindpunt van het randknooppunt verifiëren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een randknooppunt.
3. Selecteer **Toepassingen**. Je zult het randknooppunt zien.  De standaardnaam is **nieuw-edgenode**.
4. Selecteer het randknooppunt. U ziet het SSH-eindpunt.

**Hive op het randknooppunt gebruiken**

1. Gebruik SSH om verbinding te maken met het edge-knooppunt. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Nadat u met SSH verbinding hebt gemaakt met het randknooppunt, gebruikt u de volgende opdracht om de Hive-console te openen:

        hive

3. Voer de volgende opdracht uit om Hive-tabellen in het cluster weer te geven:

        show tables;

## <a name="delete-an-edge-node"></a>Een randknooppunt verwijderen

U een randknooppunt verwijderen uit de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een randknooppunt.
3. Selecteer **Toepassingen**. U ziet een lijst met randknooppunten.  
4. Klik met de rechtermuisknop op het randknooppunt dat u wilt verwijderen en selecteer **Verwijderen**.
5. Selecteer **Ja** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een randknooppunt toevoegt en hoe u toegang krijgt tot het randknooppunt. Zie voor meer informatie de volgende artikelen:

* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md): informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [Installeer aangepaste HDInsight-toepassingen:](hdinsight-apps-install-custom-applications.md)leer hoe u een niet-gepubliceerde HDInsight-toepassing implementeert op HDInsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [MSDN: een HDInsight-toepassing installeren](https://msdn.microsoft.com/library/mt706515.aspx): informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Maak Apache Hadoop-clusters op basis van Linux in HDInsight met behulp van Resource Manager-sjablonen:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)lees hoe u Resource Manager-sjablonen aanroept om HDInsight-clusters te maken.
