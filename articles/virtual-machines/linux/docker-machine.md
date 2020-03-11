---
title: Docker-machine gebruiken voor het maken van Linux-hosts
description: Hierin wordt beschreven hoe u docker machine gebruikt om docker-hosts in azure te maken.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968804"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Docker machine gebruiken om hosts in azure te maken
In dit artikel wordt beschreven hoe u [docker machine](https://docs.docker.com/machine/) gebruikt voor het maken van hosts in Azure. Met de `docker-machine` opdracht wordt een virtuele Linux-machine (VM) in azure gemaakt en vervolgens docker geïnstalleerd. U kunt uw docker-hosts vervolgens beheren in azure met behulp van dezelfde lokale hulpprogram ma's en werk stromen. Als u docker-machine in Windows 10 wilt gebruiken, moet u Linux bash gebruiken.

## <a name="create-vms-with-docker-machine"></a>Vm's maken met docker-machine
Verschaf eerst uw Azure-abonnements-ID met [AZ account show](/cli/azure/account) :

```azurecli
sub=$(az account show --query "id" -o tsv)
```

U maakt docker host-Vm's in azure met `docker-machine create` door *Azure* op te geven als het stuur programma. Zie de [documentatie van docker Azure-stuur programma](https://docs.docker.com/machine/drivers/azure/) voor meer informatie.

In het volgende voor beeld wordt een virtuele machine gemaakt met de naam *myVM*, op basis van het plan Standard D2 v2, wordt een gebruikers account met de naam *azureuser*gemaakt en wordt poort *80* geopend op de host-VM. Volg de aanwijzingen om u aan te melden bij uw Azure-account en docker-computer machtigingen te verlenen voor het maken en beheren van resources.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

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

## <a name="configure-your-docker-shell"></a>Uw docker-shell configureren
Als u verbinding wilt maken met uw docker-host in azure, definieert u de juiste verbindings instellingen. Zoals vermeld aan het einde van de uitvoer, kunt u als volgt de verbindings gegevens voor uw docker-host weer geven: 

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

Als u de verbindings instellingen wilt definiëren, kunt u de aanbevolen configuratie opdracht (`eval $(docker-machine env myvm)`) uitvoeren, of u kunt de omgevings variabelen hand matig instellen. 

## <a name="run-a-container"></a>Een container uitvoeren
Als u een container in actie wilt zien, kunt u een eenvoudige NGINX-webserver uitvoeren. Maak een container met `docker run` en open poort 80 voor webverkeer als volgt:

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

Actieve containers weer geven met `docker ps`. In de volgende voorbeeld uitvoer ziet u de NGINX-container met poort 80 beschikbaar:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>De container testen
U krijgt als volgt het open bare IP-adres van de docker-host:


```bash
docker-machine ip myvm
```

Als u de container in actie wilt zien, opent u een webbrowser en voert u het open bare IP-adres in dat wordt vermeld in de uitvoer van de voor gaande opdracht:

![Ngnix-container uitvoeren](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Volgende stappen
Voor voor beelden van het gebruik van docker opstellen, Zie [aan de slag met docker en samen stellen in azure](docker-compose-quickstart.md).
