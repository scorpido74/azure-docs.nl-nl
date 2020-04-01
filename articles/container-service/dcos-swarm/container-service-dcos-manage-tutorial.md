---
title: '(AFGESCHAFT) Zelfstudie voor Azure Container Service: DC/OS beheren'
description: 'Zelfstudie Azure Container Service: DC/OS beheren'
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4212277dbdf29705152832f3830692b43b8d1297
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402810"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>(AFGESCHAFT) Zelfstudie voor Azure Container Service: DC/OS beheren

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS biedt een gedistribueerd platform voor het uitvoeren van moderne toepassingen in containers. Met Azure Container Service kunt u eenvoudig en snel een DC/OS-cluster inrichten dat gereed is voor productie. Met deze quickstart worden basisstappen beschreven die nodig zijn om een DC/OS-cluster te implementeren en de basiswerkbelasting uit te voeren.

> [!div class="checklist"]
> * Een ACS DC/OS-cluster maken
> * Verbinding maken met het cluster
> * De DC/OS CLI installeren
> * Een toepassing implementeren in het cluster
> * Een toepassing in het cluster schalen
> * De DC/OS-clusterknooppunten schalen
> * Eenvoudig DC/OS-beheer
> * Het DC/OS-cluster verwijderen

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Een DC/OS-cluster maken

Maak eerst een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *Europa West*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Maak vervolgens een DC/OS-cluster met de opdracht [az acs create](/cli/azure/acs#az-acs-create).

In het volgende voorbeeld wordt een DC/OS-cluster gemaakt met de naam *myDCOSClster* en worden SSH-sleutels gemaakt, als deze nog niet bestaan. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en wordt er informatie geretourneerd over de implementatie.

## <a name="connect-to-dcos-cluster"></a>Verbinding maken met een DC/OS-cluster

Zodra een DC/OS-cluster is gemaakt, hebt u toegang tot het cluster via een SSH-tunnel. Voer de volgende opdracht uit om het openbare IP-adres van de DC/OS-master te retourneren. Dit IP-adres wordt opgeslagen in een variabele en gebruikt in de volgende stap.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Voer de volgende opdracht uit en volg de instructies op het scherm op de SSH-tunnel te maken. Als poort 80 al in gebruik is, mislukt de opdracht. Werk de poort bij naar een poort die nog niet als tunnel wordt gebruikt, zoals `85:localhost:80`. 

```console
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>De DC/OS CLI installeren

Installeer de DC/OS CLI met behulp van de opdracht [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Als u Azure CloudShell gebruikt, is de DC/OS CLI al geïnstalleerd. Als u Azure CLI uitvoert in Mac OS of Linux, moet u de opdracht mogelijk uitvoeren met sudo.

```azurecli
az acs dcos install-cli
```

Voordat de CLI kan worden gebruikt met het cluster, moet deze worden geconfigureerd voor gebruik van de SSH-tunnel. Voer hiervoor de volgende opdracht, waarbij u de poort aanpast, indien nodig.

```console
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Een toepassing uitvoeren

Het standaardmechanisme voor het plannen van een ACS DC/OS-cluster is Marathon. Marathon wordt gebruikt om een toepassing te starten en de status van de toepassing te beheren op het DC/OS-cluster. Als u een toepassing wilt plannen via Marathon, maakt u een bestand met de naam **marathon app.json** en kopieert u de volgende inhoud in dit bestand. 

```json
{
  "id": "demo-app-private",
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
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Voer de volgende opdracht uit om het uitvoeren van de toepassing op het DC/OS-cluster te plannen.

```console
dcos marathon app add marathon-app.json
```

Voer de volgende opdracht uit om de implementatiestatus voor de app te zien.

```console
dcos marathon app list
```

Wanneer de waarde in de kolom **TASKS** is veranderd van *0/1* in *1/1*, is de implementatie van de toepassing voltooid.

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Marathon-toepassing schalen

In het vorige voorbeeld is één exemplaar van een toepassing gemaakt. Werk deze implementatie bij, zodat drie exemplaren van de toepassing beschikbaar zijn. Open hiervoor het bestand **marathon-app.json** en werk de eigenschap voor exemplaar bij naar 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Werk de toepassing bij met de opdracht `dcos marathon app update`.

```console
dcos marathon app update demo-app-private < marathon-app.json
```

Voer de volgende opdracht uit om de implementatiestatus voor de app te zien.

```console
dcos marathon app list
```

Wanneer de waarde in de kolom **TASKS** is veranderd van *1/3* in *3/1*, is de implementatie van de toepassing voltooid.

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Op internet toegankelijk app uitvoeren

Het ACS DC/OS-cluster bestaat uit twee sets knooppunten, een openbare set die toegankelijk is op internet en een privéset die niet toegankelijk is op internet. De standaardset bestaat uit de privéknooppunten, die zijn gebruikt in het vorige voorbeeld.

Als u een toepassing toegankelijk wilt maken op internet, moet u deze implementeren in de openbare set knooppunten. Geef hiervoor het object `acceptedResourceRoles` een waarde van `slave_public`.

Maak een bestand met de naam **nginx-public.json** en kopieer de volgende inhoud naar het bestand.

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

```console
dcos marathon app add nginx-public.json
```

Haal het openbare IP-adres van de openbare DC/OS-clusteragents op.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Als u naar dit adres gaat, wordt de standaardsite NGINX geopend.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Het DC/OS-cluster schalen

In de voorgaande voorbeelden is een toepassing geschaald naar meerdere exemplaren. De DC/OS-infrastructuur kan ook worden geschaald om meer of minder rekencapaciteit te bieden. Dit wordt gedaan met de opdracht [az acs scale](/cli/azure/acs#az-acs-scale). 

Als u het huidige aantal DC/OS-agents wilt zien, gebruikt u de opdracht [az acs show](/cli/azure/acs#az-acs-show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Als u het aantal wilt verhogen naar 5, gebruikt u de opdracht [az acs scale](/cli/azure/acs#az-acs-scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Het DC/OS-cluster verwijderen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, het DC/OS-cluster en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over eenvoudige DC/OS-beheertaken, waaronder de volgende. 

> [!div class="checklist"]
> * Een ACS DC/OS-cluster maken
> * Verbinding maken met het cluster
> * De DC/OS CLI installeren
> * Een toepassing implementeren in het cluster
> * Een toepassing in het cluster schalen
> * De DC/OS-clusterknooppunten schalen
> * Het DC/OS-cluster verwijderen

Volg de volgende zelfstudie voor meer informatie over taakverdeling in een toepassing in DC/OS in Azure. 

> [!div class="nextstepaction"]
> [Load balancing gebruiken voor toepassingen](container-service-load-balancing.md)
