---
title: (AFGESCHAFT) ACR gebruiken met een Azure DC/OS-cluster
description: Een Azure Container Registry met een DC/OS-cluster gebruiken in Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 9e69b66c7cee5a6e012ad7ed2477556fa840bfb5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78402066"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(AFGESCHAFT) ACR met een DC/OS-cluster gebruiken om uw toepassing te implementeren

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel wordt uitgelegd hoe u Azure Container Registry gebruikt met een DC/OS-cluster. Als u ACR gebruikt, kunt u containerinstallatiekopieën privé opslaan en beheren. Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Azure Container Registry implementeren (indien nodig)
> * ACR-verificatie configureren in een DC/OS-cluster
> * Een installatiekopie naar Azure Container Registry uploaden
> * Een containerinstallatiekopie uitvoeren vanuit Azure Container Registry

U hebt een ACS DC/OS-cluster nodig om de stappen in deze zelfstudie te kunnen uitvoeren. Zo nodig kan [dit voorbeeldscript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) er een voor u maken.

Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt upgraden, raadpleegt u [Azure CLI installeren]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Azure Container Registry implementeren

Maak zo nodig een Azure Container Registry met de opdracht [az acr create](/cli/azure/acr#az-acr-create). 

In het volgende voorbeeld wordt een register met een willekeurig gegenereerde naam gemaakt. Het register wordt ook geconfigureerd met een beheerdersaccount met het argument `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Zodra het register is gemaakt, voert de Azure CLI gegevens uit zoals de volgende. Noteer `name` en `loginServer`. Deze worden gebruikt in latere stappen.

```output
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Haal de referenties voor het containerregister op met behulp van de opdracht [az acr credential show](/cli/azure/acr/credential). Vervang `--name` door degene die u hebt genoteerd in de vorige stap. Noteer een wachtwoord. Dit is vereist in een latere stap.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Zie [Inleiding tot privé-Docker-containerregisters](../../container-registry/container-registry-intro.md) voor meer informatie over Azure Container Registry. 

## <a name="manage-acr-authentication"></a>ACR-verificatie beheren

De conventionele manier om installatiekopieën uit een privéregister te pushen en te pullen is eerst te verifiëren bij het register. Hiervoor gebruikt u de opdracht `docker login` op elke client die toegang moet hebben tot het privéregister. Omdat een DC/OS-cluster veel knooppunten kan bevatten, die allemaal moeten worden geverifieerd bij de ACR, is het handig dit proces te automatiseren op elk knooppunt. 

### <a name="create-shared-storage"></a>Gedeelde opslag maken

Voor dit proces wordt een Azure-bestandsshare gebruikt die is gekoppeld op elk knooppunt in het cluster. Zie [Een bestandsshare in een DC/OS-cluster instellen](container-service-dcos-fileshare.md) als u nog geen gedeelde opslag hebt ingesteld.

### <a name="configure-acr-authentication"></a>ACR-verificatie configureren

Haal eerst de FQDN van de DC/OS-master op en sla deze op in een variabele.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Maak een SSH-verbinding met de master (of de eerste master) van uw op DC/OS gebaseerde cluster. Werk de gebruikersnaam bij als geen standaardwaarde is gebruikt bij het maken van het cluster.

```console
ssh azureuser@$FQDN
```

Voer de volgende opdracht uit om u aan te melden bij Azure Container Registry. Vervang `--username` door de naam van het containerregister en `--password` door een van de opgegeven wachtwoorden. Vervang het laatste argument *mycontainerregistry.azurecr.io* in het voorbeeld door de naam van de aanmeldingsserver van het containerregister. 

Deze opdracht slaat de verificatiewaarden lokaal op onder het pad `~/.docker`.

```console
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Maak een gecomprimeerd bestand met de verificatiewaarden van het containerregister.

```console
tar czf docker.tar.gz .docker
```

Kopieer dit bestand naar de gedeelde clusteropslag. In deze stap wordt het bestand beschikbaar gesteld op alle knooppunten van het DC/OS-cluster.

```console
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Installatiekopie naar ACR uploaden

Maak vanuit een ontwikkelcomputer of een ander systeem waarop Docker is geïnstalleerd een installatiekopie en upload deze naar Azure Container Registry.

Maak een container van de Ubuntu-installatiekopie.

```console
docker run ubuntu --name base-image
```

Leg de container nu vast in een nieuwe installatiekopie. De naam van de installatie kopie moet `loginServer` de naam van het container register bevatten met een `loginServer/imageName`indeling van.

```console
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Meld u aan bij Azure Container Registry. Vervang de naam door de naam van de aanmeldingsserver, vervang --username door de naam van het containerregister en vervang --password door een van de verstrekte wachtwoorden.

```console
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Ten slotte upload u de installatiekopie naar het ACR-register. In dit voorbeeld wordt een installatiekopie genaamd dcos-demo geüpload.

```console
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Een installatiekopie uitvoeren vanuit ACR

Als u een installatiekopie vanuit het ACR-register wilt gebruiken, maakt u een bestand genaamd *acrDemo.json* en kopieert u de volgende tekst naar dat bestand. Vervang de naam van de installatiekopie door de naam van de aanmeldingsserver van het containerregister en de naam van de installatiekopie, bijvoorbeeld `loginServer/imageName`. Noteer de eigenschap `uris`. Deze eigenschap bevat de locatie van het verificatiebestand van het containerregister, in dit geval de Azure-bestandsshare die is gekoppeld op elk knooppunt in het DC/OS-cluster.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Implementeer de toepassing met de DC/OC CLI.

```console
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u DC/OS geconfigureerd voor het gebruik van Azure Container Registry met de volgende taken:

> [!div class="checklist"]
> * Azure Container Registry implementeren (indien nodig)
> * ACR-verificatie configureren in een DC/OS-cluster
> * Een installatiekopie naar Azure Container Registry uploaden
> * Een containerinstallatiekopie uitvoeren vanuit Azure Container Registry
