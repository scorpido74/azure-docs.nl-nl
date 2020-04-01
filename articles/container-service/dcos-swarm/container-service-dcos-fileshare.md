---
title: (AFGESCHAFT) Bestandsshare voor Azure DC/OS-cluster
description: Een bestandsshare maken en aan een DC/OS-cluster koppelen in Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "60480381"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(AFGESCHAFT) Een bestandsshare maken en aan een DC/OS-cluster koppelen

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In deze zelfstudie wordt uitgelegd hoe u een bestandsshare in Azure kunt maken en deze aan elke agent en master van de DC/OS-cluster kunt koppelen. Als u een bestandsshare maakt, kunt u gemakkelijker bestanden, zoals configuratie-, toegangs- en logboeken, in uw cluster delen. In deze zelfstudie worden de volgende taken uitgevoerd:

> [!div class="checklist"]
> * Een Azure-opslagaccount maken
> * Een bestandsshare maken
> * De share koppelen in de DC/OS-cluster

U hebt een ACS DC/OS-cluster nodig om de stappen in deze zelfstudie te kunnen uitvoeren. Zo nodig kan [dit voorbeeldscript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) er een voor u maken.

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Een bestandsshare maken in Microsoft Azure

Voordat u een Azure-bestandsshare met een ACS DC/OS-cluster gebruikt, moeten het opslagaccount en de bestandsshare worden gemaakt. Voer het volgende script uit om het opslag- en bestandsshare te maken. Werk de parameters bij met de parameters uit uw omgeving.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>De share in uw cluster koppelen

Vervolgens moet de bestandsshare in elke virtuele machine in uw cluster worden gekoppeld. Deze taak wordt uitgevoerd met behulp van het cifs-hulpprogramma/-protocol. De koppelingsbewerking kan handmatig in elk knooppunt van het cluster worden uitgevoerd of door een script voor elk knooppunt in het cluster uit te voeren.

In dit voorbeeld worden twee scripts uitgevoerd: een om de Azure-bestandsshare te koppelen en een tweede om dit script op elk knooppunt van het DC/OS-cluster uit te voeren.

Eerst zijn de naam en toegangssleutel voor het Azure-opslagaccount nodig. Voer de volgende opdrachten uit om deze informatie te verkrijgen. Noteer ze, want deze waarden worden in een latere stap gebruikt.

Naam van opslagaccount:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Toegangssleutel voor opslagaccount:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Haal vervolgens de FQDN van de DC/OS-master op en sla deze op in een variabele.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Kopieer uw persoonlijke sleutel naar het hoofdknooppunt. Deze code is nodig om een ssh-verbinding met alle knooppunten in het cluster te maken. Werk de gebruikersnaam bij als geen standaardwaarde is gebruikt bij het maken van het cluster. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Maak een SSH-verbinding met de master (of de eerste master) van uw op DC/OS gebaseerde cluster. Werk de gebruikersnaam bij als geen standaardwaarde is gebruikt bij het maken van het cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Maak een bestand met de naam **cifsMount.sh** en kopieer de volgende inhoud naar het bestand. 

Dit script wordt gebruikt om de Azure-bestandsshare te koppelen. Werk de variabelen `STORAGE_ACCT_NAME` en `ACCESS_KEY` bij met de informatie die u eerder hebt verzameld.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Maak een tweede bestand met de naam **getNodesRunScript.sh** en kopieer de volgende inhoud in het bestand. 

Dit script detecteert alle clusterknooppunten en voert vervolgens het script **cifsMount.sh** uit om de bestandsshare voor elk knooppunt te koppelen.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Voer het script voor het koppelen van de Azure-bestandsshare uit op alle knooppunten van het cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

De bestandsshare is nu toegankelijk op `/mnt/share/dcosshare` in elk knooppunt van het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie werd een Azure-bestandsshare voor een DC/OS-cluster beschikbaar gemaakt aan de hand van de volgende stappen:

> [!div class="checklist"]
> * Een Azure-opslagaccount maken
> * Een bestandsshare maken
> * De share koppelen in de DC/OS-cluster

Ga naar de volgende zelfstudie om te leren over de integratie van een Azure Container Registry met DC/OS in Azure.  

> [!div class="nextstepaction"]
> [Load balancing gebruiken voor toepassingen](container-service-dcos-acr.md)
