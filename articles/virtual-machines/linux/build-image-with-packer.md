---
title: Linux Azure VM-afbeeldingen maken met Packer
description: Meer informatie over het gebruik van Packer om afbeeldingen van virtuele Linux-machines in Azure te maken
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 3aec50b8c8f2033b7340bde15ea7670c1a0b6bb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534216"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Packer gebruiken om Virtuele machineafbeeldingen van Linux te maken in Azure
Elke virtuele machine (VM) in Azure wordt gemaakt op basis van een afbeelding die de Linux-distributie en OS-versie definieert. Afbeeldingen kunnen vooraf geïnstalleerde toepassingen en configuraties bevatten. De Azure Marketplace biedt veel afbeeldingen van eerste en derde partijen voor de meest voorkomende distributies en toepassingsomgevingen, of u uw eigen aangepaste afbeeldingen maken die zijn afgestemd op uw behoeften. In dit artikel wordt beschreven hoe u het open source-hulpprogramma [Packer](https://www.packer.io/) gebruiken om aangepaste afbeeldingen in Azure te definiëren en te bouwen.

> [!NOTE]
> Azure heeft nu een service, Azure Image Builder (preview), voor het definiëren en maken van uw eigen aangepaste afbeeldingen. Azure Image Builder is gebouwd op Packer, zodat u er zelfs uw bestaande Packer shell provisioner-scripts mee gebruiken. Zie [Een Linux-vm maken met Azure Image Builder](image-builder.md)om aan de slag te gaan met Azure Image Builder.


## <a name="create-azure-resource-group"></a>Azure-brongroep maken
Tijdens het bouwproces maakt Packer tijdelijke Azure-resources terwijl het de bron-VM bouwt. Als u die bron-VM wilt vastleggen voor gebruik als afbeelding, moet u een brongroep definiëren. De uitvoer van het Packer-buildproces wordt opgeslagen in deze resourcegroep.

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-referenties maken
Packer verifieert met Azure met behulp van een serviceprincipal. Een Azure-serviceprincipal is een beveiligingsidentiteit die u gebruiken met apps, services en automatiseringshulpprogramma's zoals Packer. U beheert en definieert de machtigingen voor welke bewerkingen de serviceprincipal in Azure kan uitvoeren.

Maak een serviceprincipal met [az ad sp create-for-rbac](/cli/azure/ad/sp) en voer de referenties uit die Packer nodig heeft:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Een voorbeeld van de uitvoer van de voorgaande opdrachten is als volgt:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Als u wilt verifiëren voor Azure, moet u ook uw Azure-abonnements-id met [de AZ-account tonen:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

U gebruikt de uitvoer van deze twee opdrachten in de volgende stap.


## <a name="define-packer-template"></a>Packer-sjabloon definiëren
Als u afbeeldingen wilt maken, maakt u een sjabloon als JSON-bestand. In de sjabloon definieert u bouwers en provisioneerders die het eigenlijke bouwproces uitvoeren. Packer heeft een [provisionervoor Azure](https://www.packer.io/docs/builders/azure.html) waarmee u Azure-resources definiëren, zoals de hoofdreferenties van de service die in de vorige stap zijn gemaakt.

Maak een bestand met de naam *ubuntu.json* en plak de volgende inhoud. Voer uw eigen waarden in voor het volgende:

| Parameter                           | Waar te verkrijgen |
|-------------------------------------|----------------------------------------------------|
| *Client_id*                         | Eerste regel van `az ad sp` output van opdracht maken - *appId* |
| *client_secret*                     | Tweede regel van `az ad sp` uitvoer van opdracht maken - *wachtwoord* |
| *tenant_id*                         | Derde regel van `az ad sp` uitvoer van opdracht maken - *huurder* |
| *subscription_id*                   | Uitvoer `az account show` van opdracht |
| *managed_image_resource_group_name* | Naam van de resourcegroep die u in de eerste stap hebt gemaakt |
| *managed_image_name*                | Naam voor de beheerde schijfafbeelding die is gemaakt |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Deze sjabloon bouwt een Ubuntu 16.04 LTS-afbeelding, installeert NGINX en deinds vervolgens de VM.

> [!NOTE]
> Als u deze sjabloon uitbreidt om gebruikersreferenties in te richten, past u `-deprovision` de `deprovision+user`opdracht provisionering aan die de Azure-agent deprovisioneert om te lezen in plaats van .
> De `+user` vlag verwijdert alle gebruikersaccounts van de bron-VM.


## <a name="build-packer-image"></a>Packer-afbeelding bouwen
Als Packer nog niet op uw lokale machine is geïnstalleerd, [volgt u de installatie-instructies van Packer.](https://www.packer.io/docs/install/index.html)

Bouw de afbeelding op door het sjabloonbestand Packer als volgt op te geven:

```bash
./packer build ubuntu.json
```

Een voorbeeld van de uitvoer van de voorgaande opdrachten is als volgt:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Het duurt een paar minuten voordat Packer de VM bouwt, de provisioners uitvoert en de implementatie opruimt.


## <a name="create-vm-from-azure-image"></a>VM maken vanuit Azure Image
U nu een VM maken op basis van uw afbeelding met [az vm maken.](/cli/azure/vm) Geef de afbeelding op `--image` die u met de parameter hebt gemaakt. In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt van *myPackerImage* en genereert ssh-sleutels als deze nog niet bestaan:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Als u VM's wilt maken in een andere resourcegroep of -regio dan uw Packer-afbeelding, geeft u de afbeeldings-id op in plaats van de naam van de afbeelding. U de afbeelding-ID met [az image show](/cli/azure/image#az-image-show)verkrijgen.

Het duurt een paar minuten om de VM te maken. Zodra de VM is gemaakt, `publicIpAddress` neemt u nota van de weergave door de Azure CLI. Dit adres wordt gebruikt om toegang te krijgen tot de NGINX-site via een webbrowser.

Open poort 80 via internet met [az vm open-port](/cli/azure/vm) zodat beveiligd webverkeer uw virtuele machine kan bereiken:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test VM en NGINX
Nu kunt u een webbrowser openen en `http://publicIpAddress` in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. De standaard NGINX-pagina wordt weergegeven zoals in het volgende voorbeeld:

![Standaardsite van NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Volgende stappen
U ook bestaande Packer provisioner-scripts gebruiken met [Azure Image Builder.](image-builder.md)
