---
title: Docker Machine gebruiken om Linux-hosts te maken
description: Beschrijft hoe u Docker Machine gebruiken om Docker-hosts in Azure te maken.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968804"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Docker Machine gebruiken om hosts in Azure te maken
In dit artikel wordt beschreven hoe u [Docker Machine gebruikt](https://docs.docker.com/machine/) om hosts in Azure te maken. De `docker-machine` opdracht maakt een Virtuele Linux-machine (VM) in Azure en installeert Docker. Vervolgens u uw Docker-hosts in Azure beheren met dezelfde lokale hulpprogramma's en werkstromen. Als u docker-machine wilt gebruiken in Windows 10, moet u Linux bash gebruiken.

## <a name="create-vms-with-docker-machine"></a>VM's maken met Docker Machine
Ten eerste, het verkrijgen van uw Azure-abonnement ID met [AZ-account te tonen](/cli/azure/account) als volgt:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

U maakt Docker-hostVM's in Azure met `docker-machine create` azure als stuurprogramma. *azure* Zie de documentatie [van Docker Azure Driver](https://docs.docker.com/machine/drivers/azure/) voor meer informatie

In het volgende voorbeeld wordt een VM met de naam *myVM*gemaakt op basis van het 'Standard D2 v2'-abonnement, wordt een gebruikersaccount met de naam *azureuser*gemaakt en wordt poort *80* geopend op de host-VM. Volg alle aanwijzingen om in te loggen op uw Azure-account en geef Docker Machine machtigingen om resources te maken en te beheren.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

De uitvoer lijkt op het volgende voorbeeld:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>De shell Docker configureren
Als u verbinding wilt maken met uw Docker-host in Azure, definieert u de juiste verbindingsinstellingen. Zoals aan het einde van de uitvoer is opgemerkt, bekijkt u de verbindingsgegevens voor uw Docker-host als volgt: 

```bash
docker-machine env myvm
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Als u de verbindingsinstellingen wilt definiëren, kunt`eval $(docker-machine env myvm)`u de voorgestelde configuratieopdracht () uitvoeren of de omgevingsvariabelen handmatig instellen. 

## <a name="run-a-container"></a>Een container uitvoeren
Als u een container in actie wilt zien, u een eenvoudige NGINX-webserver uitvoeren. Maak een `docker run` container met en stel poort 80 voor webverkeer als volgt bloot:

```bash
docker run -d -p 80:80 --restart=always nginx
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Bekijk lopende `docker ps`containers met . In het volgende voorbeeld wordt weergegeven dat de NGINX-container wordt uitgevoerd met poort 80:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Test de container
Verkrijg het openbare IP-adres van Docker host als volgt:


```bash
docker-machine ip myvm
```

Als u de container in actie wilt zien, opent u een webbrowser en voert u het openbare IP-adres in dat is vermeld in de uitvoer van de vorige opdracht:

![Lopende ngnix container](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Volgende stappen
Zie Aan de slag met [Docker en Compose in Azure](docker-compose-quickstart.md)voor voorbeelden over het gebruik van Docker Compose.
