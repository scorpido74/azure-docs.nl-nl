---
title: 'Zelf studie: een Apache Kafka REST-proxy cluster in HDInsight maken met behulp van Azure CLI'
description: Meer informatie over het uitvoeren van Apache Kafka-bewerkingen met behulp van een Kafka REST-proxy op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201883"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Zelf studie: een Apache Kafka REST-proxy cluster in HDInsight maken met behulp van Azure CLI

In deze zelf studie leert u hoe u een Apache Kafka [rest-proxy](./rest-proxy.md) cluster in azure HDInsight maakt met behulp van de Azure-opdracht regel interface (CLI). Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Apache Kafka is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten. Met Kafka REST proxy kunt u met uw Kafka-cluster communiceren via een [rest API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) over http. De Azure CLI is de platformoverschrijdende opdrachtregelervaring van Microsoft voor het beheren van Azure-resources.

De Apache Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. U kunt het cluster rechtstreeks openen via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Apache Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie [verbinding maken met Apache Kafka met behulp van een virtueel netwerk](./apache-kafka-connect-vpn-gateway.md)voor meer informatie.

In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * Vereisten voor Kafka REST proxy
> * Een Apache Kafka-cluster maken met behulp van Azure CLI

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een toepassing die is geregistreerd bij Azure AD. De client toepassingen die u schrijft om te communiceren met de Kafka REST-proxy, gebruiken de ID en het geheim van deze toepassing om te verifiëren bij Azure. Zie [een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-register-app.md)voor meer informatie.

* Een Azure AD-beveiligings groep met uw geregistreerde toepassing als lid. Deze beveiligings groep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [een basis groep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van Azure ad-groepen.

* Azure CLI. Zorg ervoor dat u ten minste versie 2.0.79 hebt. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

1. Meld u aan bij uw Azure-abonnement.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Omgevings variabelen instellen. Het gebruik van variabelen in deze zelf studie is gebaseerd op bash. Er zijn kleine variaties nodig voor andere omgevingen.

    |Variabele | Beschrijving |
    |---|---|
    |resourceGroupName|Vervang RESOURCEGROUPNAME door de naam van de nieuwe resource groep.|
    |locatie|Vervang de locatie door een regio waar het cluster wordt gemaakt. Gebruik de opdracht `az account list-locations` voor een lijst met geldige locaties|
    |clusterName|Vervang CLUSTERNAME door een globaal unieke naam voor het nieuwe cluster.|
    |storageAccount|Vervang STORAGEACCOUNTNAME door een naam voor uw nieuwe opslag account.|
    |httpPassword|Vervang het wacht woord door een wacht woord voor de cluster aanmelding, de **beheerder**.|
    |sshPassword|Vervang het wacht woord door een wacht woord voor de beveiligde shell gebruikers naam, **sshuser**.|
    |securityGroupName|Vervang SECURITYGROUPNAME door de naam van de client AAD-beveiligings groep voor Kafka rest proxy. De variabele wordt door gegeven aan de para meter `--kafka-client-group-name` voor `az-hdinsight-create`.|
    |securityGroupID|Vervang SECURITYGROUPID door de client AAD-beveiligings groep-ID voor Kafka rest proxy. De variabele wordt door gegeven aan de para meter `--kafka-client-group-id` voor `az-hdinsight-create`.|
    |storageContainer|Opslag container die door het cluster wordt gebruikt, blijft voor deze zelf studie. Deze variabele wordt ingesteld met de naam van het cluster.|
    |workernodeCount|Het aantal worker-knoop punten in het cluster, dat voor deze zelf studie moet worden ingeschakeld. Om hoge Beschik baarheid te garanderen, zijn voor Kafka mini maal drie werk knooppunten vereist|
    |clusterType|Type HDInsight-cluster. voor deze zelf studie moet u de voor keur laten.|
    |clusterVersion|De versie van het HDInsight-cluster, die voor deze zelf studie moet worden achtergelaten. Voor Kafka rest proxy is een minimale cluster versie van 4,0 vereist.|
    |componentVersion|Kafka-versie, voor deze zelf studie moet u ongewijzigd laten. Kafka rest proxy vereist een minimale onderdeel versie van 2,1.|

    Werk de variabelen bij met de gewenste waarden. Voer vervolgens de CLI-opdrachten in om de omgevings variabelen in te stellen.

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

1. [Maak de resource groep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) door de volgende opdracht in te voeren:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Maak een Azure Storage-account](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) door de onderstaande opdracht in te voeren:

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

1. [Pak de primaire sleutel](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) uit het Azure Storage-account in en sla deze op in een variabele door de onderstaande opdracht in te voeren:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Maak een Azure storage-container](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) door de volgende opdracht in te voeren:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Maak het HDInsight-cluster](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Let op de volgende para meters voordat u de opdracht typt:

    1. Vereiste para meters voor Kafka-clusters:

        |Parameter | Beschrijving|
        |---|---|
        |--type|De waarde moet **Kafka**zijn.|
        |--workernode-data-disks-per knoop punt|Het aantal gegevens schijven dat per werk knooppunt moet worden gebruikt. HDInsight Kafka wordt alleen ondersteund met gegevens schijven. In deze zelf studie wordt de waarde **2**gebruikt.|

    1. Vereiste para meters voor Kafka REST proxy:

        |Parameter | Beschrijving|
        |---|---|
        |--Kafka-beheer-knooppunt grootte|De grootte van het knoop punt. In deze zelf studie wordt gebruikgemaakt van de waarde **Standard_D4_v2**.|
        |--Kafka-client-groep-ID|De AAD-beveiligings groep-ID van de client voor de Kafka rest-proxy. De waarde wordt door gegeven van de variabele **$securityGroupID**.|
        |--Kafka-client-groep-naam|De naam van de AAD-beveiligings groep van de client voor Kafka rest proxy. De waarde wordt door gegeven van de variabele **$securityGroupName**.|
        |--versie|De versie van het HDInsight-cluster moet ten minste 4,0 zijn. De waarde wordt door gegeven van de variabele **$clusterVersion**.|
        |--onderdeel-versie|De Kafka-versie moet ten minste 2,1 zijn. De waarde wordt door gegeven van de variabele **$componentVersion**.|
    
        Als u het cluster wilt maken zonder REST-proxy, elimineert u `--kafka-management-node-size`, `--kafka-client-group-id`en `--kafka-client-group-name` van de `az hdinsight create` opdracht.

    1. Als u een bestaand virtueel netwerk hebt, voegt u de para meters `--vnet-name` en `--subnet`en de waarden ervan toe.

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

    Het kan enkele minuten duren voordat het proces voor het maken van het cluster is voltooid. Meestal ongeveer 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

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

Nu u een cluster met de Apache Kafka-REST-proxy hebt gemaakt in azure HDInsight met behulp van Azure CLI, gebruikt u Python-code om te communiceren met de REST-proxy:

> [!div class="nextstepaction"]
> [Voorbeeld toepassing maken](./rest-proxy.md#client-application-sample)