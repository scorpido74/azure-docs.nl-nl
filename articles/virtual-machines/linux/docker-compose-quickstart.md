---
title: Docker Compose gebruiken
description: Docker en Compose installeren en gebruiken op virtuele Linux-machines met de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970309"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Aan de slag met Docker en Compose om een multicontainertoepassing in Azure te definiëren en uit te voeren
Met [Compose](https://github.com/docker/compose)gebruikt u een eenvoudig tekstbestand om een toepassing te definiëren die bestaat uit meerdere Docker-containers. Vervolgens draait u uw toepassing in één opdracht die alles doet om uw gedefinieerde omgeving te implementeren. In dit artikel ziet u bijvoorbeeld hoe u snel een WordPress-blog instellen met een backend MariaDB SQL-database op een Ubuntu VM. U Compose ook gebruiken om complexere toepassingen in te stellen.

Dit artikel is voor het laatst getest op 2/14/2019 met behulp van de [Azure Cloud Shell](https://shell.azure.com/bash) en de Azure [CLI-versie](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Docker-host maken met Azure CLI
Installeer de nieuwste [Azure CLI](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met [az-aanmelding.](/cli/azure/reference-index)

Maak eerst een resourcegroep voor uw Docker-omgeving met [de AZ-groep om te maken.](/cli/azure/group) In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Maak een bestand met de naam *cloud-init.txt* en plak de volgende configuratie. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. 

```yaml
#include https://get.docker.com
```

Maak een virtuele machine met [az vm create](/cli/azure/vm#az-vm-create). Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. In het volgende voorbeeld wordt een VM met de naam *myDockerVM* gesneualen en wordt poort 80 geopend voor webverkeer.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. 

                 

## <a name="install-compose"></a>Compose installeren


SSH naar uw nieuwe Docker host VM. Geef uw eigen IP-adres op.

```bash
ssh azureuser@10.10.111.11
```

Installeer Compose op de VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Een docker-compose.yml-configuratiebestand maken
Maak `docker-compose.yml` een configuratiebestand om de Docker-containers te definiëren die op de VM moeten worden uitgevoerd. Het bestand geeft de afbeelding op die op elke container moet worden uitgevoerd, noodzakelijke omgevingsvariabelen en afhankelijkheden, poorten en de koppelingen tussen containers. Zie [Bestandsverwijzing samenstellen](https://docs.docker.com/compose/compose-file/)voor meer informatie over de syntaxis van yml-bestanden .

Maak een *docker-compose.yml-bestand.* Gebruik uw favoriete teksteditor om bepaalde gegevens aan het bestand toe te voegen. In het volgende voorbeeld wordt `sensible-editor` het bestand gemaakt met een prompt om een editor te kiezen die u wilt gebruiken.

```bash
sensible-editor docker-compose.yml
```

Plak het volgende voorbeeld in het docker-bestand. Deze configuratie maakt gebruik van afbeeldingen uit het [DockerHub-register](https://registry.hub.docker.com/_/wordpress/) om WordPress (het open source-blogging- en contentmanagementsysteem) en een gekoppelde backend MariaDB SQL-database te installeren. Voer uw eigen *MYSQL_ROOT_PASSWORD*in.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Start de containers met Compose
Voer in dezelfde map als uw *docker-compose.yml-bestand* de volgende opdracht uit (afhankelijk van uw omgeving moet u mogelijk uitvoeren `docker-compose` met): `sudo`

```bash
sudo docker-compose up -d
```

Met deze opdracht worden de Docker-containers gestart die zijn opgegeven in *docker-compose.yml*. Het duurt een minuut of twee voor deze stap te voltooien. U ziet uitvoer vergelijkbaar met de volgende:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Als u wilt controleren of `sudo docker-compose ps`de containers zijn aangegaan, typt u . Dit ziet er ongeveer als volgt uit:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

U nu rechtstreeks verbinding maken met WordPress op de VM op poort 80. Open een webbrowser en voer de IP-adresnaam van uw vm in. U ziet nu het startscherm van WordPress, waar u de installatie voltooien en aan de slag gaan met de toepassing.

![WordPress startscherm](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Volgende stappen
* Bekijk de [opdrachtregelverwijzing](https://docs.docker.com/compose/reference/) en [gebruikershandleiding](https://docs.docker.com/compose/) opstellen voor meer voorbeelden van het bouwen en implementeren van apps met meerdere containers.
* Gebruik een Azure Resource Manager-sjabloon, uw eigen of een sjabloon die vanuit de [community](https://azure.microsoft.com/documentation/templates/)is bijgedragen, om een Azure VM met Docker te implementeren en een toepassing die is ingesteld met Compose. De [WordPress-blog implementeren met Docker-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) maakt bijvoorbeeld gebruik van Docker en Compose om WordPress snel te implementeren met een MySQL-backend op een Ubuntu-vm.
* Probeer Docker Compose te integreren met een Docker Swarm-cluster. Zie [Componeren gebruiken met Zwerm](https://docs.docker.com/compose/swarm/) gebruiken voor scenario's.

