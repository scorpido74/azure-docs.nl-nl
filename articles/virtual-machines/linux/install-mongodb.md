---
title: MongoDB installeren op een Linux-VM met de Azure CLI
description: Meer informatie over het installeren en configureren van MongoDB op een Linux virtuele machine met behulp van de Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944887"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>MongoDB installeren en configureren op een Linux VM

[MongoDB](https://www.mongodb.org) is een populaire open-source, high-performance NoSQL database. In dit artikel ziet u hoe u MongoDB installeert en configureert op een Linux-vm met de Azure CLI. Voorbeelden worden getoond met details hoe u:

* [Handmatig een basisMongoDB-exemplaar installeren en configureren](#manually-install-and-configure-mongodb-on-a-vm)
* [Een basis-MongoDB-exemplaar maken met behulp van een resourcemanagersjabloon](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Een complex MongoDB-geshard cluster met replicasets maken met behulp van een resourcemanagersjabloon](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>MongoDB handmatig installeren en configureren op een VM
MongoDB [biedt installatie-instructies](https://docs.mongodb.com/manual/administration/install-on-linux/) voor Linux-distro's, waaronder Red Hat / CentOS, SUSE, Ubuntu en Debian. In het volgende voorbeeld wordt een *CentOS* VM gemaakt. Om deze omgeving te maken, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index).

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVM gemaakt* met een gebruiker met de naam *azureuser* met SSH-verificatie voor openbare sleutels

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH naar de VM met `publicIpAddress` uw eigen gebruikersnaam en de lijst in de uitvoer van de vorige stap:

```bash
ssh azureuser@<publicIpAddress>
```

Als u de installatiebronnen voor MongoDB wilt toevoegen, maakt u als volgt een **yum** repository-bestand:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Open het Repo-bestand mongoDB voor bewerking, bijvoorbeeld met `vi` of `nano`. Voeg de volgende regels toe:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installeer MongoDB met **yum** als volgt:

```bash
sudo yum install -y mongodb-org
```

SELinux wordt standaard afgedwongen op CentOS-afbeeldingen die voorkomen dat u toegang krijgt tot MongoDB. Installeer beleidsbeheertools en configureer SELinux zodat MongoDB als volgt op de standaard TCP-poort 27017 kan werken:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Start de MongoDB-service als volgt:

```bash
sudo service mongod start
```

Controleer de MongoDB-installatie door `mongo` verbinding te maken met de lokale client:

```bash
mongo
```

Test nu de MongoDB-instantie door wat gegevens toe te voegen en vervolgens te zoeken:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Configureer indien gewenst MongoDB om automatisch te starten tijdens een herstart van het systeem:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Basis-MongoDB-instantie op CentOS maken met behulp van een sjabloon
U een basis-MongoDB-exemplaar maken op één CentOS-vm met de volgende Azure-snelstartsjabloon van GitHub. Deze sjabloon gebruikt de Custom Script-extensie voor Linux om een **yum-repository** toe te voegen aan uw nieuw gemaakte CentOS VM en vervolgens MongoDB te installeren.

* [BasisMongoDB-exemplaar op CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Om deze omgeving te maken, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index). Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementeer vervolgens de MongoDB-sjabloon bij [het maken van de implementatie van az-groepen.](/cli/azure/group/deployment) Voer desgevraagd uw eigen unieke waarden in voor *newStorageAccountName,* *dnsNameForPublicIP*en gebruikersnaam en wachtwoord voor beheerders:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Meld u aan bij de VM met het openbare DNS-adres van uw vm. U het openbare DNS-adres bekijken met [de AZ VM Show:](/cli/azure/vm)

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH naar uw VM met uw eigen gebruikersnaam en openbaar DNS-adres:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Controleer de MongoDB-installatie door `mongo` de lokale client als volgt aan te sluiten:

```bash
mongo
```

Test nu de instantie door wat gegevens toe te voegen en als volgt te zoeken:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Een complex MongoDB Sharded Cluster op CentOS maken met behulp van een sjabloon
U een complex MongoDB-geshard cluster maken met behulp van de volgende Azure quickstart-sjabloon van GitHub. Deze sjabloon volgt de best practices voor [sharding cluster van MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) om redundantie en hoge beschikbaarheid te bieden. De sjabloon maakt twee shards, met drie knooppunten in elke replicaset. Er wordt ook een config-serverreplicaset met drie knooppunten gemaakt, plus twee **mongos-routerservers** om consistentie te bieden aan toepassingen van over de shards.

* [MongoDB Sharding Cluster op CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Het implementeren van dit complexe MongoDB sharded cluster vereist meer dan 20 cores, wat meestal het standaard aantal cores per regio is voor een abonnement. Open een Azure-ondersteuningsaanvraag om het aantal cores te verhogen.

Om deze omgeving te maken, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index). Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementeer vervolgens de MongoDB-sjabloon bij [het maken van de implementatie van az-groepen.](/cli/azure/group/deployment) Definieer uw eigen resourcenamen en -formaten waar nodig, zoals voor *mongoAdminUsername,* *sizeOfDataDiskInGB*en *configNodeVmSize:*

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Deze implementatie kan meer dan een uur duren om alle VM-exemplaren te implementeren en te configureren. De `--no-wait` vlag wordt aan het einde van de vorige opdracht gebruikt om het besturingselement terug te sturen naar de opdrachtprompt zodra de sjabloonimplementatie is geaccepteerd door het Azure-platform. Vervolgens u de implementatiestatus met [de inzet van az-groepen weergeven.](/cli/azure/group/deployment) In het volgende voorbeeld wordt de status voor de implementatie *van myMongoDBCluster* in de brongroep *myResourceGroup* weergegeven:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Volgende stappen
In deze voorbeelden maakt u lokaal verbinding met de MongoDB-instantie via de VM. Als u verbinding wilt maken met de MongoDB-instantie vanuit een andere virtuele machine of netwerk, moet u ervoor zorgen dat de juiste [regels voor netwerkbeveiligingsgroepen worden gemaakt.](nsg-quickstart.md)

Deze voorbeelden implementeren de kern MongoDB-omgeving voor ontwikkelingsdoeleinden. Pas de vereiste beveiligingsconfiguratieopties toe voor uw omgeving. Zie voor meer informatie de [mongoDB-beveiligingsdocumenten](https://docs.mongodb.com/manual/security/).

Zie het overzicht Azure Resource [Manager](../../azure-resource-manager/management/overview.md)voor meer informatie over het maken van gebruikssjablonen.

De Azure Resource Manager-sjablonen gebruiken de aangepaste scriptextensie om scripts op uw VM's te downloaden en uit te voeren. Zie [De Azure Custom Script-extensie gebruiken met virtuele linuxmachines](extensions-customscript.md)voor meer informatie.

