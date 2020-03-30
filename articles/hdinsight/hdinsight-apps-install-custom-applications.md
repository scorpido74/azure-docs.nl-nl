---
title: Installeer uw aangepaste Apache Hadoop-toepassingen op Azure HDInsight
description: Meer informatie over het installeren van HDInsight-toepassingen voor Apache Hadoop-clusters in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 8fe9e551a4ca7dea8f53e0f8c2975ca3d88c48f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366254"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Aangepaste Apache Hadoop-toepassingen installeren op Azure HDInsight

In dit artikel leert u hoe u een [Apache Hadoop-toepassing](https://hadoop.apache.org/) installeert op Azure HDInsight, die niet is gepubliceerd op de Azure-portal. De toepassing die u in dit artikel installeert, is [Hue.](https://gethue.com/)

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een HDInsight-cluster.  Deze toepassingen kunnen zijn ontwikkeld door Microsoft, door onafhankelijke softwareleveranciers (ISV) of door u zelf.  

## <a name="prerequisites"></a>Vereisten

Als u HDInsight-toepassingen wilt installeren op een bestaand HDInsight-cluster, hebt u een HDInsight-cluster nodig. Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md) voor informatie over het maken van een cluster. U kunt ook HDInsight-toepassingen installeren wanneer u een HDInsight-cluster maakt.

## <a name="install-hdinsight-applications"></a>HDInsight-toepassingen installeren

HDInsight-toepassingen kunnen worden geïnstalleerd wanneer u een cluster maakt of op een bestaand HDInsight-cluster. Zie [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: een HDInsight-toepassing installeren) voor het definiëren van Azure Resource Manager-sjablonen.

De bestanden die nodig zijn voor het implementeren van deze toepassing (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): de Resource Manager-sjabloon voor het installeren van de HDInsight-toepassing. Zie [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: een HDInsight-toepassing installeren) om uw eigen Resource Manager-sjabloon te ontwikkelen.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): de scriptactie die wordt aangeroepen met de Resource Manager-sjabloon om het Edge-knooppunt te configureren.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): het binaire Hue-bestand dat wordt aangeroepen vanuit hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): het binaire Hue-bestand dat wordt aangeroepen vanuit hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): een voorbeeldweb-app (Tomcat) die wordt aangeroepen vanuit hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Hue installeren op een bestaand HDInsight-cluster

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de sjabloon Resourcebeheer in de Azure-portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    De sjabloon Resourcemanager [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)bevindt zich op .  Zie [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: een HDInsight-toepassing installeren) voor informatie over het schrijven van deze Resource Manager-sjabloon.

1. Selecteer de bestaande **resourcegroep** die uw cluster bevat in de vervolgkeuzelijst. Het is vereist om dezelfde brongroep als het cluster te gebruiken.

1. Voer de naam in van het cluster waar u de toepassing wilt installeren. Dit cluster moet een bestaand cluster zijn.

1. Schakel het selectievakje in want **ik ga akkoord met de hierboven vermelde algemene voorwaarden.**

1. Selecteer **Aankoop**.

U kunt de installatiestatus zien op de tegel die is vastgemaakt aan het portaldashboard en de portalmelding. (Klik op het belpictogram boven aan de portal.)  Het duurt ongeveer 10 minuten om de toepassing te installeren.

### <a name="to-install-hue-while-creating-a-cluster"></a>Hue installeren tijdens het maken van een cluster

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de sjabloon Resourcebeheer in de Azure-portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    De sjabloon Resourcemanager [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)bevindt zich op .  Zie [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: een HDInsight-toepassing installeren) voor informatie over het schrijven van deze Resource Manager-sjabloon.

2. Volg de instructies om het cluster te maken en Hue te installeren. Zie [Op Linux gebaseerde Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het maken van HDInsight-clusters.

### <a name="other-installation-methods"></a>Andere installatiemethoden

Naast de Azure Portal kunt u ook [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) en [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) gebruiken om Resource Manager-sjablonen aan te roepen.

## <a name="validate-the-installation"></a>De installatie valideren

Controleer de status van de toepassing in de Azure Portal om de installatie van de toepassing te valideren. Bovendien u ook valideren alle HTTP-eindpunten kwam zoals verwacht en de webpagina als er een is.

Voor **Hue**u de volgende stappen gebruiken:

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het cluster waar u de toepassing hebt geïnstalleerd.
1. Selecteer **Toepassingen**in het menu **Instellingen** .
1. Selecteer **tint** in de lijst om de eigenschappen weer te geven.  
1. Selecteer de koppeling Webpagina om de website te valideren.

### <a name="azure-cli"></a>Azure-CLI

Vervang `CLUSTERNAME`en `RESOURCEGROUP` door de relevante waarden en voer de onderstaande opdrachten in:

* Om alle toepassingen voor het HDInsight-cluster weer te geven.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Eigenschappen van de opgegeven toepassing ophalen.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Problemen met de installatie oplossen

U kunt de installatiestatus van de toepassing controleren in de portalmelding (klik boven aan de portal op het belpictogram).

Als een toepassingsinstallatie is mislukt, u de foutberichten en foutopsporingsgegevens op drie plaatsen bekijken:

* HDInsight-toepassingen: algemene foutgegevens.

    Open het cluster vanuit de portal en selecteer Toepassingen in Instellingen:

    ![Fout bij het installeren van HDInsight-toepassingen](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* HDInsight-scriptactie: als het foutbericht voor HDInsight-toepassingen een mislukte scriptactie aangeeft, wordt er meer informatie over deze fout weergegeven in het deelvenster Scriptacties.

    Selecteer Scriptactie in Instellingen. De foutberichten worden weergegeven in de geschiedenis van de scriptactie

    ![Scriptactiefout voor HDInsight-toepassingen](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI: Als het installatiescript de oorzaak van de fout was, gebruikt u De Gebruikersinterface van Ambari Om volledige logboeken over de installatiescripts te controleren.

    Zie [Probleemoplossing](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) voor meer informatie .

## <a name="remove-hdinsight-applications"></a>HDInsight-toepassingen verwijderen

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het cluster waar u de toepassing hebt geïnstalleerd.
1. Selecteer **Toepassingen**in het menu **Instellingen** .
1. Klik met de rechtermuisknop op de toepassing die u wilt verwijderen en selecteer **Verwijderen**.
1. Selecteer **Ja** om te bevestigen.

### <a name="azure-cli"></a>Azure-CLI

Vervang `NAME` `CLUSTERNAME`, `RESOURCEGROUP` en met de relevante waarden en voer de onderstaande opdracht in:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Volgende stappen

* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: een HDInsight-toepassing installeren): informatie over het ontwikkelen van Resource Manager-sjablonen voor het implementeren van HDInsight-toepassingen.
* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md): informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Maak Apache Hadoop-clusters op basis van Linux in HDInsight met behulp van Resource Manager-sjablonen:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)lees hoe u Resource Manager-sjablonen aanroept om HDInsight-clusters te maken.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Lege edge-knooppunten gebruiken in HDInsight): meer informatie over het gebruik van een leeg edge-knooppunt om toegang te krijgen tot het HDInsight-cluster, HDInsight toepassingen te testen en HDInsight-toepassingen te hosten.
