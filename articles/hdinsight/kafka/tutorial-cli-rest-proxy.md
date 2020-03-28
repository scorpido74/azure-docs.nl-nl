---
title: 'Zelfstudie: Een cluster met Apache Kafka REST-proxy maken in HDInsight met Azure CLI'
description: Meer informatie over het uitvoeren van Apache Kafka-bewerkingen met een Kafka REST-proxy op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201883"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Zelfstudie: Een cluster met Apache Kafka REST-proxy maken in HDInsight met Azure CLI

In deze zelfstudie leert u hoe u een cluster met Apache Kafka [REST-proxy maakt](./rest-proxy.md) in Azure HDInsight met behulp van de Azure command-line interface (CLI). Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Apache Kafka is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten. Met Kafka REST Proxy u communiceren met uw Kafka-cluster via een [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) via HTTP. De Azure CLI is de platformoverschrijdende opdrachtregelervaring van Microsoft voor het beheren van Azure-resources.

De Apache Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. U hebt rechtstreeks toegang tot het cluster via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Apache Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie Verbinding maken met Apache Kafka voor meer informatie [via een virtueel netwerk.](./apache-kafka-connect-vpn-gateway.md)

In deze zelfstudie leert u:

> [!div class="checklist"]
> * Voorwaarden voor Kafka REST proxy
> * Een Apache Kafka-cluster maken met Azure CLI

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een toepassing die is geregistreerd bij Azure AD. De clienttoepassingen die u schrijft om te communiceren met de Kafka REST-proxy, gebruiken de id en het geheim van deze toepassing om te verifiëren aan Azure. Zie [Een toepassing registreren bij het Microsoft-identiteitsplatform](../../active-directory/develop/quickstart-register-app.md)voor meer informatie.

* Een Azure AD-beveiligingsgroep met uw geregistreerde toepassing als lid. Deze beveiligingsgroep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van Azure AD-groepen.

* Azure CLI. Zorg ervoor dat u ten minste versie 2.0.79 hebt. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

1. Meld u aan bij uw Azure-abonnement.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Omgevingsvariabelen instellen. Het gebruik van variabelen in deze tutorial is gebaseerd op Bash. Kleine variaties zullen nodig zijn voor andere omgevingen.

    |Variabele | Beschrijving |
    |---|---|
    |resourceGroupName|Vervang RESOURCEGROUPNAME door de naam voor uw nieuwe resourcegroep.|
    |location|Vervang LOCATIE door een gebied waar het cluster wordt gemaakt. Gebruik de `az account list-locations` opdracht Voor een lijst met geldige locaties|
    |clusterName|Vervang CLUSTERNAME door een wereldwijd unieke naam voor uw nieuwe cluster.|
    |storageAccount|Vervang STORAGEACCOUNTNAME door een naam voor uw nieuwe opslagaccount.|
    |httpPassword httpPassword|Wachtwoord vervangen door een wachtwoord voor de cluster login, **admin**.|
    |sshPassword sshPassword|Vervang WACHTWOORD met een wachtwoord voor de veilige shell gebruikersnaam, **sshuser**.|
    |securityGroupName|Vervang SECURITYGROUPNAME door de naam van de client AAD-beveiligingsgroep voor Kafka Rest Proxy. De variabele wordt doorgegeven aan de `--kafka-client-group-name` parameter voor `az-hdinsight-create`.|
    |securityGroupID|Vervang SECURITYGROUPID door de client AAD-beveiligingsgroep-id voor Kafka Rest Proxy. De variabele wordt doorgegeven aan de `--kafka-client-group-id` parameter voor `az-hdinsight-create`.|
    |opslagContainer|Opslagcontainer het cluster zal gebruiken, laat as-is voor deze zelfstudie. Deze variabele wordt ingesteld met de naam van het cluster.|
    |workernodeCount workernodeCount|Aantal werknemersknooppunten in het cluster, laat as-is voor deze zelfstudie. Om een hoge beschikbaarheid te garanderen, heeft Kafka minimaal 3 werkknooppunten nodig|
    |clusterType|Type HDInsight-cluster, laat zoals voor deze zelfstudie.|
    |clusterVersie|HDInsight cluster versie, laat as-is voor deze tutorial. Kafka Rest Proxy vereist een minimale clusterversie van 4.0.|
    |componentVersie|Kafka versie, laat as-is voor deze tutorial. Kafka Rest Proxy vereist een minimale componentversie van 2.1.|

    Werk de variabelen bij met de gewenste waarden. Voer vervolgens de CLI-opdrachten in om de omgevingsvariabelen in te stellen.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Maak de resourcegroep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) door onderstaande opdracht in te voeren:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Maak een Azure Storage-account](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) door onderstaande opdracht in te voeren:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Haal de primaire sleutel](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) uit het Azure Storage-account en sla deze op in een variabele door onderstaande opdracht in te voeren:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Maak een Azure Storage-container](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) door onderstaande opdracht in te voeren:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Het HDInsight-cluster maken.](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) Noteer de volgende parameters voordat u de opdracht invoert:

    1. Vereiste parameters voor Kafka-clusters:

        |Parameter | Beschrijving|
        |---|---|
        |--type|De waarde moet **Kafka**zijn.|
        |-workernode-data-disks-per-node|Het aantal gegevensschijven dat moet worden gebruikt per schijfknooppunt van de werknemer. HDInsight Kafka wordt alleen ondersteund met dataschijven. Deze zelfstudie gebruikt een waarde van **2**.|

    1. Vereiste parameters voor Kafka REST proxy:

        |Parameter | Beschrijving|
        |---|---|
        |--kafka-management-node-size|De grootte van het knooppunt. Deze zelfstudie gebruikt de waarde **Standard_D4_v2**.|
        |--kafka-client-group-id|De CLIENT AAD-beveiligingsgroep-ID voor Kafka Rest Proxy. De waarde wordt doorgegeven aan de variabele **$securityGroupID**.|
        |--kafka-client-groep-naam|De naam van de client AAD-beveiligingsgroep voor Kafka Rest Proxy. De waarde wordt doorgegeven aan de variabele **$securityGroupName**.|
        |--versie|De HDInsight-clusterversie moet ten minste 4.0 zijn. De waarde wordt doorgegeven aan de variabele **$clusterVersion**.|
        |--component-versie|De Kafka-versie moet ten minste 2.1 zijn. De waarde wordt doorgegeven aan de variabele **$componentVersion**.|
    
        Als u het cluster wilt maken zonder `--kafka-management-node-size` `--kafka-client-group-id`REST-proxy, elimineert u en `--kafka-client-group-name` van de `az hdinsight create` opdracht.

    1. Als u een bestaand virtueel netwerk `--vnet-name` `--subnet`hebt, voegt u de parameters en , en hun waarden toe.

    Voer de volgende opdracht in om het cluster te maken:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Het kan enkele minuten duren voordat het proces voor het maken van het cluster is voltooid. Meestal rond de 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Voer alle of enkele van de volgende opdrachten in om resources te verwijderen:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Nu u met succes een cluster met Apache Kafka REST-proxy hebt gemaakt in Azure HDInsight met Azure CLI, gebruikt u Python-code om te communiceren met de REST-proxy:

> [!div class="nextstepaction"]
> [Voorbeeldtoepassing maken](./rest-proxy.md#client-application-sample)