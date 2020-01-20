---
title: (AFGESCHAFT) Snelstartgids voor Azure Container Service - Een DC/OS-cluster implementeren
description: Quickstart voor Azure Container Service - Een DC/OS-cluster implementeren
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9efedd5b619a2a3bd592019fea3ca48b7244ca10
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278507"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>(AFGESCHAFT) Een DC/OS-cluster implementeren

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS biedt een gedistribueerd platform voor het uitvoeren van moderne toepassingen in containers. Met Azure Container Service kunt u eenvoudig en snel een DC/OS-cluster inrichten dat gereed is voor productie. Deze quickstart biedt details over de basisstappen die nodig zijn om een DC/OS-cluster te implementeren en een eenvoudige workload uit te voeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure. 

Meld u aan bij uw Azure-abonnement met de opdracht [az login](/cli/azure/reference-index#az-login) en volg de instructies op het scherm.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Een DC/OS-cluster maken

Maak een DC/OS-cluster met behulp van de opdracht [az acs maken](/cli/azure/acs#az-acs-create).

In het volgende voorbeeld wordt een DC/OS-cluster gemaakt met de naam *myDCOSClster* en worden SSH-sleutels gemaakt, als deze nog niet bestaan. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

In sommige gevallen, zoals met een beperkte proefversie, heeft een Azure-abonnement beperkte toegang tot Azure-resources. Als de implementatie mislukt vanwege beperkte beschikbare kernen, verminder dan het aantal standaardagenten door `--agent-count 1` toe te voegen aan de opdracht [az acs create](/cli/azure/acs#az-acs-create). 

Na enkele minuten is de opdracht voltooid en wordt er informatie geretourneerd over de implementatie.

## <a name="connect-to-dcos-cluster"></a>Verbinding maken met een DC/OS-cluster

Zodra een DC/OS-cluster is gemaakt, hebt u toegang tot het cluster via een SSH-tunnel. Voer de volgende opdracht uit om het openbare IP-adres van de DC/OS-master te retourneren. Dit IP-adres wordt opgeslagen in een variabele en gebruikt in de volgende stap.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Voer de volgende opdracht uit en volg de instructies op het scherm op de SSH-tunnel te maken. Als poort 80 al in gebruik is, mislukt de opdracht. Werk de poort bij naar een poort die nog niet als tunnel wordt gebruikt, zoals `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

De SSH-tunnel kan worden getest door naar `http://localhost` te bladeren. Als een andere poort dan 80 wordt gebruikt, past u de locatie aan zodat deze overeenkomt. 

Als de SSH-tunnel is gemaakt, wordt de DC/OS-portal geretourneerd.

![DCOS-UI](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>De DC/OS CLI installeren

De DC/OS-opdrachtregelinterface wordt gebruikt om een DC/OS-cluster te beheren via de opdrachtregel. Installeer de DC/OS CLI met behulp van de opdracht [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Als u Azure CloudShell gebruikt, is de DC/OS CLI al geïnstalleerd. 

Als u Azure CLI uitvoert in Mac OS of Linux, moet u de opdracht mogelijk uitvoeren met sudo.

```azurecli
az acs dcos install-cli
```

Voordat de CLI kan worden gebruikt met het cluster, moet deze worden geconfigureerd voor gebruik van de SSH-tunnel. Voer hiervoor de volgende opdracht, waarbij u de poort aanpast, indien nodig.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Een toepassing uitvoeren

Het standaardmechanisme voor het plannen van een ACS DC/OS-cluster is Marathon. Marathon wordt gebruikt om een toepassing te starten en de status van de toepassing te beheren op het DC/OS-cluster. Als u een toepassing wilt plannen via Marathon, maakt u een bestand met de naam *marathon app.json* en kopieert u de volgende inhoud in dit bestand. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Voer de volgende opdracht uit om het uitvoeren van de toepassing op het DC/OS-cluster te plannen.

```azurecli
dcos marathon app add marathon-app.json
```

Voer de volgende opdracht uit om de implementatiestatus voor de app te zien.

```azurecli
dcos marathon app list
```

Wanneer de waarde in de kolom **WAITING** is gewijzigd van *True* naar *False*, is de implementatie van de toepassing voltooid.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Haal het openbare IP-adres van de DC/OS-clusteragents op.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Als u naar dit adres gaat, wordt de standaardsite NGINX geopend.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Het DC/OS-cluster verwijderen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, het DC/OS-cluster en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een DC/OS-cluster geïmplementeerd en hebt u een eenvoudige Docker-container op het cluster uitgevoerd. Ga verder met de ACS-zelfstudies voor meer informatie over Azure Container Service.

> [!div class="nextstepaction"]
> [Een ACS DC/OS-cluster beheren](container-service-dcos-manage-tutorial.md)
